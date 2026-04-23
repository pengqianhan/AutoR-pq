---
Author: ChatGPT
---


# Next-Step Research Directions for Verifiable LLM Reasoning with Symbolic Solvers and Chain-of-Thought Faithfulness

## Section A. Executive summary of the field

Large language models (LLMs) can solve multi-step reasoning tasks with Chain-of-Thought (CoT), but correctness of the final answer frequently fails to imply correctness (or even consistency) of the intermediate reasoning. Two of the anchor papers explicitly quantify this gap: “Pushing the Boundaries of Natural Reasoning: Interleaved Bonus from Formal-Logic Verification in Language Models” reports substantial step-level inconsistency even in chains that end with the correct answer, motivating a shift from outcome-only supervision to step-level formal checks. citeturn3view1 “VERICOT: Neuro-Symbolic Chain-of-Thought Validation via Logical Consistency Checks” similarly treats CoTs as *objects to be audited*, not just explanations, and builds a verification pipeline that distinguishes “entailed,” “contradicted,” and “consistent-but-not-entailed” steps, with explicit premise reconstruction. citeturn31view0turn30view2

Across the recent literature, the dominant trajectory is moving from **translation-only neuro-symbolic pipelines** (LLM → formalisation → solver) toward **closed-loop systems** where solver feedback is used *during generation*, *during training*, or both. Logic-LM and LINC are representative of translation-plus-solver paradigms that improve logical reasoning by offloading deduction to symbolic provers and (in different ways) mitigating formalisation errors (self-refinement in Logic-LM; majority voting / multi-sample formalisation in LINC). citeturn33search2turn34search35 VeriCoT extends this by verifying *CoT steps* via SMT entailment/consistency checks and by introducing explicit premise grounding plus an LLM-as-judge stage for premise acceptability. citeturn31view0turn30view2 The “Interleaved Bonus” paper pushes further by proposing a two-stage **verification-guided SFT + policy optimisation (GRPO)** pipeline, coupled with *execution-based validation* to reduce autoformalisation noise—explicitly positioning post-hoc filtering as insufficient because it cannot prevent error propagation mid-chain. citeturn28view0turn29view0turn27view3

Three bottlenecks repeatedly surface and remain important even as base models improve:

1. **Autoformalisation noise is system-defining, not a minor implementation detail.** Both “Interleaved Bonus” and VeriCoT explicitly add machinery (execution validation; vocabulary-extension loops; premise generation) to stabilise feedback when formalisation is imperfect. citeturn28view1turn31view2turn30view1  
2. **Verification must be paired with a repair policy.** VeriCoT includes inference-time self-reflection triggered by verification failure, and the “Interleaved Bonus” framework emphasises real-time rectification with solver feedback; but neither fully treats “repair” as a learnable, optimisable policy class. citeturn30view3turn3view1  
3. **Evaluation is drifting from “accuracy-only” toward multi-objective measurement of *validity, coverage, and cost*.** VeriCoT introduces pass rate, precision, and Verified Correct Answer Rate (VCAR). “Interleaved Bonus” adds explicit tool-call limits and discusses balancing “formalism vs computational fluency.” citeturn30view1turn27view3turn28view2  

The 10 final research ideas in this report therefore prioritise *mechanism-level* contributions: learnable verification routing, counterexample-driven repair, premise-grounded rewards, noise-aware verification, solver-generated process supervision, distillation of solver invariants, verification-aware decomposition planning, faithfulness metrics, validity–coverage Pareto optimisation, and verification-calibrated abstention.

## Section B. Comparative analysis of the 3 anchor papers

### Anchor paper 1: “Beyond Translation: A Decomposed Collaborative Reasoning Framework Harnessing LLMs and Symbolic Solvers”

**Note on access:** the public forum page on entity["organization","OpenReview","peer review platform"] provides the abstract and metadata, but the PDF could not be retrieved in this environment (the “Download PDF” link resolves to a static asset). The analysis below is therefore limited to what is explicitly stated on the forum page. citeturn15view0

1. **Problem setting:** neuro-symbolic reasoning where LLM+solver systems are “underutilising” LLMs by treating them as text-to-symbol converters and suffer hallucinations and degradation on complex multi-step reasoning. citeturn15view0  
2. **Core hypothesis:** deeper integration requires *decomposing* the reasoning process so the LLM contributes structure/outline while solvers handle deduction. citeturn15view0  
3. **Main technical mechanism:** a two-step decomposition: (i) LLM produces an outline; (ii) LLM decomposes into sub-problems and generates formal conditions per sub-problem, which are then solved by a symbolic solver. citeturn15view0  
4. **Where verification is inserted:** not specified on the forum page beyond “offloading rigorous deduction to the symbolic solver.” citeturn15view0  
5. **Formal object:** “formal conditions” (keywords include First Order Logic), but exact representation language, solver type, and constraint format are not specified on the forum page. citeturn15view0  
6. **LLM’s role:** planner/outliner + decomposer + formal-condition generator. citeturn15view0  
7. **Symbolic system’s role:** rigorous deduction / solving sub-problems to mitigate hallucinations. citeturn15view0  
8. **Training setup:** not stated (unclear whether prompting-only or post-training). citeturn15view0  
9. **Benchmark coverage:** not stated on the forum page. citeturn15view0  
10. **Main strengths (implied):** emphasises decomposition as the interface, explicitly arguing that “beyond translation” better leverages LLM strengths in “contextual exploration and problem structuring.” citeturn15view0  
11. **Main weaknesses (implied):** missing detail about verification interface, training, solver language, and empirical scope prevents assessing generality and failure modes. citeturn15view0  
12. **Future opportunities (implicit):** learnable decomposition policies; robustness to decomposition errors; integration of solver feedback into decomposition and/or training. citeturn15view0  

### Anchor paper 2: “Pushing the Boundaries of Natural Reasoning: Interleaved Bonus from Formal-Logic Verification in Language Models”

1. **Problem setting:** reasoning LLMs exhibit logical inconsistency, hallucinations, and reward hacking; outcome-only optimisation enables “correct answer via invalid pathway.” citeturn3view1turn29view0  
2. **Core hypothesis:** formal verification can provide scalable, step-level feedback that improves reasoning across domains, especially when verification is *interleaved* with generation and used in training (SFT + RL). citeturn3view1turn28view0turn29view0  
3. **Main technical mechanism:** two-stage pipeline:  
   - **FLV-SFT:** hierarchical data synthesis with CoT generation, decomposition into modules, formal proof synthesis, and **execution-based validation** for proof fidelity. citeturn28view0turn28view1  
   - **FLV-RL:** policy optimisation with **GRPO** and a hierarchical reward prioritising format integrity → structural compliance → logical correctness, explicitly to prevent reward hacking/pathologies. citeturn27view2turn27view3turn29view0  
4. **Where verification is inserted:** both training-time and inference-time. The framework is explicitly positioned as “dynamically interleaving” formal verification into the generation process (not only post-hoc filtering). citeturn3view1turn28view0  
5. **Formal object:** solver-checkable constraints via SMT-style verification and executable tool calls (Python calculation and Z3 logical verification are explicitly described in prompts/appendices). citeturn27view0turn27view1  
6. **LLM’s role:** generator of reasoning chains; decomposer into logic modules; translator/producer of solver-checkable constraints; self-corrector guided by solver feedback. citeturn28view0turn27view1  
7. **Symbolic system’s role:** SMT-based verification delivering satisfiable/unsat feedback, counterexamples, and execution outputs; used as dense supervision and correction signal. citeturn27view1turn28view0  
8. **Training setup:** SFT + GRPO RL; base models Qwen2.5-7B/14B; uses teacher and judge models for data distillation and correctness judging (e.g., GPT-4o as judge; other models for synthesis). citeturn29view0  
9. **Benchmark coverage:** six benchmarks across logical, mathematical, and general reasoning: KOR-Bench, BBH, MATH-500, AIME 2024, GPQA-Diamond, TheoremQA. citeturn29view0  
10. **Main strengths:** multi-domain improvements; explicit treatment of verification overhead and tool-call limits; introduces structured reward to mitigate RL pathologies. citeturn29view0turn27view3turn28view2  
11. **Main weaknesses (explicit):** formalisation challenges for ambiguous/commonsense-heavy tasks; risk of incorrect verification feedback due to mapping errors; verification overhead. citeturn28view2turn29view0  
12. **Future opportunities (explicit/implicit):** more robust autoformalisation; adaptive verification rather than rigid checkpoints; better generalisation to open-ended reasoning; improved cost–accuracy tradeoffs. citeturn28view2turn28view3  

### Anchor paper 3: “VERICOT: Neuro-Symbolic Chain-of-Thought Validation via Logical Consistency Checks”

1. **Problem setting:** LLMs generate CoTs that may be logically flawed even when answers are correct; need verifiable reasoning for trust, especially in high-stakes domains. citeturn3view2turn0search10  
2. **Core hypothesis:** converting each CoT step into a formal logic formula and verifying entailment/consistency (plus grounding premises) yields a reliable verification signal; that signal can guide self-correction and training. citeturn31view0turn30view3  
3. **Main technical mechanism:** per-step pipeline (Algorithm 1): autoformalise to SMT-LIB; check contradiction/satisfiability; check entailment; if not entailed, generate supporting premises (context or commonsense), formalise them, and optionally apply an LLM-as-judge to assess premise acceptability/attribution. citeturn31view0turn30view2  
4. **Where verification is inserted:** primarily post-hoc over an existing CoT, but used iteratively for inference-time self-reflection (revised CoT is re-verified). Also used to create verified datasets for SFT and to define verification-based preference pairs for DPO. citeturn30view3turn31view0  
5. **Formal object:** SMT-LIB formulas representing a fragment of first-order logic with theories; premise sets; entailment/consistency queries. citeturn31view0turn30view0  
6. **LLM’s role:** autoformaliser; premise generator; (optional) judge of premise acceptability/attribution; self-corrector guided by verifier outputs. citeturn31view2turn30view2turn30view3  
7. **Symbolic system’s role:** Z3 performs satisfiability and entailment checks. citeturn30view0turn31view0  
8. **Training setup:** prompting-based verifier execution; Qwen2.5-7B-Instruct fine-tuned on distilled verified CoTs; DPO using verification-based pairs; evaluation uses Claude-3.5-Sonnet-V2 as executor of VeriCoT pipeline. citeturn30view2turn30view3  
9. **Benchmark coverage:** ProofWriter, LegalBench (SARA subset), BioASQ (task b). citeturn30view2  
10. **Main strengths:** explicit premise grounding and error typing (Ungrounded/Contradiction/Untranslatable); introduces VCAR as a combined validity+accuracy measure; demonstrates verification signals improve both verifiability and accuracy via SFT and DPO. citeturn31view0turn30view3  
11. **Main weaknesses:** limited to what can be expressed/translated into the supported logic fragment; premise generation and LLM-as-judge remain potential sources of confabulation/bias; verifying entailment does not itself guarantee premises are *true* or *acceptable* without the judge layer. citeturn31view0turn30view2turn30view1  
12. **Future opportunities:** better modelling of premise epistemics; scalable premise retrieval/grounding; robustness to autoformalisation errors; richer repair strategies. citeturn30view2turn30view3  

