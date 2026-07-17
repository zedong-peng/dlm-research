# Deadline Paths for Training-Free Masked Diffusion Decoding

**Method.** Deadline Path Projection (DPP)

## Motivation
A masked diffusion language model (DLM) writes many token positions in parallel by repeatedly replacing masks with guesses. Under a strict limit of B model calls, every call has competing jobs: reveal more positions, undo weak earlier guesses, or gather better context for the next decision. Existing decoders control these jobs separately, so a sensible repair can consume the call that was needed to finish the sequence.

Saber can reveal tokens faster and roll back low-confidence choices, but its reveal threshold and rollback quota do not come from calls remaining. MEDAL spends search on the initial part of decoding because searching the whole trajectory is too expensive. The Attention-Discounted Adaptive Sampler (ADAS) chooses a better set of currently masked positions but inherits stopping and remasking rules from another sampler. Recent open LLaDA-8B and Dream-7B models provide the token probabilities needed to compare new guesses with old commitments in one decision.

Deadline Path Projection (DPP) fixes how many positions must be committed after each call, then chooses that many positions from both the masked and already committed groups. A weak old token may be remasked only when another masked proposal takes its place, so repair does not delay the required net progress. This produces zero masks after exactly B calls. The central empirical question remains whether those exchanges improve final Pass@1; pinning all old commitments so $r_{b}=0$ tests that mechanism while keeping the same completion path.

## Method
### Background

1. Create one immutable run record with the model checkpoint, tokenizer, target length L, model limit $L_{max},$ number of allowed model calls B, and optional time target T. If B is supplied, require a positive integer. If T is supplied, keep the model, software stack, batch size of one, and length L fixed; run five unmeasured warm-ups, synchronize the device around each measured model-forward-plus-sort operation on 32 held-out prompts, take the largest measured duration as $\tau _{max},$ and apply the stated floor rule to obtain B. Require $B \ge  1$ and $1 \le  L \le  L_{max}.$ Fix how each prompt receives L and how an early End of Sequence (EOS) token is treated before decoding. 【author decision: choose a reference-free rule for L and decide whether tokens after EOS remain, become padding, or are ignored only when text is recovered】 Create an L-position token array filled with the model's mask identifier, a same-size Boolean committed array filled with false, an empty committed-position set $U_{B},$ the all-mask canvas $x_{B},$ and set b to B.
   - _Why:_ Every later choice needs a finite number of calls remaining, and infeasible wall-clock targets should fail before decoding starts.
2. Build the current canvas as a batch-one $input_{ids}$ tensor. Put the current token identifier at every committed position, put $\mathit{mask\_token\_id}$ everywhere else, and mark all L positions as valid in the attention mask. Load LLaDA-8B or Dream-7B in evaluation mode, turn off gradient recording, and do not change its weights. Use the checkpoint's reference inference wrapper for required special-token or diffusion-time inputs; if the model requires a time or noise value, use the reference sampler's value for the current fraction of masked positions and do not pass B or b as extra conditioning. Call the model exactly once. Require a finite output table with one row per position and one column per vocabulary item, convert each row to a float32 probability distribution $p_{i},$ and pass this one result to the next step without another model call.
   - _Why:_ The method uses only evidence from the allowed frozen-model call, without a second scorer or probe.
3. For every prespecified combination of prompt, checkpoint, length rule, and B, run Deadline Path Projection (DPP), a pinned control with no exchanges, and the official Saber, Attention-Discounted Adaptive Sampler (ADAS), Fast-dLLM, and Top-k implementations. Give all methods the same tokenizer, checkpoint weights, initial canvas, and at most B model calls. In the pinned control, keep every previously committed position and add only enough highest-supported masked proposals to reach the next required committed count, using the same tie rule; this makes $r_{b}$ zero while leaving the completion path unchanged. Use LLaDA-8B on HumanEval at B values 16, 32, and 64 as the primary evaluation. Keep each baseline's published defaults except for the shared B and L rules, stop after B calls, and count an output with any remaining mask as an incomplete failure instead of giving extra calls. For each run, store the method, checkpoint, prompt identifier, B, L, exchange count at every step, final number of masks, decoded tokens, correctness, and synchronized time from the initialized canvas through recovered text. Measure HumanEval Pass@1 with its official execution harness and use exact match where that is the task's official metric. Summarize completion, quality, and latency for every method and B, and obtain paired prompt-level confidence intervals with 10,000 bootstrap resamples.
   - _Why:_ Equal call caps test the deadline claim, and $r_{b}=0$ isolates whether exchanges rather than the fixed completion path create any quality gain.

### M1: Deadline path
*Convert calls remaining into an exact committed-set cardinality and preserve the zero-mask terminal invariant.*