### Comparative matrix across the anchor papers

| Dimension | Beyond Translation | Interleaved Bonus | VeriCoT |
|---|---|---|---|
| Primary bottleneck targeted | Underuse of LLMs as mere translators; hallucinations in complex reasoning citeturn15view0 | Error propagation + reward hacking; lack of step-level consistency citeturn3view1turn29view0 | Unreliable CoT validity; missing premise grounding citeturn31view0turn30view2 |
| Core interface | Decomposition → formal conditions per subproblem citeturn15view0 | Interleaved NL + formal verification/tool calls; execution-validated synthesis citeturn28view0turn29view0 | Stepwise logical auditing of existing CoT; premise reconstruction citeturn31view0turn30view2 |
| Verification placement | Not specified beyond solver “rigorous deduction” citeturn15view0 | During generation + training-time verification-guided SFT/RL citeturn3view1turn28view0 | Post-hoc step checks, used iteratively for self-reflection; training via verified CoTs + DPO citeturn30view3turn31view0 |
| Formal objects | “Formal conditions”, likely FOL (keywords) but unspecified citeturn15view0 | SMT constraints + executable code tools (Python, Z3 tool use) citeturn27view0turn27view1 | SMT-LIB FOL fragment; premise sets; entailment/consistency queries citeturn31view0turn30view1 |
| Training | Unspecified citeturn15view0 | SFT + GRPO RL with hierarchical reward; curated synthesis pipeline citeturn28view0turn27view2turn29view0 | SFT on verified CoTs; DPO built from verification pass/fail pairs citeturn30view3 |
| Domains demonstrated | Not stated on forum page citeturn15view0 | Logical, mathematical, general reasoning benchmarks citeturn29view0 | Rule-based reasoning + legal + biomedical QA citeturn30view2 |
| Key open gap across all three | How to make decomposition/formalisation/repair *learned, robust, and cost-aware* rather than prompt-engineered or brittle. citeturn15view0turn28view2turn31view0 |

## Section C. Expanded literature map

This map is organised by **mechanism cluster** (A–L), focusing on how symbolic solvers and formal verification interact with LLM generation and training.

**A. Translation-only neuro-symbolic systems**  
Mechanism: LLM translates NL → symbolic program/logic; solver executes/deduces; sometimes a refinement loop corrects translation. Logic-LM is a canonical example: translate to symbolic formulation, run deterministic solver, use solver error messages for self-refinement. citeturn33search2 LINC similarly treats the LLM as a semantic parser from NL premises/conclusions to FOL, then offloads deduction to a first-order logic prover; it emphasises multi-sample/majority-vote style mitigation of formalisation errors. citeturn34search35 Evidence: large gains on logical reasoning datasets compared to prompting-only baselines are reported by Logic-LM; LINC reports complementary failure modes to CoT and improves over several baselines in many settings. citeturn33search2turn34search35 Limitation: workflows are constrained by formalisation reliability and by the expressiveness of the chosen symbolic language; they often validate *answers* rather than validating the *faithfulness* of an NL reasoning trace.

**B. Decomposition-based solver collaboration**  
Mechanism: separate “planning/structuring” from “deducing/solving” so that solvers operate on smaller, formally stated subproblems. The anchor “Beyond Translation” explicitly frames this as a two-stage decomposition where the LLM produces an outline then subproblems + formal conditions. citeturn15view0 Related work in tool-integrated reasoning (e.g., ToRA) also builds interactive trajectories combining NL reasoning with tool use (including symbolic solvers), though ToRA is primarily mathematics-focused rather than logic-auditing. citeturn33search4 Evidence: decomposition can raise performance for smaller models, but recent evidence suggests that for frontier models decomposition’s benefit shifts toward diagnostic/auditing rather than raw accuracy improvements, implying decomposition quality should be treated as a reliability signal. citeturn32view0 Limitation: decomposition introduces new failure points (bad subproblem boundaries; missing constraints), and most work lacks explicit optimisation of decomposition quality under verification objectives.

**C. Step-wise CoT verification**  
Mechanism: treat CoT as a sequence of claims to be checked for entailment/consistency, not merely a narrative. VeriCoT formalises each CoT step to SMT-LIB and checks contradiction/entailment, introducing error reasons and premise repair. citeturn31view0turn30view1 Adjacent approaches like RCoT detect inconsistencies by reconstructing the problem from the generated solution and providing fine-grained feedback for revision, illustrating that “reverse checking” can supply actionable step-level signals even without full formalisation. citeturn0academia42 Evidence: VeriCoT improves verification coverage (pass rate) and yields high precision among verified chains; it also shows verification signals can drive improved reasoning via self-reflection and post-training. citeturn30view2turn30view3 Limitation: step-wise checking depends on representability in the chosen logic fragment; “untranslatable” steps remain unverified.

**D. Premise-grounded logical auditing**  
Mechanism: verification systems must account for implicit premises (document facts, commonsense, domain rules). VeriCoT’s premise generation constructs premises that make steps entailable, then filters via consistency checks and an LLM-as-judge premise evaluation stage. citeturn30view2turn31view0 Evidence: VeriCoT reports premise acceptability/grounding breakdown metrics and uses them to audit reasoning in legal/biomed settings. citeturn30view2 Limitation: premise generation can introduce “plausible but false” supports; LLM-as-judge filtering reduces but does not eliminate epistemic risk.

**E. Formal verification interleaved with generation**  
Mechanism: verification feedback is used *mid-generation* to prevent error propagation rather than filter completed traces. The “Interleaved Bonus” anchor paper explicitly argues post-hoc verification is insufficient and inserts SMT-based feedback into the reasoning chain, including global satisfiability constraints and counterexample-driven correction. citeturn3view1turn27view1 Evidence: large improvements across multiple benchmarks are reported under a training regime that teaches models to use verification tools and respond to feedback. citeturn29view0turn28view0 Limitation: formalisation difficulties in ambiguous/commonsense domains can make interleaving harmful unless the feedback channel is robust. citeturn28view2

**F. Verifier-guided SFT / RL / DPO**  
Mechanism: use verifier outputs as supervision signals. “Interleaved Bonus” provides SFT from execution-validated synthetic proof traces and RL with a hierarchical reward (format/structure/correctness). citeturn28view0turn27view3turn29view0 VeriCoT uses verification pass/fail to (i) distil verified CoTs for SFT and (ii) build DPO preference pairs. citeturn30view3 Evidence: both report improved CoT validity metrics and accuracy improvements from verifier-guided post-training. citeturn29view0turn30view3 Limitation: reward hacking can move from “answer hacking” to “verification hacking” (e.g., generating trivially satisfiable but irrelevant statements).

**G. Autoformalisation bottlenecks**  
Mechanism: formalisation is noisy; systems add redundancy and validation. LINC uses multi-sample/majority mechanisms to mitigate translation errors. citeturn34search35 VeriCoT uses a two-stage translation with vocabulary extension loops and limits the number of attempts. citeturn30view1turn31view2 “Interleaved Bonus” uses execution-based validation, rejecting misaligned proofs and discarding deeply inconsistent cases. citeturn28view1turn28view2 Evidence: both anchor papers treat formalisation noise as first-order and devote significant space/metrics to it. citeturn28view1turn31view2 Limitation: there is little formal modelling of the verification channel as *noisy supervision* (most pipelines treat solver outputs as ground truth).

**H. Inference-time self-reflection using symbolic feedback**  
Mechanism: run verifier; if fails, re-prompt model with granular error outputs; re-verify. VeriCoT describes this self-reflection loop explicitly and measures benefits. citeturn30view3 Evidence: correctness and verification metrics improve when models can revise based on structured failure information. citeturn30view3 Limitation: reflection prompts are often heuristic; the “repair policy” is underspecified and not learned as a robust controller.

**I. Verification under weak or noisy formalisation**  
Mechanism: accept partial structure; use softer checks or multiple representations. The survey literature notes uncertainty modelling and hybrid neuro-symbolic integration directions, but practical, general mechanisms remain underdeveloped. citeturn26view0 Evidence: “Interleaved Bonus” explicitly notes mapping errors in ambiguous domains can create incorrect verification feedback, implying the need for robustness techniques. citeturn28view2 Limitation: few systems provide calibrated confidence over verification outputs.

**J. Verification in non-math, high-stakes domains**  
Mechanism: apply solver-audited reasoning to domains where trust requires more than answer accuracy. VeriCoT reports experiments on LegalBench-SARA and BioASQ, explicitly motivated by high-stakes reasoning trust. citeturn30view2turn0search16 Evidence: verification signals can predict correctness and support audited reasoning even in document-grounded settings. citeturn30view2turn30view3 Limitation: domain semantics often exceed what can be captured in a single FOL/SMT fragment.

**K. Scalability / latency / cost bottlenecks**  
Mechanism: verification adds compute; systems add caps and efficiency tradeoffs. “Interleaved Bonus” imposes tool-call limits and penalises excessive tool usage; it also analyses token overhead. citeturn27view3turn29view0 Evidence: they argue overhead is acceptable given gains, but also acknowledge formal verification efficiency constraints. citeturn29view0turn28view2 Limitation: cost-aware routing is mostly hand-designed, not learned.

**L. Evaluation metrics for reasoning validity vs answer accuracy**  
Mechanism: measure more than accuracy. VeriCoT uses pass rate, precision, and VCAR to quantify how often reasoning is verifiable and correct simultaneously. citeturn30view1turn30view2 “Interleaved Bonus” frames evaluation as multi-domain and includes tool-use behaviour analyses. citeturn29view0turn27view0 Limitation: there is no standardised notion of “faithfulness” when the CoT is partially formalised, partially natural language, and influenced by tool feedback.

## Section D. 15+ precise research gaps

1. **Autoformalisation noise is not treated as a probabilistic supervision channel.** VeriCoT and “Interleaved Bonus” both add heuristics (attempt limits, execution validation) but do not model solver feedback as noisy labels with confidence calibration. (Algorithmic + evaluative) Why it matters: training on mis-specified verifier feedback can systematically teach wrong invariants. Plausible fix: noise-aware verification ensembles and calibrated posterior validity. citeturn31view2turn28view1turn28view2  

2. **Step validity is optimised, but decomposition quality is rarely optimised under verification objectives.** “Beyond Translation” and “Interleaved Bonus” both decompose problems/solutions, but neither (in accessible detail) defines metrics or learning objectives for *good decomposition trees*. (Representational + algorithmic) Why it matters: decomposition determines what becomes formalised/verifiable. Plausible fix: learned planners trained with verification-derived rewards on subproblem structure. citeturn15view0turn28view0  

3. **Premise grounding is checked for entailment but weakly checked for truth/acceptability.** VeriCoT adds an LLM-as-judge stage, but this remains a neural heuristic and is not integrated as a dense reward with minimality/necessity constraints. (Algorithmic) Why it matters: systems can “justify” any conclusion with fabricated premises. Plausible fix: premise-level rewards for attribution/necessity/minimality. citeturn30view2turn30view3  

4. **Repair policies are under-modelled.** Both VeriCoT and “Interleaved Bonus” include correction/self-reflection concepts, but repair is not formalised as a policy with action space and training signals. (Algorithmic) Why it matters: verification without repair becomes filtering, not reasoning improvement. Plausible fix: counterexample-driven edit policies. citeturn30view3turn27view1  

5. **Verification can be “hacked” (vacuous satisfiable chains), and defences are mostly format-based.** “Interleaved Bonus” uses hierarchical rewards to prevent pathological generation, but format/structure rewards do not guarantee relevance or progress. (Algorithmic) Why it matters: RL may shift from answer-hacking to verifier-hacking. Plausible fix: progress-based and minimality-based verification rewards. citeturn27view3turn29view0  

6. **Dynamic selection of solver type/formal language is not yet a standard, learned competency.** The EACL 2026 work on dynamic solver composition argues static solver choice limits ability to use diverse inference strategies, but end-to-end training of such selection remains open. (Algorithmic) Why it matters: different tasks map better to different formalisms. Plausible fix: train a routing policy over solver backends. citeturn0search14turn8search7  

7. **Global consistency across chains is used, but “global consistency under uncertainty” is not.** “Interleaved Bonus” uses global satisfiability checks over accumulating claims, but assumes accurate formalisation. (Representational) Why it matters: partial/uncertain semantics are common outside math. Plausible fix: soft constraints and probabilistic entailment estimates. citeturn27view1turn28view2  

8. **Most benchmarks conflate answer accuracy with reasoning validity.** VeriCoT introduces VCAR to decouple them partially, but cross-benchmark standardisation is lacking. (Evaluative) Why it matters: capability gains can mask faithfulness regressions. Plausible fix: multi-metric leaderboards and Pareto-frontier reporting. citeturn30view1turn29view0  

9. **Inference-time cost control is mostly heuristic.** “Interleaved Bonus” includes tool-call limits and penalties; VeriCoT implies attempt limits. Learned cost-aware control remains missing. (Algorithmic) Why it matters: deployment requires predictable latency. Plausible fix: budget-conditioned policies. citeturn27view3turn31view2  

10. **Distillation of solver-guided reasoning into weights is underexplored relative to tool-heavy inference.** VeriCoT does SFT/DPO on verified CoTs; “Interleaved Bonus” claims data efficiency and training-time integration, but systematic “solver invariant distillation” is not isolated as a mechanism. (Algorithmic) Why it matters: reliance on verifiers at inference is costly and brittle. Plausible fix: train internal predictors of verifier outcomes/counterexamples. citeturn30view3turn29view0  

11. **Faithfulness metrics are missing for hybrid traces (NL + formal snippets).** “Interleaved Bonus” and VeriCoT both motivate trust, but neither defines whether the produced CoT is causally tied to the model’s decision rather than post-hoc justification. (Evaluative) Why it matters: verifiable-looking traces can still be post-rationalisations. Plausible fix: counterfactual/ablation-based faithfulness tests plus verification coupling. citeturn3view1turn31view0  

12. **Verification coverage vs accuracy tradeoffs are not optimised explicitly.** VeriCoT can have high precision among verified traces but limited pass rate on some settings; “Interleaved Bonus” discusses overhead limits. (Evaluative + algorithmic) Why it matters: systems may become overly conservative or overly permissive. Plausible fix: multi-objective training for validity/coverage/cost. citeturn30view2turn29view0turn28view2  

13. **Commonsense-heavy domains remain poorly formalised.** Both anchor systems explicitly acknowledge difficulty: VeriCoT needs premise generation; “Interleaved Bonus” warns about mapping errors in ambiguous descriptions. (Representational) Why it matters: real-world reasoning is rarely purely mathematical. Plausible fix: weak-formalisation + retrieval-grounded premises. citeturn30view2turn28view2  

14. **“Disagreement signals” between reasoning modes are underused as verification features.** Decomposition-based prompting shows cross-regime disagreement becomes a strong error signal for frontier models. (Evaluative + algorithmic) Why it matters: can create cheap reliability gates without full formalisation. Plausible fix: combine natural-vs-formal disagreement and solver checks for abstention. citeturn32view0turn28view2  

15. **Verifier design is mostly binary (pass/fail), not explanatory in a machine-usable way.** VeriCoT provides error categories, but structured “repair traces” are not standardised. (Representational) Why it matters: learning effective repair requires richer targets than failure flags. Plausible fix: counterexample-and-edit datasets. citeturn31view0turn27view1  

16. **Benchmark diversity is still narrow relative to deployment settings.** Even with legal/biomedical additions (VeriCoT) and “general reasoning” benchmarks (Interleaved Bonus), most evaluations remain text QA or formal theorem application, not long-horizon, tool-rich, document-grounded multi-step decision scenarios. (Empirical) Why it matters: robustness claims may not transfer. Plausible fix: new eval suites combining grounding + verification + cost constraints. citeturn30view2turn29view0  

## Section E. Candidate idea pool (brief)

Candidate directions generated from the gaps above (before selecting the final 10) include: (i) cost-aware learned routing over *when* and *how* to verify; (ii) counterexample-driven repair policies; (iii) premise-grounding as a dense structured reward; (iv) noise-aware verification ensembles over multiple autoformalisation samples; (v) solver-generated data engines for process supervision; (vi) distillation of solver invariants into model weights; (vii) learned decomposition trees optimised for verifiability; (viii) hybrid weak-formalisation verification for commonsense domains; (ix) Pareto-frontier training for validity vs coverage vs cost; (x) verification-calibrated abstention policies; (xi) evaluation protocols for hybrid NL+formal trace faithfulness; (xii) dynamic solver composition trained end-to-end; (xiii) verifier hacking diagnostics and adversarial training; (xiv) uncertainty-aware premise retrieval; (xv) modular verifier stacks (syntax → semantics → grounding). citeturn28view2turn30view2turn32view0turn0search14turn26view0  

## Section F. Final 10 ideas (full details)

### Idea 1. Budget-Conditioned Verification Routing as a Learned Policy

1. **One-sentence summary**  
Train a controller that decides *when* to invoke formal verification, *which* solver/formalism to use, and *how deep* to verify, under an explicit cost budget.

2. **Research question**  
Can a learned routing policy achieve a better accuracy–validity–latency frontier than fixed “always verify” or hand-tuned tool-call heuristics?

3. **Core hypothesis**  
A budget-conditioned policy trained on verifier outcomes will learn to allocate verification to steps where it has the highest marginal utility (e.g., ambiguity, constraint propagation), outperforming rigid tool-call caps while reducing overhead.

4. **Derivation path from prior work**  
- “Interleaved Bonus” shows verification improves reasoning but introduces overhead and therefore imposes explicit tool-call limits and penalties, indicating cost control is central but currently heuristic. citeturn27view3turn29view0  
- VeriCoT demonstrates step-level verification signals (entailed/contradicted/untranslatable) and uses them for repair and training, implying a rich state signal for a routing/controller policy. citeturn31view0turn30view3  
- Dynamic solver composition work argues static solver choice is limiting, motivating policies that select inference strategies and solvers per instance. citeturn0search14turn8search7  
Therefore: treat “verification decisions” as actions in a learnable controller optimised for multi-objective performance.

5. **Why this idea is non-trivial**  
- Simple heuristics (always verify, verify every k steps, cap at 4) do not adapt to problem type, ambiguity, or formalisation confidence, which “Interleaved Bonus” flags as a failure mode in commonsense-heavy tasks. citeturn28view2turn29view0  
- Dynamic solver choice requires representation of problem structure and uncertainty; naive selection risks worse feedback when formalisation is wrong.

6. **Proposed method**  
- **Inputs:** prompt/context; partial CoT; verifier state (previous check results, counterexamples if any); estimated formalisation uncertainty (from multi-sample divergence).  
- **Model components:**  
  - Base reasoner LLM (frozen or lightly tuned).  
  - Routing head (small policy network or LLM tool-policy) that outputs actions: {no-verify, consistency-check, entailment-check, request-premises, switch-formalism/solver, stop}.  
  - Solver backend set (at minimum SMT/Z3 as in VeriCoT; optionally multiple solver types as in dynamic composition frameworks). citeturn31view0turn0search14  