4. At the beginning of each model call, check that b is an integer from 1 through B. Use exact integer arithmetic to evaluate the linked ceiling rule for how many masks must remain after this call, so floating-point rounding cannot change the count. Compute the complementary number of committed positions, $K_{b-1},$ and check that both counts lie from 0 through L and that the committed count never decreases. Save b, b-1, the required mask count $m_{b-1},$ and the required committed count $K_{b-1}$ in one transition record. Every later operation for this call reads that record; no confidence threshold is allowed to change its cardinality.

*The deadline path fixes the required mask and commitment counts after the current forward.*
$$ m_{b-1}=\left\lceil \frac{L(b-1)}{B} \right\rceil,\qquad K_{b-1}=L-m_{b-1} \tag{1} $$

   - _Why:_ This path reserves enough net progress to reach $m_{0}=0$ even when some old commitments are replaced.
5. Only after all step 8 (Create the next canvas in $a fresh\dots )$ checks pass, replace the current canvas and committed set with $x_{b-1}$ and $U_{b-1},$ then reduce b by one exactly once. If b is still positive, return to step 4 (At the beginning of each model call). If b is zero, check that $U_{0}$ contains all L positions, that no token equals $\mathit{mask\_token\_id},$ and that the trace contains exactly B model calls. Recover text with the same tokenizer and the length and End of Sequence rule fixed in step 1 (Create one immutable run $record\dots ).$ Return the complete token sequence, recovered text, and transition trace. Stop with an error if any invariant fails; never silently invent or discard a position.

*Maintaining the path invariant yields a fully committed canvas at the deadline.*
$$ |\{i:x_{b-1,i}=\mathtt{[MASK]}\}|=m_{b-1},\qquad m_0=0 \tag{2} $$

   - _Why:_ Maintaining the mask-count invariant at every transition guarantees zero masks after exactly B forwards.

### M2: Cross-status projection
*Score new proposals and old commitments together, then perform quota-free commit-repair exchanges inside the required set size.*

6. Turn the probability table from the one model call into one record per position. At a masked position, choose the most probable token $v_{i}$; if vocabulary items tie, choose the smaller token identifier, and record its log probability as support $s_{i}.$ At a committed position, keep the current token $z_{i}$ and read that token's log probability from the same model output. Store four fields for every position: position index, whether it was masked or committed, the proposed token identifier, and support. A zero probability may become negative infinity, but any other non-finite value is an error. Keep the records in position order so the next step has a deterministic input.

*One forward supplies comparable support for a masked proposal or the token already committed at each position.*
$$ s_i=\begin{cases}\log p_i(v_i), & i\notin U_b,\ v_i=\arg\max_v p_i(v),\\ \log p_i(z_i), & i\in U_b.\end{cases} \tag{3} $$

   - _Why:_ Putting new guesses and old commitments on one support scale allows progress and repair to compete directly.
7. Sort all L position records once, first by support $s_{i}$ from largest to smallest and then, for equal support, by position index from smallest to largest. Take the first $K_{b-1}$ records. Taking zero records yields an empty set, and taking L records yields every position. Call the selected position set $U_{b-1},$ convert it to a length-L Boolean selection array, and check that it contains exactly $K_{b-1}$ distinct positions. Do not apply a separate quota for old or new positions, another confidence cutoff, or a second ranking pass.

*The next committed set is the fixed-cardinality top-K selection across both statuses.*
$$ U_{b-1}=\operatorname{TopK}_{i\in\{1,\ldots,L\}}(s_i;K_{b-1}) \tag{4} $$

   - _Why:_ The deadline fixes only how many positions must remain committed; current model evidence decides which positions they are.
8. Create the next canvas in a fresh length-L array initially filled with $\mathit{mask\_token\_id}.$ For each position in $U_{b-1},$ copy its current token $z_{i}$ if it was already committed; otherwise write the new proposal $v_{i}$ saved in step 6 (Turn the probability table from $the\dots ).$ Leave every position outside $U_{b-1}$ masked, which removes any old commitment that was not selected. Define exchange count $r_{b}$ as the number of old committed positions missing from $U_{b-1}.$ Also save lists of removed, newly committed, and retained positions, plus the net increase in commitments. Before replacing the old state, check that every selected position contains a real token, every unselected position contains $\mathit{mask\_token\_id},$ the committed Boolean array exactly matches $U_{b-1},$ there are $K_{b-1}$ committed positions, and exactly $m_{b-1}$ masks remain. Publish the new canvas, committed set, exchange count, and trace together.

*Each discarded commitment is exchanged for one extra masked proposal in addition to mandatory net progress.*
$$ r_b=|U_b\setminus U_{b-1}|,\qquad |U_{b-1}\setminus U_b|=r_b+(K_{b-1}-K_b) \tag{5} $$

   - _Why:_ Every repaired old token is replaced inside the same fixed-size set, so repair is budget-neutral and directly measurable.