- **Loop:** generate step → router decides verification action → run check → feed back structured result → continue.  
- **Training:** offline RL or preference learning where reward is a weighted function of (final correctness, VCAR-like verified correctness, cost/latency, tool-call count). Use trajectories from “Interleaved Bonus” style interleaving and VeriCoT error categories as supervision targets. citeturn29view0turn31view0turn30view1  

7. **Baseline papers and why they are the right baselines**  
- **Tier 1 (direct mechanistic):**  
  - Fixed interleaving with tool-call penalties from “Interleaved Bonus” (baseline for cost control without learning). citeturn27view3turn29view0  
  - VeriCoT default pipeline (verify every step; premise generation when not entailed). This is a non-learned but structured verification controller. citeturn31view0turn30view2  
- **Tier 2 (anchor baselines):** both anchor 2 and anchor 3 as above. citeturn3view1turn3view2  
- **Tier 3 (adjacent):**  
  - Dynamic solver composition (select solver strategy) as a baseline for solver-routing without budget-conditioned RL. citeturn0search14  
  - Tool-integrated reasoning agents (e.g., ToRA) as a baseline for tool use but not validity-auditing. citeturn33search4  

8. **Experimental plan**  
- **Datasets/benchmarks:**  
  - Logic + general: KOR-Bench, BBH, GPQA-Diamond, TheoremQA (from “Interleaved Bonus”). citeturn29view0  
  - High-stakes/document: LegalBench-SARA and BioASQ (from VeriCoT). citeturn30view2  
- **Metrics:** accuracy; verification pass rate; verifier precision; VCAR; average tool calls; wall-clock or token cost; cost–validity Pareto frontier. citeturn30view1turn27view3turn29view0  
- **Ablations:** no uncertainty feature; fixed solver; fixed budget; remove premise-generation action; router trained on one domain tested on others.  
- **Failure analysis:** cases where router suppresses needed verification; cases with harmful verification due to bad formalisation.  
- **Success criterion:** strictly dominates heuristic caps in VCAR at matched cost, or achieves same VCAR with fewer tool calls.  
- **Falsification:** if learned router collapses to always-verify or never-verify across domains and fails to improve Pareto frontier.

9. **Expected contribution**  
A general, scalable mechanism for *cost-aware verification*, shifting tool-use from brittle heuristics to learned control.

10. **Main risks / likely failure modes**  
Router learns dataset-specific shortcuts; formalisation noise misleads the reward; sparse situations where verification helps may make learning unstable.

11. **Novelty judgment**  
**Moderate to strong** (learned, budget-conditioned verification control is implied but not standardised in anchor works).

12. **Confidence level**  
**Medium** (strong motivation; engineering and reward design are non-trivial).

13. **Minimal paper abstract draft (120–180 words)**  
We study verification as a *control problem* rather than a fixed pipeline. Prior neurosymbolic systems either verify post-hoc (auditing) or interleave verification with hand-tuned tool-call limits. We propose Budget-Conditioned Verification Routing (BCVR): a learned policy that decides when to verify, which verification operator to apply (consistency vs entailment vs premise request), and which solver formalism to invoke, under an explicit cost budget. BCVR uses structured verifier feedback (e.g., contradiction/ungrounded/untranslatable categories) and formalisation uncertainty estimates to allocate verification where it yields the largest marginal gain. We evaluate BCVR on logical, mathematical, and document-grounded reasoning benchmarks, reporting accuracy, verification coverage, Verified Correct Answer Rate, and cost. BCVR improves the validity–cost Pareto frontier relative to fixed interleaving, stepwise auditing, and static solver selection, demonstrating that verification can be learned as a scalable resource allocation policy.

---

### Idea 2. Counterexample-Driven Minimal-Edit Repair for Verified Chain-of-Thought

1. **One-sentence summary**  
Learn a repair policy that turns verifier failures (unsat, non-entailment, missing premises) into *minimal edits* to CoT steps or premises, closing the loop from detection to correction.

2. **Research question**  
Can models learn systematic “proof repair” behaviours for natural-language CoT using symbolic counterexamples and entailment failures as supervision?

3. **Core hypothesis**  
Training on minimal-edit repairs conditioned on solver feedback will yield more reliable and shorter correction trajectories than prompt-only self-reflection.

4. **Derivation path from prior work**  
- VeriCoT produces granular failure reasons (Ungrounded/Contradiction/Untranslatable) and uses verifier output as a self-reflection signal, but the repair behaviour remains prompt-driven rather than learned. citeturn31view0turn30view3  
- “Interleaved Bonus” explicitly uses SMT-based feedback and highlights counterexample-driven learning as a mechanism (numeric witnesses) plus global satisfiability constraints that identify inconsistent hypotheses. citeturn27view1turn28view0  
- Logic-LM’s self-refinement loop uses solver error messages to revise formalisation, indicating that solver outputs can supervise structured correction. citeturn33search2  
Therefore: unify these into a *minimal-edit repair dataset + learned repair model* for CoT.

5. **Why this idea is non-trivial**  
- “Self-reflection” prompts can drift, over-edit, or introduce new errors because they do not optimise for edit minimality or semantic preservation. citeturn30view3turn28view2  
- Minimal repair requires defining an action space over edits (replace step, insert missing lemma/premise, adjust quantifier/variable) and a correctness notion tied to solver entailment.

6. **Proposed method**  
- **Inputs:** original CoT; per-step formalisation; solver result (sat/unsat, failing constraints, counterexample assignments); premise set; context/document. citeturn31view0turn27view1  
- **Components:**  
  - Repair model (LLM head or separate model) trained to output an *edit script* (insert/delete/replace operations) rather than rewriting full CoT.  
  - Verifier executes patched CoT’s formalisation and checks whether all steps are entailed/consistent.  
- **Training data generation:**  
  - Start from verified CoTs (VeriCoT-verified or execution-validated traces). citeturn30view3turn28view1  
  - Corrupt them with controlled perturbations (swap entity, flip inequality, remove premise).  
  - Use solver counterexample to identify the minimal inconsistent subset; label minimal edit that restores entailment (via search over edits + verifier).  
- **Learning:** supervised learning on edit scripts + optional RL fine-tuning for minimality (penalise number/size of edits and tool calls).

7. **Baseline papers and why they are the right baselines**  
- **Tier 1:**  
  - VeriCoT inference-time self-reflection loop (repair via prompting). citeturn30view3  
  - Logic-LM refinement loop (formalisation repair, not CoT repair). citeturn33search2  
- **Tier 2:**  
  - “Interleaved Bonus” (interleaved correction but not minimal-edit repair as a learned policy). citeturn27view1turn29view0  
- **Tier 3:**  
  - RCoT (inconsistency detection via reversal; feedback-guided revision). citeturn0academia42  
  - Tool-integrated reasoning agents like ToRA (iterative tool feedback but different objective). citeturn33search4  

8. **Experimental plan**  
- **Benchmarks:** ProofWriter and LegalBench-SARA for entailment-heavy reasoning; BBH logical subsets for generalisation. citeturn30view2turn29view0  
- **Metrics:** repair success rate (percentage of failed CoTs repaired to verification pass); edit distance minimality; post-repair VCAR; number of iterations/tool calls. citeturn30view1turn30view3  
- **Ablations:** full rewrite vs edit scripts; remove counterexample features; remove minimality penalty; train only on one domain.  
- **Failure analysis:** repairs that satisfy solver but contradict document; repairs that overfit to solver artefacts.  
- **Success:** higher repair success with fewer iterations and lower edit distance than prompt self-reflection.  
- **Falsification:** if learned repair does not beat prompt-based reflection or produces solver-satisfying but semantically wrong CoTs.

9. **Expected contribution**  
A principled, learnable “repair policy” for reasoning traces that can be integrated into both inference-time controllers and training data engines.

10. **Main risks / likely failure modes**  
Search for minimal edits could be expensive; solver-driven minimality might not align with semantic minimality; risk of “repairing” by adding spurious premises.

11. **Novelty judgment**  
**Strong** (shifts from verification-as-filter to verification-as-edit-supervision with explicit minimality).

12. **Confidence level**  
**Medium** (high potential; dataset construction is demanding).

13. **Minimal paper abstract draft (120–180 words)**  
Formal verifiers can detect invalid reasoning steps, but most LLM systems still rely on heuristic self-reflection to fix them. We propose Counterexample-Driven Minimal-Edit Repair (CDMER), a learned repair policy that converts solver feedback into compact edit scripts over Chain-of-Thought (CoT). CDMER receives (i) the failing CoT, (ii) its stepwise formalisation, and (iii) solver outputs including satisfiability failures and counterexample assignments. Instead of rewriting entire solutions, CDMER predicts minimal insert/delete/replace operations that restore entailment and global consistency while preserving the original semantic intent. We build a repair dataset by perturbing verifier-approved CoTs and searching for smallest repairs that pass formal checks. Across logical, legal, and document-grounded benchmarks, CDMER improves verified correctness (VCAR) and reduces correction iterations relative to prompt-based self-reflection and solver-guided rewriting baselines. Our results position solver feedback as a scalable supervision signal for learning robust reasoning repair policies.

---

### Idea 3. Premise Grounding as Dense Structured Reward for Verifiable Reasoning

1. **One-sentence summary**  
Turn premise attribution, acceptability, and *necessity* into a dense reward (or preference signal) so models learn to produce CoTs that are not only consistent but also properly grounded.

2. **Research question**  
Can premise-level supervision reduce “verification hacking” by preventing models from adding arbitrary premises to make steps entailable?

3. **Core hypothesis**  
Optimising for premise minimality and attribution will increase trustworthiness and reduce spurious supporting statements, improving VCAR without inflating hallucinated premises.

4. **Derivation path from prior work**  
- VeriCoT explicitly generates premises when steps are not entailed, then optionally uses LLM-as-judge to evaluate whether premises are attributable/acceptable, but treats this largely as an auditing step rather than a dense training reward. citeturn30view2turn31view0  
- “Interleaved Bonus” designs hierarchical rewards to prevent format/pathology but does not directly encode premise-grounding quality or necessity in the reward. citeturn27view3turn29view0  
- Decomposition-based and neuro-symbolic surveys highlight autoformalisation and premise grounding as key bottlenecks for trustworthy reasoning. citeturn26view0turn28view2  
Therefore: embed premise-grounding metrics into RL/DPO objectives.

5. **Why this idea is non-trivial**  
- Grounding is multi-faceted: “attributable to document,” “commonsense acceptable,” “necessary for entailment,” and “minimal” can trade off. VeriCoT measures some of these but does not optimise the Pareto frontier. citeturn30view2turn30view3  
- Naively rewarding “more premises” can worsen hallucination; the reward must penalise unnecessary premises even if consistent.

6. **Proposed method**  
- **Inputs:** context/document; CoT; extracted premises; mapping from premise spans to source evidence; formalisation. citeturn30view2turn31view2  
- **Verifier/solver role:**  
  - SMT solver tests entailment with premises.  
  - Attribution checker verifies premise-evidence alignment (LLM-as-judge as in VeriCoT, plus optional string/span match constraints). citeturn30view2  
- **Reward design:**  
  - +1 for each step entailed without contradiction (as usual).  
  - −λ for each premise not attributable/acceptable.  
  - −μ for each premise that is not *necessary* (remove it and entailment still holds). Necessity can be approximated by leave-one-out entailment tests over premises (expensive but can be sampled).  
  - −ν for premise length/complexity to enforce minimality.  
- **Training:** DPO pairs based on (verified & grounded) vs (verified but weakly grounded) CoTs, extending VeriCoT’s pass/fail DPO pairing to a richer preference ordering. citeturn30view3

7. **Baseline papers and why they are the right baselines**  
- **Tier 1:** VeriCoT (premise generation + LLM-as-judge auditing; DPO based on pass/fail). citeturn30view3turn30view2  
- **Tier 2:** “Interleaved Bonus” RL reward scheme (format/structure/correctness). citeturn27view3turn29view0  
- **Tier 3:**  
  - Logic-LM / LINC (solver-based reasoning without premise-level grounding objectives). citeturn33search2turn34search35  
  - RCoT (fine-grained feedback without explicit premise necessity). citeturn0academia42  

8. **Experimental plan**  
- **Datasets:** BioASQ (document-grounded), LegalBench-SARA (statutory reasoning), ProofWriter (rule-based). citeturn30view2  
- **Metrics:** task accuracy; pass rate; precision; VCAR; premise attribution accuracy; average number of premises; “premise necessity rate” (fraction of premises whose removal breaks entailment). citeturn30view1turn30view2  
- **Ablations:** remove necessity penalty; remove attribution penalty; only penalise premise count; different λ/μ/ν.  
- **Failure analysis:** cases where necessary premises are judged unacceptable (judge bias); cases where minimal premises underfit and reduce pass rate.  
- **Success:** improved VCAR and reduced spurious premises at similar accuracy.  
- **Falsification:** no reduction in spurious premises or a collapse to over-conservative “no premise” behaviour that lowers coverage.

9. **Expected contribution**  
A training objective that makes premise grounding a first-class optimisation target, reducing verifier-hacking incentives.

10. **Main risks / likely failure modes**  
Necessity checks can be costly; LLM-as-judge introduces bias; commonsense premises are hard to attribute.

11. **Novelty judgment**  
**Strong** (moves from premise auditing to premise-aware optimisation).

12. **Confidence level**  
**Medium**.

13. **Minimal paper abstract draft (120–180 words)**  
Neuro-symbolic verifiers increasingly reconstruct missing premises to make Chain-of-Thought (CoT) steps formally entailable. However, premise generation creates a new failure mode: models can “verify” flawed reasoning by inventing arbitrary premises. We propose Premise-Grounded Process Optimisation (PGPO), which turns premise attribution, acceptability, and necessity into dense supervision. PGPO augments stepwise entailment checks with (i) premise–document attribution scoring and (ii) necessity testing via leave-one-out entailment probes, penalising premises that are unverifiable, unacceptable, or unnecessary. We train models using verifier-derived preference learning and cost-aware rewards, encouraging CoTs that achieve entailment with minimal, grounded premises. On document-grounded biomedical QA (BioASQ), statutory reasoning (LegalBench-SARA), and rule-based reasoning (ProofWriter), PGPO increases Verified Correct Answer Rate while reducing spurious premise insertion. Our study highlights that trustworthy verification requires optimising not only logical consistency but also the epistemic quality of the premises that support each reasoning step.

---

### Idea 4. Noise-Aware Verification via Multi-Formalisation Posteriors

1. **One-sentence summary**  
Treat autoformalisation as a stochastic channel and compute a posterior probability of validity by aggregating multiple formalisation attempts and solver outcomes.

2. **Research question**  
Can we reduce harmful verifier feedback by explicitly modelling uncertainty over formalisation, especially in ambiguous/commonsense-heavy steps?

3. **Core hypothesis**  
A posterior validity estimate (instead of binary pass/fail) will improve downstream control (routing, repair, abstention) and enable safer training signals.

4. **Derivation path from prior work**  
- “Interleaved Bonus” explicitly states that ambiguous descriptions can produce mapping errors that yield incorrect verification feedback, limiting generalisability and motivating more robust autoformalisation. citeturn28view2  
- VeriCoT uses a two-stage vocabulary extension loop and limits retries, implicitly acknowledging formalisation uncertainty but still producing discrete outcomes. citeturn31view2turn30view1  
- LINC employs a majority voting step to mitigate formalisation errors, showing multi-sample aggregation is effective. citeturn34search2turn34search35  
Therefore: formalise the aggregation into a probabilistic posterior and use it across training/inference.

5. **Why this idea is non-trivial**  
- Majority voting over strings is not equivalent to calibrated validity; different formalisation samples can fail for correlated reasons (systematic translation bias).  
- Need to define likelihoods: e.g., probability that a step is truly entailed given solver unsat could be affected by formalisation syntax/semantic mismatch.

6. **Proposed method**  
- **Inputs:** step text + context; set of K autoformalisation samples (SMT-LIB formulas + mappings); solver outcomes (sat/unsat/unknown); optional counterexamples and error logs. citeturn31view2turn27view1  
- **Posterior model:**  
  - Estimate P(valid | {formalisation_i, solver_i}) using a learned calibration model trained on a labelled set where “ground truth validity” is determined by (i) known synthetic tasks with exact formal semantics or (ii) agreement across multiple independent verifiers.  
  - Include features: formalisation agreement, solver consistency across samples, syntax error rates, vocabulary extension count. citeturn31view2turn28view1  
- **Usage:**  
  - Replace binary verification with posterior thresholds for routing/repair.  
  - For training, weight rewards/gradients by posterior confidence (reduces learning from noisy feedback), echoing the “Interleaved Bonus” idea of weighting by verification success but with a calibrated probability. citeturn27view1turn28view2  

7. **Baseline papers and why they are the right baselines**  
- **Tier 1:** LINC majority voting; VeriCoT two-stage autoformalisation; “Interleaved Bonus” execution-based validation. citeturn34search35turn31view2turn28view1  
- **Tier 2:** both anchor 2 and 3 as end-to-end systems with binary verification decisions. citeturn3view1turn3view2  
- **Tier 3:** Logic-LM self-refinement (single-path correction) as contrast to probabilistic aggregation. citeturn33search2  

8. **Experimental plan**  
- **Benchmarks:** mix of structured (ProofWriter) and ambiguous (BioASQ, some BBH). citeturn30view2turn29view0  
- **Metrics:** calibration error of posterior (ECE/Brier) on validity labels; impact on VCAR and accuracy when used for routing or training; reduction in “harmful verification” cases (where verifier says fail but reasoning is correct, or vice versa). citeturn30view1turn28view2  
- **Ablations:** K=1 vs K>1; remove calibration model; use simple majority vs posterior; treat solver unknown as fail vs probabilistic.  
- **Success:** improved calibration and better downstream performance (higher VCAR at same cost).  
- **Falsification:** posteriors do not improve calibration or downstream decisions relative to majority voting.

9. **Expected contribution**  
A principled way to incorporate formalisation uncertainty into neuro-symbolic reasoning pipelines, enabling safer training and control.

10. **Main risks / likely failure modes**  
Obtaining validity ground truth is hard; correlation between formalisation samples reduces benefit; increased compute for K samples.

11. **Novelty judgment**  
**Moderate** (extends voting to calibrated posteriors and integrates into training/control).

12. **Confidence level**  
**Medium**.

13. **Minimal paper abstract draft (120–180 words)**  
Autoformalisation is the central bottleneck in neuro-symbolic reasoning: incorrect mappings can produce misleading solver feedback, harming both inference and verifier-guided training. Existing systems mitigate this with retries, execution validation, or majority voting over multiple formalisation samples. We propose Noise-Aware Verification (NAV), which treats formalisation as a stochastic channel and outputs a posterior probability that each reasoning step is valid, aggregating K autoformalisation attempts and their solver outcomes. NAV learns a calibration model using structured validity labels derived from synthetic tasks and cross-verifier agreement, producing confidence estimates that reflect both solver results and formalisation uncertainty (e.g., disagreement, syntax failures, vocabulary growth). We show that replacing binary pass/fail decisions with posterior-weighted control improves cost-aware verification routing and reduces harmful verification errors, yielding higher Verified Correct Answer Rate at matched tool budgets. NAV provides a general mechanism for stable verifier-guided learning under imperfect formalisation.

---

### Idea 5. Verifier-as-Data-Engine for Solver-Interactive Process Supervision

1. **One-sentence summary**  
Use symbolic solvers to generate large-scale, step-verifiable reasoning traces (including *negative* traces with labelled failure modes), then distil them into models via SFT and preference learning.

2. **Research question**  
Can we build a general-purpose “process supervision” dataset generator that scales beyond hand-written CoTs and avoids outcome-only reward hacking?

3. **Core hypothesis**  
Solver-generated step labels (entailed/contradicted/ungrounded) can supply dense, low-bias supervision for reasoning behaviours that remains valuable even as base models improve.

4. **Derivation path from prior work**  
- “Interleaved Bonus” constructs a hierarchical data synthesis pipeline because interleaved NL+formal datasets are scarce, using execution-based validation to retain high-fidelity proof traces. citeturn28view0turn28view1  
- VeriCoT shows verifier signals can be used for SFT and DPO by distilling verified CoTs and building pass/fail preference pairs. citeturn30view3  
- Logic-LM shows solver error messages can guide self-refinement, suggesting negative examples with structured errors are valuable training signal. citeturn33search2  
Therefore: scale this into a data engine that produces *both positive and negative process traces with structured labels*.

5. **Why this idea is non-trivial**  
- Simply generating more CoTs risks amplifying model biases; the key is generating *verifiable intermediate supervision* and *diverse failure modes*.  
- Need a principled task distribution so the resulting skills transfer (not benchmark-specific).

6. **Proposed method**  
- **Inputs:** templated task generators across domains (logic puzzles, rulebases, quantitative word problems, document-grounded entailment with extracted constraints).  
- **Pipeline:**  
  1) Sample a latent structured instance (facts/rules/constraints).  
  2) Generate NL problem statements (multiple paraphrases).  
  3) Use solver to generate proof/derivation steps and minimal premises.  
  4) Generate *hard negatives*: perturb a step, remove a premise, flip a quantifier; solver identifies contradiction or non-entailment and returns counterexample. citeturn27view1turn31view0  
  5) Train reasoner on (NL, CoT, labels) with multi-task objectives: generate answer, generate verifiable steps, predict step validity labels, propose repair edits (ties to Idea 2).  
- **Training:** SFT on positive traces + DPO using (verified vs invalid) pairs as in VeriCoT, but with richer label structure. citeturn30view3turn28view0

7. **Baseline papers and why they are the right baselines**  
- **Tier 1:** “Interleaved Bonus” data synthesis pipeline; VeriCoT verified-CoT distillation + DPO; Logic-LM self-refinement. citeturn28view0turn30view3turn33search2  
- **Tier 2:** both anchor 2 and 3 as end-to-end verified training frameworks. citeturn3view1turn3view2  
- **Tier 3:** LINC/Logic-LM as translation-only reasoning baselines. citeturn34search35turn33search2  

8. **Experimental plan**  
- **Benchmarks:** out-of-generator evaluations: ProofWriter, FOLIO-like tasks (via LINC/Logic-LM lineage), BBH logic, plus BioASQ/LegalBench to test document grounding transfer. citeturn30view2turn34search35turn29view0  
- **Metrics:** accuracy; VCAR; robustness under paraphrase; generalisation to unseen rule schemas; calibration of step-validity predictions. citeturn30view1turn30view2  
- **Ablations:** no hard negatives; no counterexamples; only outcome supervision; only positive traces.  
- **Success:** models trained on solver-generated process supervision show improved VCAR and robustness relative to size-matched baselines trained on standard CoT.  
- **Falsification:** improvements do not transfer beyond generator distribution (overfitting), or validity gains come with large accuracy drops.

9. **Expected contribution**  
A scalable data pipeline for reasoning process supervision grounded in formal verification, potentially reusable as a community benchmark/training resource.

10. **Main risks / likely failure modes**  
Generator bias; limited semantic diversity; high compute to generate and verify large corpora.

11. **Novelty judgment**  
**Moderate to strong** (data-engine framing + structured negatives + multi-task supervision).

12. **Confidence level**  
**Medium**.

13. **Minimal paper abstract draft (120–180 words)**  
Verifier-guided training is promising but bottlenecked by scarce, high-quality datasets containing machine-checkable intermediate reasoning. We propose Verifier-as-Data-Engine (VDE), a scalable pipeline that synthesises solver-interactive process supervision at step granularity. VDE samples latent structured problems (rules, constraints, premises), renders them into diverse natural-language tasks, and uses symbolic solvers to produce verified derivations. Crucially, VDE also generates hard negative traces by perturbing steps or removing premises, labelling failure modes (contradiction, non-entailment, ungrounded) and extracting counterexamples. We train reasoning models with multi-task objectives: answer prediction, verifiable CoT generation, step-validity classification, and counterexample-conditioned repair. Across logical, mathematical, and document-grounded benchmarks, VDE-trained models achieve higher Verified Correct Answer Rate and improved robustness under paraphrase and distribution shift compared to standard CoT and outcome-only RL baselines. VDE reframes verifiers from filters into data engines for general reasoning training.

---

### Idea 6. Distilling Solver Invariants into Internal “Verifier Predictors” to Reduce Tool Dependence

1. **One-sentence summary**  
Train models to internally predict solver outcomes (sat/unsat/entails) and counterexample structure, enabling fast “approximate verification” and reducing reliance on external solvers at inference.

2. **Research question**  
Can we distil solver behaviour into model weights without losing the generality benefits of formal verification?

3. **Core hypothesis**  
Learning to predict verifier outcomes (and *why*) will improve reasoning robustness and efficiency, and will complement external verification when available.

4. **Derivation path from prior work**  
- “Interleaved Bonus” shows formal verification improves performance with limited data and discusses verification overhead and tool-call limits, motivating internalisation of some verification capacity. citeturn29view0turn27view3  
- VeriCoT outputs structured step outcomes and errors, which can serve as labels to train a predictor head. citeturn31view0  
- LINC and Logic-LM show that formalisation errors are common and multi-sample techniques help; internal predictors could learn to detect when formalisation is likely wrong. citeturn34search35turn33search2  
Therefore: distil “verifier semantics” into a neural module, keeping external solver as optional oracle.

5. **Why this idea is non-trivial**  
- Verifier prediction must generalise beyond training distributions; otherwise it collapses to a model-based verifier with similar biases.  
- Must avoid leaking ground truth answer into the verifier predictor; prediction should be about *logical validity* given formalisation.

6. **Proposed method**  
- **Inputs:** (context, NL step, formalised SMT formula, current knowledge base/premises). citeturn31view0turn30view1  
- **Model:** add a verifier-predictor head producing:  
  - P(entails), P(contradiction), P(unknown/untranslatable)  
  - optional token-level localisation of offending sub-formulas  
  - optional “counterexample sketch” (variable assignments) when contradiction. citeturn27view1turn31view0  
- **Training:**  
  - Supervise using solver outputs from VeriCoT / “Interleaved Bonus” pipelines (and the VDE dataset from Idea 5).  
  - Use knowledge distillation: run solver on a subset; train predictor on all; calibrate with temperature scaling.  
- **Inference:**  
  - Use predictor for cheap screening; invoke real solver only when predictor is uncertain or when high-stakes.

7. **Baseline papers and why they are the right baselines**  
- **Tier 1:** model-based verifiers and verifier-guided RL baselines in “Interleaved Bonus” (they compare to model-based verifier training like General-Reasoner). citeturn29view0  
- **Tier 2:** VeriCoT (solver-based auditing); “Interleaved Bonus” (solver-interleaved reasoning). citeturn31view0turn29view0  
- **Tier 3:** LLM-as-judge approaches (as criticised in both anchors) as baseline for neural verification without formal tools. citeturn3view1turn30view2  

8. **Experimental plan**  
- **Benchmarks:** same as Idea 1 plus synthetic out-of-distribution rule sets (ProofWriter variants). citeturn30view2turn29view0  
- **Metrics:** verifier prediction accuracy + calibration; end-to-end VCAR when predictor replaces solver or acts as gate; latency/tool-call reduction. citeturn30view1turn27view3  
- **Ablations:** predictor trained only on one domain; predictor without formal formula input; predictor without counterexample supervision.  
- **Success:** significant reduction in solver usage at same (or improved) VCAR; calibrated uncertainty.  
- **Falsification:** predictor fails to generalise and harms VCAR vs solver-only systems.

9. **Expected contribution**  
A bridge between strict symbolic verification and scalable neural inference, making verification benefits usable under tight budgets.

10. **Main risks / likely failure modes**  
Predictor becomes an LLM-as-judge in disguise; learns spurious correlations; counterexample prediction is hard.

11. **Novelty judgment**  
**Moderate**.

12. **Confidence level**  
**Medium**.

13. **Minimal paper abstract draft (120–180 words)**  
Formal solvers provide unbiased validity checks but are expensive to run at scale. We propose Solver-Invariant Distillation (SID): training LLMs to internally predict formal verification outcomes (entailment, contradiction, untranslatable) and approximate counterexamples, using solver outputs as supervision. SID adds a verifier-predictor head conditioned on a step’s natural-language text, its SMT formalisation, and the accumulated premise set. We train on solver-labelled traces produced by existing verification pipelines and synthetic verifier-generated datasets, calibrating probabilities to model verification uncertainty. At inference, the predictor acts as a cheap gate: it screens steps and triggers external solver calls only when uncertainty is high or consequences are severe. Across logical, mathematical, and document-grounded benchmarks, SID reduces solver usage and latency while preserving (and in some cases improving) Verified Correct Answer Rate. SID complements symbolic tools rather than replacing them, enabling scalable verification-aware reasoning under strict compute budgets.

---

### Idea 7. Verification-Aware Decomposition Planning Learned from Solver Feedback

1. **One-sentence summary**  
Learn decomposition trees that maximise downstream verifiability: subproblems should be chosen so each step is easy to formalise and prove with minimal premises and minimal tool calls.

2. **Research question**  
Can we train models to decompose problems into subgoals that are *optimised for formal verification*, not just for human readability?

3. **Core hypothesis**  
Decomposition that explicitly targets verifiability (formalisation success, entailment rate, low premise injection) produces more faithful reasoning and better verifier-guided training data than generic “plan then solve” decompositions.

4. **Derivation path from prior work**  
- “Beyond Translation” argues that LLMs should do contextual exploration and problem structuring while solvers do deduction; it proposes outline + subproblem decomposition with formal conditions. citeturn15view0  
- “Interleaved Bonus” decomposes reasoning chains into discrete logical modules for formal proof synthesis and executes validation to ensure alignment. citeturn28view0turn28view1  
- VeriCoT reveals that steps often require hidden premises and may be untranslatable; a decomposition that reduces “untranslatable” and “ungrounded” steps is directly valuable. citeturn31view0turn30view2  
Therefore: make decomposition quality a learnable objective driven by verifier outcomes.

5. **Why this idea is non-trivial**  
- Decomposition is a structured prediction problem: trees/sequences with long-term effects (later steps depend on earlier splits).  
- Verifiability depends on formal language constraints: a “good” human decomposition might still be hard to formalise.

6. **Proposed method**  
- **Inputs:** problem context; optional retrieved docs; target answer format; available solver backends.  
- **Components:**  
  - Decomposition planner module that outputs a tree: nodes are subgoals with typed variables and expected outputs.  
  - Solver interface that attempts to formalise and verify each node’s output.  
  - Critic that scores decomposition by (formalisation success rate, entailment consistency across nodes, premise minimality, total tool calls). citeturn31view0turn27view3turn28view1  
- **Training:**  
  - Supervise planner from successful decompositions extracted in “Interleaved Bonus” synthesis and from verified CoTs in VeriCoT. citeturn28view0turn30view3  
  - RL on decomposition actions using verifier-derived reward; optionally constrain with budget (ties to Idea 1).

7. **Baseline papers and why they are the right baselines**  
- **Tier 1:** “Beyond Translation” (decomposition-first solver collaboration); “Interleaved Bonus” module decomposition; VeriCoT (no explicit decomposition planning). citeturn15view0turn28view0turn31view0  
- **Tier 2:** anchor papers 1–3 collectively. citeturn15view0turn3view1turn3view2  
- **Tier 3:**  
  - ToRA (tool-use trajectories but not decomposition-for-verifiability). citeturn33search4  
  - Decomposition-based prompting studies (shows decomposition helps differently by scale; suggests decomposition quality is nuanced). citeturn32view0  

8. **Experimental plan**  
- **Benchmarks:** BBH logical tasks; ProofWriter; TheoremQA; LegalBench-SARA (to test decomposition with statutory premises). citeturn29view0turn30view2  
- **Metrics:** step formalisation success; “untranslatable” rate; pass rate; VCAR; tool-call count; decomposition tree quality metrics (depth, branching, reuse). citeturn31view0turn30view1turn27view3  
- **Ablations:** planner without verifier reward; planner without typing/vocabulary hints; no premise minimality term.  
- **Success:** improved verifiability (higher pass rate/VCAR) at similar accuracy, with fewer untranslatable steps.  
- **Falsification:** decomposition reward causes degenerate decompositions (too fine-grained, trivial) or harms accuracy.

9. **Expected contribution**  
A principled approach to make decomposition a *verification-aware skill* rather than a prompt pattern.

10. **Main risks / likely failure modes**  
Sparse reward; high variance RL; planner may learn trivial decompositions that game metrics.

11. **Novelty judgment**  
**Strong** (explicitly optimises decomposition structure under formal verification constraints).

12. **Confidence level**  
**Medium**.

13. **Minimal paper abstract draft (120–180 words)**  
Decomposition is a key interface between LLM reasoning and symbolic solvers, yet existing work rarely optimises decomposition itself. We propose Verification-Aware Decomposition Planning (VADP), which learns to generate decomposition trees whose subgoals are maximally amenable to autoformalisation and proof. VADP outputs typed subproblems and expected intermediate artefacts, then uses formal verifiers to score decomposition quality by (i) formalisation success, (ii) entailment consistency across subgoals, (iii) premise minimality, and (iv) verification cost. We train VADP using a combination of supervised distillation from solver-validated traces and reinforcement learning over decomposition actions. Across logical, mathematical, and document-grounded reasoning benchmarks, VADP reduces untranslatable and ungrounded steps, increases verification pass rates, and improves Verified Correct Answer Rate under fixed tool budgets. Our results suggest that decomposition should be treated as a learnable policy optimised for verifiability, not merely a prompting heuristic.

---

### Idea 8. Hybrid-Trace Faithfulness Metrics for NL + Formal Verification Reasoning

1. **One-sentence summary**  
Define and validate evaluation protocols that measure whether hybrid (NL + formal tool) reasoning traces are *causally faithful* and not merely post-hoc rationalisations.

2. **Research question**  
How can we measure “faithfulness” when reasoning is partly a natural-language narrative and partly formal constraints/proofs?

3. **Core hypothesis**  
A metric that combines (i) formal step validity, (ii) premise grounding, and (iii) **counterfactual dependence** of the final answer on intermediate claims will detect rationalisation failures that pass pure satisfiability checks.

4. **Derivation path from prior work**  
- “Interleaved Bonus” reports that even correct-answer chains can contain formally disproved steps, exposing a mismatch between outcome and step validity and motivating deeper evaluation beyond accuracy. citeturn3view1turn3view1  
- VeriCoT introduces premise reconstruction and explicit error types; it increases transparency by exposing premises and formalised steps, implying evaluation can and should inspect these artefacts. citeturn30view3turn31view0  
- Decomposition-as-auditor work shows cross-regime disagreement becomes a strong error signal for large models, suggesting behavioural counterfactuals are informative. citeturn32view0  
Therefore: build evaluation metrics using formal validity + counterfactual behavioural tests.

5. **Why this idea is non-trivial**  
- Measuring causal dependence requires interventions (remove/change steps, alter premises) and re-evaluation under controlled semantics; naive ablation can change context length and confound.  
- Need to separate “trace faithfulness” from “trace plausibility.”

6. **Proposed method**  
- **Faithfulness protocol (three axes):**  
  1) **Formal validity:** fraction of steps entailed given premises (VeriCoT). citeturn31view0  
  2) **Grounding:** proportion of premises attributable/acceptable (VeriCoT premise evaluation). citeturn30view2  
  3) **Counterfactual dependence:** re-run the model under controlled edits:  
     - Remove a supposedly critical premise/step; does the answer change appropriately?  
     - Swap an intermediate derived fact with its negation; does the model detect inconsistency or still output same answer?  
     - Replace NL step text while keeping formal constraints constant; does output remain stable?  
- **Metric:** Trace Faithfulness Score = validity × grounding × dependence (with calibrated confidence).  
- **Dataset construction:** start from solver-verified traces; create counterfactual variants via solver-guided perturbations (related to Idea 5). citeturn27view1turn31view0  

7. **Baseline papers and why they are the right baselines**  
- **Tier 1:** VeriCoT metrics (pass rate/precision/VCAR) and “Interleaved Bonus” step-consistency analysis (validity without causal dependence). citeturn30view1turn3view1  
- **Tier 2:** the two anchor papers again. citeturn3view1turn3view2  
- **Tier 3:** decomposition disagreement-based auditing (DBA) as a behavioural consistency baseline. citeturn32view0  

8. **Experimental plan**  
- **Benchmarks:** a subset of VeriCoT tasks (ProofWriter, LegalBench-SARA) plus “Interleaved Bonus” logic benchmarks. citeturn30view2turn29view0  
- **Metrics:** correlation between Trace Faithfulness Score and held-out adversarial correctness (under perturbations); ability to identify “lucky guesses” (correct answer but low faithfulness score). citeturn3view1turn30view1  
- **Ablations:** remove counterfactual component; remove grounding; remove formal validity.  
- **Success:** new metric better separates reliable from unreliable answers than accuracy or VCAR alone, especially under perturbation.  
- **Falsification:** counterfactual dependence does not add signal beyond formal validity/grounding.

9. **Expected contribution**  
A defensible evaluation methodology for “faithfulness” in neuro-symbolic reasoning systems, supporting more rigorous comparisons and training targets.

10. **Main risks / likely failure modes**  
Interventions may be expensive; models may be sensitive to prompt formatting; causality is hard to approximate.

11. **Novelty judgment**  
**Strong** (evaluation innovation, not just method transfer).

12. **Confidence level**  
**Medium**.

13. **Minimal paper abstract draft (120–180 words)**  
Neuro-symbolic reasoners increasingly produce hybrid traces containing natural-language Chain-of-Thought (CoT) plus formal constraints and solver feedback. Yet current evaluations focus on answer accuracy or binary verification pass rates, leaving “faithfulness” underspecified: a trace can be consistent while still being a post-hoc rationalisation. We introduce Hybrid Trace Faithfulness (HTF), an evaluation protocol combining (i) formal step validity, (ii) premise grounding quality, and (iii) counterfactual dependence tests that probe whether intermediate claims causally influence the final answer. HTF constructs solver-guided counterfactual variants by removing or negating purportedly critical steps/premises while controlling for context and format, then measures whether model outputs respond appropriately. Across logical and document-grounded benchmarks, HTF identifies “lucky guesses” and rationalised traces that standard metrics miss, and better predicts robustness under perturbation. HTF provides a missing evaluation layer for trustworthy reasoning, enabling more meaningful benchmarking and training of verifiable CoT systems.

---

### Idea 9. Multi-Objective Training to Optimise the Validity–Coverage–Cost Pareto Frontier

1. **One-sentence summary**  
Train reasoning models under an explicit multi-objective objective to balance (a) verified validity, (b) verification coverage, and (c) cost—rather than optimising them separately.

2. **Research question**  
Can we directly optimise the Pareto frontier between verifiability (pass rate/VCAR) and task accuracy under compute constraints?

3. **Core hypothesis**  
Explicit multi-objective optimisation will avoid regimes where models become overly conservative (high precision, low pass rate) or overly permissive (high pass rate, low precision), improving overall utility.

4. **Derivation path from prior work**  
- VeriCoT introduces pass rate, precision, and VCAR, explicitly separating “verified and correct” from overall accuracy; results show variants can have very low pass rate despite accuracy, highlighting a coverage issue. citeturn30view2turn30view1  
- “Interleaved Bonus” controls verification overhead with tool-call limits and penalises excess calls, implying cost is a first-class axis. citeturn27view3turn29view0  
Therefore: treat (VCAR, pass rate, cost) as joint objectives and train with Pareto-aware RL/DPO.

5. **Why this idea is non-trivial**  
- Simply maximising VCAR might push models to output “untranslatable” or minimal traces to avoid failure; pass rate might be increased by producing overly simple steps.  
- Need mechanisms for multi-objective optimisation (e.g., scalarisation schedules, constrained RL).

6. **Proposed method**  
- **Objective:** maximise E[accuracy] subject to constraints on cost and minimum pass rate, or directly optimise a vector reward and compute Pareto front.  
- **Training loop:**  
  - Use verification signals (pass/fail, entailment, error types) as in VeriCoT. citeturn31view0  
  - Use hierarchical reward stabilisation as in “Interleaved Bonus” to prevent pathologies, but extend final reward to include pass-rate and cost regularisers. citeturn27view3turn29view0  
  - Use adaptive scalarisation: sample weights for (validity, coverage, cost) per minibatch to learn a set of Pareto-optimal policies (or a single policy conditioned on desired tradeoff).

7. **Baseline papers and why they are the right baselines**  
- **Tier 1:** VeriCoT SFT+DPO (optimises pass/fail but not explicit Pareto tradeoffs); “Interleaved Bonus” GRPO with cost penalties (but objective is not framed as Pareto frontier). citeturn30view3turn27view3  
- **Tier 2:** both anchor 2 and 3. citeturn3view1turn3view2  
- **Tier 3:** LINC/Logic-LM (solver use without explicit validity-coverage tradeoffs). citeturn34search35turn33search2  

8. **Experimental plan**  
- **Benchmarks:** ProofWriter + BioASQ + one general benchmark like BBH; measure frontier shifts. citeturn30view2turn29view0  
- **Metrics:** plot Pareto curves of (VCAR vs cost) at multiple pass-rate thresholds; calibration of tradeoff conditioning; user-level utility metrics (expected verified-correct answers per unit cost). citeturn30view1turn27view3  
- **Ablations:** no Pareto conditioning; fixed scalar weights; remove cost constraint; remove pass-rate constraint.  
- **Success:** learned policy set dominates single-objective baselines, delivering tunable operating points.  
- **Falsification:** Pareto training collapses to extreme points only (always verify or never verify) or fails to improve compared to scalar baselines.

9. **Expected contribution**  
A rigorous training and reporting framework that treats verifiable reasoning as multi-objective optimisation, improving reproducibility and deployment alignment.

10. **Main risks / likely failure modes**  
Complex RL optimisation; difficulty stabilising Pareto learning; evaluation noise across benchmarks.

11. **Novelty judgment**  
**Moderate** (reframes and systematises objectives; may be seen as methodological contribution).

12. **Confidence level**  
**Medium**.

13. **Minimal paper abstract draft (120–180 words)**  
Verification-based reasoning introduces new tradeoffs: a model can be highly accurate but rarely verifiable, or frequently verifiable but unreliable, and verification increases cost. Existing work reports multiple metrics (e.g., pass rate, precision, Verified Correct Answer Rate) and uses heuristic tool-call limits, but does not explicitly optimise these axes jointly. We propose Pareto-Optimised Verifiable Reasoning (POVR), a multi-objective training framework that learns policies along the validity–coverage–cost frontier. POVR extends verifier-guided post-training with Pareto-aware optimisation: policies are trained under sampled reward scalarisations or budget-conditioned constraints, combining stepwise entailment checks with cost regularisation and stability safeguards against pathological generation. We evaluate POVR across logical and document-grounded benchmarks, reporting Pareto curves of verified correctness versus tool cost at fixed coverage levels. POVR yields tunable operating points that dominate single-objective baselines, enabling principled selection of verification strength under deployment budgets and advancing standardised evaluation for trustworthy reasoning systems.

---

### Idea 10. Verification-Calibrated Abstention and “Audit-First” Interaction Policies

1. **One-sentence summary**  
Build abstention and user-interaction policies that use verification failure probabilities and cross-regime disagreement to decide when to answer, when to verify more, and when to say “I don’t know.”

2. **Research question**  
Can verification signals be turned into reliable abstention policies that outperform confidence-based or prompt-based uncertainty methods, especially for frontier models?

3. **Core hypothesis**  
Combining (i) solver-based validity signals (VeriCoT / FLV) with (ii) cross-regime disagreement signals (direct vs decomposed) yields a strong, training-free or lightly trained reliability gate.

4. **Derivation path from prior work**  
- VeriCoT provides structured failure reasons and shows verification signals improve reasoning and transparency; these signals can naturally define “unsafe to answer” conditions. citeturn31view0turn30view3  
- “Interleaved Bonus” quantifies logical inconsistency even in correct answers and limits tool calls, implying that systems need a principled way to decide *when more verification is worth it* and when to stop. citeturn3view1turn27view3  
- Decomposed prompting work shows that for large models, disagreement between direct and decomposed outputs is a precise error signal and can drive a training-free abstention method (DBA). citeturn32view0  
Therefore: integrate solver verification confidence + behavioural disagreement into an abstention/audit policy.

5. **Why this idea is non-trivial**  
- Abstention requires calibrated decision thresholds and must avoid trivial “abstain always” behaviour.  
- Need to align abstention with user utility: sometimes ask clarifying question, sometimes provide partial verified sub-results.

6. **Proposed method**  
- **Signals:**  
  - VeriCoT outcomes per step + posterior validity from Idea 4. citeturn31view0turn30view1  
  - Cross-regime disagreement between direct and decomposed prompting (DBA-style). citeturn32view0  
  - Tool-cost budget and marginal gain estimates (Idea 1). citeturn27view3turn29view0  
- **Policy:**  
  - If (high disagreement) OR (low validity posterior) → abstain or request more evidence / run extra verification steps.  
  - If validity high but grounding low → respond with caveats and list missing premises (audit-first explanation).  
- **Training:** can be training-free thresholding (as DBA is), or learned calibration on held-out data to maximise F1/AUROC for error detection.

7. **Baseline papers and why they are the right baselines**  
- **Tier 1:** DBA disagreement-based abstention (decomposition-based; no formal verifier). citeturn32view0  
- **Tier 2:** VeriCoT (verification + self-reflection but not abstention-focused); “Interleaved Bonus” (verification-guided reasoning but not abstention-focused). citeturn30view3turn29view0  
- **Tier 3:** LLM confidence / “Are you sure?”-style uncertainty baselines discussed in abstention work (as compared within that paper). citeturn32view0  

8. **Experimental plan**  
- **Tasks:** closed-book multi-hop QA (as in decomposition-disagreement study) plus document-grounded settings (BioASQ, LegalBench-SARA) where verification is meaningful. citeturn32view0turn30view2  
- **Metrics:** abstention precision/recall/F1/AUROC (error detection); coverage vs accuracy; verified-coverage utility (how many answers are both provided and verified-correct). citeturn32view0turn30view1  
- **Ablations:** only disagreement, only solver-signal, combined; different thresholds; add cost budget.  
- **Success:** improved AUROC/F1 over DBA and confidence baselines; better tradeoff between answered fraction and error rate.  
- **Falsification:** combined signals fail to outperform DBA alone or lead to excessive abstention.

9. **Expected contribution**  
A practical reliability layer that remains valuable with stronger base models: it operationalises when to trust outputs and when to escalate verification.

10. **Main risks / likely failure modes**  
Solver signals may be unavailable for some tasks; disagreement can arise from benign variation; user experience depends on good abstention messaging.

11. **Novelty judgment**  
**Moderate** (strong synthesis across solver verification and behavioural disagreement).

12. **Confidence level**  
**High** for utility as an evaluation/control component; **medium** for broad generalisation.

13. **Minimal paper abstract draft (120–180 words)**  
As LLMs scale, prompting-based decomposition often stops improving accuracy but becomes a powerful diagnostic: disagreement between direct and decomposed answers precisely predicts errors. Separately, neuro-symbolic verifiers can detect logical inconsistency in Chain-of-Thought (CoT) reasoning, but systems still lack principled “when to answer” policies. We propose Verification-Calibrated Abstention (VCA), which combines solver-based validity signals (step entailment/contradiction outcomes and premise-grounding checks) with cross-regime disagreement to decide whether to answer, verify further, ask for evidence, or abstain. VCA can be deployed training-free via calibrated thresholds or lightly tuned to optimise error-detection AUROC under coverage constraints. Across closed-book multi-hop QA and document-grounded legal/biomedical reasoning, VCA improves error detection over confidence-based and decomposition-only abstention baselines while maintaining higher useful coverage of verified-correct answers. VCA reframes verifiers as interaction controllers, enabling reliability-aware deployment of reasoning models under uncertainty and limited verification budgets.

## Section G. Cross-idea ranking table

| Idea | Novelty | Feasibility | Expected empirical gain | Publication potential | Dependence on expensive tooling | Risk level |
|---|---|---|---|---|---|---|
| 1. Budget-Conditioned Verification Routing | High | Medium | High | High | Medium–High (solver calls) | Medium |
| 2. Counterexample-Driven Minimal-Edit Repair | High | Medium–Low | High | High | Medium–High | High |
| 3. Premise-Grounded Structured Reward | High | Medium | Medium–High | High | Medium | Medium |
| 4. Noise-Aware Multi-Formalisation Posteriors | Medium | Medium | Medium | Medium–High | Medium | Medium |
| 5. Verifier-as-Data-Engine Process Supervision | Medium–High | Medium–Low | High | High | High (data generation) | High |
| 6. Distilled Solver Invariants (Verifier Predictors) | Medium | Medium | Medium | Medium | Medium | Medium |
| 7. Verification-Aware Decomposition Planning | High | Medium–Low | Medium–High | High | Medium–High | High |
| 8. Hybrid-Trace Faithfulness Metrics | High | Medium | Medium (evaluation-first) | High | Low–Medium | Medium |
| 9. Pareto Validity–Coverage–Cost Optimisation | Medium | Medium | Medium | Medium–High | Medium | Medium |
| 10. Verification-Calibrated Abstention Policies | Medium | High | Medium–High | Medium–High | Low–Medium | Low–Medium |

**Single best idea for a short paper**: **Idea 8 (Hybrid-Trace Faithfulness Metrics)** — can be executed with limited model training, focused on evaluation protocol + controlled perturbation experiments, and addresses a reviewer-visible gap in current verification work. citeturn31view0turn3view1turn32view0  

**Single best idea for a full conference paper**: **Idea 2 (Counterexample-Driven Minimal-Edit Repair)** — strong algorithmic novelty, clear experimental hooks (repair success, minimality, VCAR), and directly resolves the “verification without repair” bottleneck across both anchor systems. citeturn30view3turn27view1turn33search2  

**Single best idea for a long-term research agenda**: **Idea 1 (Budget-Conditioned Verification Routing)** — a unifying control-layer that can incorporate advances in solver choice, noise-aware verification, repair, and abstention; it targets scalability and deployment constraints that will persist as models improve. citeturn27view3turn29view0turn0search14turn31view0