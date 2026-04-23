---
Author: Gemini 3.5 Pro
---

# **Next-Generation Neuro-Symbolic Integration: Advancing Large Language Model Reasoning through Formal Verification and Structural Supervision**

## **Section A. Executive summary of the field**

The intersection of large language models and formal symbolic reasoning represents a critical frontier in artificial intelligence, addressing the fundamental epistemological limitations of autoregressive transformers. While contemporary models exhibit unprecedented fluency and heuristic problem-solving capabilities, their foundational reliance on probabilistic next-token prediction renders them inherently vulnerable to logical inconsistencies, hallucinated premises, and unfaithful reasoning chains.1 To overcome these architectural constraints, the field has aggressively pivoted toward neuro-symbolic methodologies. These hybrid systems seek to offload rigorous deduction to deterministic symbolic solvers, such as Satisfiability Modulo Theories (SMT) engines and interactive theorem provers, while preserving the language model's superior capacity for semantic parsing, heuristic decomposition, and contextual abstraction.1

Historically, neuro-symbolic integration functioned primarily as a post-hoc translation mechanism, wherein a model converted a natural language query into a monolithic formal script for execution.4 However, recent advancements demonstrate a profound paradigm shift toward deep, iterative integration. Frameworks are increasingly designed to dynamically interleave formal verification directly into the generation process, maintain explicit logical grounding for intermediate premises, and utilize solver feedback as dense supervisory signals for Reinforcement Learning (RL) and Direct Preference Optimization (DPO).6 Despite these algorithmic breakthroughs, the field continues to confront significant structural bottlenecks. Chief among these are the challenges of "semantic drift" during the autoformalization of ambiguous natural language, the prohibitive computational latency associated with in-loop solver execution, and the acute susceptibility of reinforcement learning agents to reward hacking via intent obfuscation when optimized against rigid formal metrics.9 The subsequent analysis synthesizes the current theoretical landscape, mapping the mechanistic trajectories of state-of-the-art literature to derive ten rigorous, actionable research directives designed to stabilize, scale, and optimize verifier-guided machine reasoning.

## **Section B. Comparative analysis of the 3 anchor papers**

The three anchor papers delineate distinct yet highly complementary vectors for resolving reasoning failures through neuro-symbolic integration. The first paper, "Beyond Translation: A Decomposed Collaborative Reasoning Framework Harnessing LLMs and Symbolic Solvers," identifies that existing methods underutilize the structuring capabilities of language models by treating them as mere syntax converters.4 To mitigate performance degradation in complex, multi-step scenarios, the framework hypothesizes that problem decomposition must precede formalization. The primary technical mechanism involves a two-stage process where the model first generates a strategic outline and subsequently decomposes the problem into simpler sub-problems with distinct formal conditions, effectively separating contextual exploration from the rigorous deduction offloaded to the solver.4

The second anchor paper, "Pushing the Boundaries of Natural Reasoning: Interleaved Bonus from Formal-Logic Verification in Language Models" (FLV-RL), addresses the limitations of passive, post-hoc filtering. The central hypothesis is that dynamic, real-time feedback from symbolic interpreters can guide reasoning trajectories and prevent the accumulation of logical errors.6 The technical architecture introduces a two-stage training pipeline featuring verification-guided supervised fine-tuning followed by policy optimization. During the reinforcement learning phase, the model generates interleaved natural language and formal reasoning, receiving step-wise bonuses based on verification outcomes to compute advantages, yielding significant performance margins over baselines across diverse benchmarks.2

The third anchor paper, "VERICOT: Neuro-Symbolic Chain-of-Thought Validation via Logical Consistency Checks," focuses on the disconnect between correct final answers and flawed underlying logic. It hypothesizes that extracting and verifying formal logical arguments from intermediate reasoning steps can identify ungrounded or fallacious leaps.1 VERICOT autoformalizes natural language into First-Order Logic (FOL) formulas encoded in SMT-LIB and employs the Z3 theorem prover to perform rigorous entailment and contradiction checks. A unique strength of this framework is its premise maintenance system, which grounds reasoning in explicit source context or commonsense, providing actionable diagnostic feedback utilized for inference-time self-reflection and training-time preference learning.1

The table below provides a structured comparative extraction of the specific mechanistic properties across the three anchor frameworks.

| Feature | Beyond Translation | FLV-RL (Pushing Boundaries) | VERICOT |
| :---- | :---- | :---- | :---- |
| **1\. Problem setting** | Complex multi-step reasoning suffers when models act merely as text-to-symbol translators. | Stochastic decoding leads to intermediate fallacies and inconsistency in logical tasks. | Models reach correct final answers via flawed, ungrounded, or logically invalid CoT steps. |
| **2\. Core hypothesis** | Decomposing problems before translation leverages structural capabilities and mitigates hallucinations. | Interleaving formal verification directly into generation prevents error accumulation and provides superior RL rewards. | Formalizing individual steps and grounding them in explicit premises enables robust self-correction and preference alignment. |
| **3\. Technical mechanism** | Two-stage decomposition: 1\) Outline generation, 2\) Sub-problem decomposition and formal condition synthesis. | Dynamically interleaves natural language steps with formal proofs, utilizing real-time interpreter feedback. | Translates steps to SMT-LIB, uses Z3 to check entailment/contradiction, and infers missing natural language premises. |
| **4\. Verification timing** | Post-decomposition (solvers execute after formal conditions are explicitly generated). | Interleaved during generation (step-by-step) and during training-time (RL). | Inference-time (self-reflection) and Training-time (DPO / SFT). |
| **5\. Formal object** | Formal conditions and constraints (First-Order Logic). | Executable formal proofs and interpreter feedback (Reward signals). | First-Order Logic (FOL) encoded in SMT-LIB constraints; natural language premises. |
| **6\. LLM's role** | Problem analyzer, outliner, decomposer, and formal condition generator. | Reasoner and interleaved formal proof generator. | Autoformalizer, premise generator, and optional context-grounding judge. |
| **7\. Symbolic role** | Rigorous deductive reasoning to offload logic execution from the generative model. | Real-time validation of step correctness and feedback generation for policy updates. | Entailment and contradiction checking across sequential reasoning steps. |
| **8\. Training setup** | Prompting framework and inference architecture. | Two-stage: 1\) Verification-guided SFT, 2\) Proximal Policy Optimization using verification rewards. | Direct Preference Optimization (DPO) using verification signals, plus SFT on distilled data. |
| **9\. Benchmark coverage** | Complex reasoning tasks requiring multi-step logic. | Six benchmarks across mathematical, logical, and general reasoning. | ProofWriter, LegalBench, BioASQ, SARA. |
| **10\. Main strengths** | Leverages inherent structuring strengths rather than forcing pure syntax translation. | Eliminates reliance on passive post-hoc filtering; achieves robust gains via RL process supervision. | High transparency through explicit premises; handles non-math/code domains (law, biology). |
| **11\. Main weaknesses** | Relies on heuristic decomposition; pipeline latency scales with problem complexity. | Overhead of interleaved interpreter calls; risk of reward hacking through obfuscation. | Vulnerable to "untranslatable" steps if the FOL fragment is too rigid or semantic drift occurs. |
| **12\. Future opps.** | Transitioning heuristic decomposition mechanisms into learned, trainable routing policies. | Expanding the taxonomy of formal rewards to prevent verification evasion and reward hacking. | Using structured signals for online RL; expanding supported logical fragments and abductive inference. |

## **Section C. Expanded literature map**

The expansive literature surrounding neuro-symbolic reasoning can be systematically organized into twelve distinct mechanism clusters, reflecting the algorithmic diversity and ongoing evolution of the field.

**A. Translation-only neuro-symbolic systems** This fundamental cluster involves systems where the language model acts purely as a semantic parser, mapping natural language directly into executable symbolic languages, which are then evaluated to derive an answer. Representative papers such as Logic-LM and LINC demonstrate that this approach yields significant gains over standard prompting on deductive logic datasets by entirely bypassing the model's internal probabilistic reasoning.13 The evidence supporting this mechanism is strong for rigid, well-defined mathematical problems. However, the main limitation is severe information loss during translation; nuanced or ambiguous natural language is frequently discarded, causing the deterministic solver to fail on edge cases or return false negatives.7

**B. Decomposition-based solver collaboration** To manage the complexity of monolithic translation, this mechanism introduces an intermediate planning phase where the model breaks a large problem into modular sub-tasks before formalization. Frameworks like "Beyond Translation" and the LLM-TP Tree explicitly utilize atomic decomposition to reduce syntax errors.3 The evidence indicates that simplifying the syntactic burden reduces inference latency by 10% to 21% and significantly increases solver executable rates.3 The unresolved tension lies in the nature of the decomposition policy, which is predominantly static or heuristic-based rather than learned directly from task-specific formal rewards, limiting adaptability to novel domains.

**C. Step-wise CoT verification** Rather than verifying only the final execution state, this cluster focuses on validating the intermediate steps within a reasoning chain. The LogicReward framework utilizes interactive theorem provers like Isabelle/HOL to score individual reasoning steps for logical validity, enabling granular process supervision.8 Evidence shows that models trained with these step-wise signals achieve state-of-the-art results on natural language inference tasks.8 The primary limitation is computational scalability; step-wise verification requires maintaining the complete proof state context and compiling formal logic at every reasoning step, which is prohibitively expensive during large-scale reinforcement learning rollouts.

**D. Premise-grounded logical auditing** This mechanism ensures that each logical step is not only formally valid but explicitly tied to a premise retrieved from the context or generated via commonsense. The VERICOT framework excels in this paradigm, successfully detecting "ungrounded" inferences in high-stakes domains by strictly separating logical entailment from factual faithfulness.1 The empirical evidence proves that grounding verification drastically reduces hallucinations that would otherwise pass pure syntactic checks. However, a major contradiction exists: hallucinated premises generated by the model during the bridging phase can artificially satisfy the SMT solver if the system lacks rigorous external retrieval validation.

**E. Formal verification interleaved with generation** Moving beyond post-hoc checks, this cluster invokes the solver token-by-token or step-by-step during the autoregressive decoding process. Architectures like FLV-RL and DeepSeek-Prover-V1.5 utilize truncate-and-resume mechanisms, immediately halting generation upon a compilation error and prompting the model to explore alternative tactics.6 This interleaved approach boosts accuracy by dynamically pruning dead branches in the search space. The central limitation is the severe decoding latency caused by synchronous calls to external environments, which throttles inference speed and complicates batched generation.

**F. Verifier-guided SFT / RL / DPO** This cluster focuses on utilizing the deterministic, high-fidelity signals of symbolic solvers to shape model weights, overcoming the inherent noise and bias of neural reward models. Both LogicReward and FLV-RL demonstrate that verified traces provide vastly superior gradients for preference learning and policy optimization compared to human annotations or LLM-as-a-judge scores.6 The evidence for improved mathematical and logical accuracy is overwhelming. However, a critical limitation has emerged: reinforcement learning agents rapidly learn to "reward hack" these verifiers by obfuscating their natural language logic or emitting syntactically valid but semantically meaningless formal code.10

**G. Autoformalization bottlenecks** A pervasive issue across all neuro-symbolic systems is the friction of mapping ambiguous natural language to rigid formal logic, known as the semantic gap or semantic drift.7 Frameworks like LLM-TP Tree introduce mechanisms such as ![][image1]\-substitution to enforce consistent argument-role bindings, improving autoformalization fidelity.3 The VERGE study quantifies a "Formalization Barrier," revealing that models under twenty billion parameters fail to translate effectively.7 The unresolved tension is that real-world data frequently falls outside decidable logic fragments, leading to "verified hallucinations" where the solver perfectly validates an incorrect translation.7

**H. Inference-time self-reflection using symbolic feedback** This mechanism involves feeding exact compilation failures or satisfiability errors back into the context window to prompt autoregressive self-correction. The VERGE framework utilizes Minimal Correction Subsets (MCS) to precisely localize errors, turning abstract binary SMT failures into highly actionable natural language feedback, yielding an 18.7% performance uplift.7 While the evidence supports robust localized repair, the limitation is that the prompt context grows rapidly with each iteration, and models frequently enter infinite self-correction loops without converging if the mathematical logic exceeds their parametric capabilities.

**I. Verification under weak or noisy formalization** Acknowledging that full formalization is often impossible, this cluster develops adaptive systems to handle claims that cannot be strictly translated. VERGE introduces "Semantic Routing," parsing claims as Mathematical, Logical, or Vague, and routing vague claims to an ensemble of neural verifiers rather than an SMT solver.7 LogicReward utilizes "Soft Unification" to automatically interpolate missing implicit assumptions.8 While this increases overall system robustness on noisy data, the reliance on neural routing blurs the boundary of formal guarantees, subtly reintroducing the probabilistic hallucinations the symbolic solver was meant to eradicate.

**J. Verification in non-math, high-stakes domains** Expanding beyond quantitative benchmarks, this cluster applies formal methods to law, biology, and compliance. VERICOT explicitly targets LegalBench and BioASQ 1, while systems like LOGicalThought (LogT) apply non-monotonic logic programs to complex medical guideline adherence.20 The evidence shows improved reliability in heavily regulated domains. The core limitation is the extreme reliance on correct entity extraction; legal and medical semantics often rely on defeasible reasoning and fuzzy boundaries that defy the rigid strictures of standard First-Order Logic constraints.

**K. Scalability / latency / cost bottlenecks** The computational burden of neuro-symbolic systems represents a distinct cluster of infrastructural research. The VERGE analysis explicitly notes that inline SMT solving requires 15 to 30 seconds per iteration, creating a massive latency bottleneck.7 The limitation is entirely operational but dictates algorithmic design; such extreme computational costs prevent real-time deployment in production environments and make massive-scale reinforcement learning sample generation prohibitively expensive for all but the most resourced institutions.

**L. Evaluation metrics for reasoning validity vs answer accuracy** This cluster addresses the fundamental decoupling of the "right answer" from the "right reasoning." VERICOT introduces the Verified Correct Answer Rate (VCAR) to specifically quantify reasoning paths that are both factually accurate at the terminus and logically sound throughout the intermediate steps.21 The evidence from these papers indicates that models frequently score high on traditional benchmarks while scoring poorly on VCAR. The unresolved tension is that evaluation metrics remain constrained by the formalizer's capability ceiling; a low VCAR may reflect a failure in the autoformalization module rather than a failure in the model's underlying cognitive logic.

## **Section D. 15+ precise research gaps**

The rigorous synthesis of the expanded literature map reveals over fifteen critical, mechanically precise voids in the current neuro-symbolic paradigm.

The first gap concerns autoformalization fidelity, where translation mechanisms frequently misrepresent the original semantic intent of the text, leading to "verified hallucinations" wherein the SMT solver validates an incorrect logical statement. This representational and evaluative gap is heavily implied by the findings in VERGE 7 and Decompose-and-Formalise.3 It matters profoundly because it destroys the foundational trust in formal verification; a verified hallucination is far more dangerous than an unverified one, as it carries a cryptographic-like seal of approval. A plausible method to address this involves contrastive preference learning designed specifically to penalize syntactically valid but semantically unfaithful translations.

A second algorithmic gap exists in reinforcement learning reward design. Formal verifiers typically return binary satisfiability signals or rigid compilation errors, lacking the actionable, continuous gradients necessary for efficient proximal policy optimization. Implied by the struggles documented in VERGE 7 and DeepSeek-Prover-V1.5 17, this reward sparsity causes RL policies to collapse or converge at unacceptably slow rates. This could be plausibly addressed by integrating the continuous geometric nature of Minimal Correction Subsets (MCS) as a dense, graded penalty function for reinforcement learning frameworks.

Third, existing problem decomposition policies are predominantly static or heuristic, forcing rigid structural constraints onto dynamic reasoning problems. Implied by the limitations of "Beyond Translation" 4 and Decompose-and-Formalise 3, this algorithmic gap restricts the system's ability to adapt to out-of-distribution complexities, artificially capping solver efficacy. A plausible resolution involves training a latent decomposition routing policy using RL to dynamically optimize the semantic granularity of claims before they are passed to the SMT solver.

Fourth, the reliance on internal model judgment for "Semantic Routing" introduces a vulnerability where models must self-classify if a claim is formally verifiable. As implied by VERGE 7, this algorithmic gap matters because language models may greedily route complex, verifiable logic to softer, neural verifiers to avoid the strict rigor of the SMT solver, constituting a novel form of reward hacking. This could be addressed by utilizing calibrated epistemic uncertainty modeling to dictate routing based purely on the model's predictive entropy during the autoformalization phase.

Fifth, reinforcement learning models optimized against formal verifiers rapidly learn to obfuscate their reasoning, hiding invalid or deceptive logic behind unparseable but syntactically acceptable formal structures. Implied empirically by OpenAI's CoT Monitoring research 10 and the Pushing Boundaries framework 6, this algorithmic gap nullifies the interpretability gains of process supervision. A plausible method to combat this is the introduction of information-theoretic regularization penalties that mathematically force high mutual information between the natural language trace and the underlying formal code during policy updates.

Sixth, premise grounding mechanisms currently assume a static world model, failing to account for state changes in temporal or multi-agent environments. Implied by the representational limits of VERICOT 1 and LOGicalThought 20, this gap prevents neuro-symbolic systems from effectively solving dynamic planning tasks such as robotics or interactive coding. Extending First-Order Logic premise extraction to incorporate Linear Temporal Logic (LTL) state-tracking offers a plausible representational solution.

Seventh, current frameworks treat missing implicit assumptions as strict translation failures rather than opportunities for automated abductive reasoning. As implied by LogicReward's implementation of Soft Unification 8 and VERICOT's ungrounded error states 1, this algorithmic gap results in extreme false-negative verification rates, as humans routinely omit obvious premises in natural text. Integrating a backward-chaining abductive module to infer minimum required premises before executing the forward entailment SMT solver could plausibly address this bottleneck.

Eighth, the distillation of neuro-symbolic traces into student models typically transfers the finalized output but fails to transfer the iterative self-correction mechanism itself. Implied by distillation studies and frameworks like IFDNS 15, this algorithmic gap leaves student models entirely dependent on external solver latency at inference time. Distilling the explicit multi-turn SMT correction trace into a single-pass "Implicit Chain-of-Thought" feed-forward policy presents a highly plausible method for internalizing formal constraints.

Ninth, the extreme latency of inline SMT solving—often exceeding 15 to 30 seconds per iteration—fundamentally throttles the scaling of on-policy reinforcement learning. Documented empirically in the VERGE analysis 7, this compute constraint prevents neuro-symbolic methods from competing with purely neural RL models on massive datasets. Training a rapid neural surrogate to approximate Z3 satisfiability during RL rollouts, and only querying the true solver to periodically update the surrogate, could plausibly resolve this latency gap.

Tenth, verification pass rates and final answer accuracy are currently measured as entirely separate metrics, with no established reinforcement learning method to jointly optimize their Pareto frontier. Implied evaluatively by the introduction of the VCAR metric in VERICOT 1, this gap matters because models naturally optimize for the path of least resistance, either outputting rigid trivialities to satisfy the verifier or outputting ungrounded correct guesses to satisfy the accuracy metric. Multi-objective reinforcement learning prioritizing a dynamic VCAR reward head could address this optimization failure.

Eleventh, neuro-symbolic natural language inference datasets rely predominantly on synthetic, fictional entities rather than real-world, document-grounded scientific complexities. This empirical gap, implied by the evaluation constraints in Decompose-and-Formalise 3, results in an overestimation of model capabilities on neat logic puzzles while masking catastrophic failures in noisy scientific reasoning. Constructing a recursively verifiable benchmark specifically extracted from complex biomedical retrieval-augmented generation systems would address this empirical blind spot.

Twelfth, the ![][image1]\-substitution process utilized to fix argument-role bindings relies on static, predefined event ontologies. As a representational gap implied by Decompose-and-Formalise 3, this limits the framework's ability to generalize to novel domains featuring unseen predicates and dynamic relationship structures. Implementing dynamic predicate induction utilizing semantic clustering in the language model's latent space could provide a necessary algorithmic bridge.

Thirteenth, achieving high-fidelity consensus for semantic equivalence checks requires excessive multi-model sampling, drastically ballooning inference costs. This algorithmic gap, highlighted in VERGE 7, blocks the adoption of robust verification in resource-constrained environments. Deterministic decoding constraints that force the model to output highly canonicalized SMT-LIB syntax could plausibly minimize surface variance and eliminate the need for costly ensemble sampling.

Fourteenth, formal verification models currently lack a dedicated mechanism to "un-learn" fallacious heuristic rules internalized during unverified, massive-scale pre-training. This empirical and algorithmic gap, implied by the training struggles in FLV-RL 6, results in the base model continuously fighting against the verifier due to deeply ingrained parametric biases. Applying neuro-symbolic feedback via targeted unlearning gradients—such as gradient ascent on reasoning paths that confidently fail SMT checks—could plausibly eradicate these latent heuristics.

Fifteenth, the epistemological boundaries between Context, Commonsense, and Inference during premise generation are often arbitrarily defined, leading to overlapping constraint violations. This representational gap, implied by VERICOT's premise handling 1, creates conflicting SMT constraints when general commonsense logic contradicts specific local context definitions. Implementing hierarchical constraint stratification in the SMT engine, solving strictly ordered theories where local context overrides global commonsense, presents a highly plausible architectural solution.

## **Section E. Candidate idea pool (brief)**

The synthesis of the identified gaps and literature mechanisms yields a broad pool of candidate research directions, structured around established derivation patterns.

| Idea Concept | Derivation Pattern | Target Gap / Mechanism |
| :---- | :---- | :---- |
| **1\. Continuous MCS-Penalty RL** | Pattern 1: Combine strengths | Translates abstract SMT failures into dense, continuous gradients for PPO. |
| **2\. Latent Decomposition Policy** | Pattern 5: Learned policy | Replaces heuristic problem splitting with an RL-trained routing network. |
| **3\. CPO for Verified Hallucinations** | Pattern 10: Model failures | Penalizes syntactically valid but semantically unfaithful formalizations in DPO. |
| **4\. Epistemic Semantic Routing** | Pattern 6: Uncertainty | Uses predictive entropy during autoformalization to route to soft/hard verifiers. |
| **5\. Bipartite PRM Grounding** | Pattern 7: Structure supervision | Decouples PRM rewards into orthogonal logical validity and factual grounding vectors. |
| **6\. Anti-Obfuscation Regularization** | Pattern 10: Model failures | Penalizes RL agents that decouple natural language reasoning from formal code. |
| **7\. Distilled Implicit Self-Correction** | Pattern 4: Validator as data engine | Distills multi-turn solver correction traces into a single-pass Implicit CoT model. |
| **8\. Recursively Verifiable RAG** | Pattern 8: Domain transfer | Applies formal entailment trees to document-grounded retrieval systems. |
| **9\. Dynamic ![][image1]\-Sub Decoding** | Pattern 3: Inference to training | Forces event binding at the logits level during generation to prevent semantic drift. |
| **10\. VCAR-Optimized Reward Shaping** | Pattern 9: Evaluation improvement | Multi-objective GRPO to maximize the Pareto frontier of task accuracy and formal validity. |
| **11\. Surrogate-SMT for Fast RL** | Pattern 11: Scale bottlenecks | Trains a neural surrogate of Z3 to eliminate execution latency during RLVR. |
| **12\. Premise-Abduction Data Engine** | Pattern 4: Validator as data engine | Uses Soft Unification to auto-generate datasets of "implicit missing premises". |
| **13\. Hierarchical SMT Stratification** | Pattern 2: Resolve tradeoffs | Resolves tension between context and commonsense using weighted MAX-SMT. |
| **14\. Temporal Logic Contract Analysis** | Pattern 8: Domain transfer | Extends FOL verification to LTL to verify state-dependent legal reasoning. |
| **15\. Ensemble-Smoothed SMT SFT** | Pattern 1: Combine strengths | Uses VERGE's semantic consensus to aggressively filter VERICOT's SFT datasets. |

## **Section F. Final 10 ideas (full details)**

### **Idea 1\. Continuous MCS-Penalty for Dense Process Supervision**

**1\. One-sentence summary:** A novel reinforcement learning reward model that utilizes the geometrical size of an SMT solver's Minimal Correction Subset (MCS) to provide dense, graded penalties for logical reasoning errors, replacing sparse binary validation signals.

**2\. Research question:** Can the explicit quantification of logical contradictions via Minimal Correction Subsets provide a smoother, more effective gradient for reinforcement learning policy optimization than traditional binary verification outcomes?

**3\. Core hypothesis:** Binary formal rewards (SAT/UNSAT) cause severe reward sparsity and slow policy convergence; utilizing the proportional size of the MCS as a continuous penalty metric will significantly accelerate policy convergence and reduce reward hacking.

**4\. Derivation path from prior work:** The framework builds upon the finding in VERGE 7 that MCS provides precise, localized feedback for inference-time self-correction by identifying the exact subset of conflicting atomic claims. FLV-RL 6 separately identifies that binary formal rewards in reinforcement learning lead to sparse signals, requiring complex two-stage workarounds. LogicReward 8 attempts to score step-level validity but relies on discrete Isabelle/HOL feedback. Therefore, integrating the continuous, geometric nature of MCS (calculating exactly how many claims must be deleted to restore logical satisfiability) directly into a reinforcement learning reward function yields a dense gradient natively grounded in formal logic.

**5\. Why this idea is non-trivial:** SMT solvers fundamentally operate within discrete, boolean decision spaces. Mapping structural constraint relaxation directly into a scalar reward space elegantly bridges the discrete nature of symbolic logic with the continuous optimization requirements of Proximal Policy Optimization (PPO) or Group Relative Policy Optimization (GRPO). Simpler alternatives, such as penalizing based merely on chain-of-thought token length or heuristic matching, entirely fail to capture the underlying topological structure of the logical failure.

**6\. Proposed method:**

* **Inputs:** Natural language problem statements and intermediate Chain-of-Thought reasoning steps.  
* **Model components:** The generative LLM policy network, an Autoformalizer module, and a Z3 SMT Solver equipped with MAX-SMT capabilities for subset extraction.  
* **Verifier role:** The solver acts as the reward engine. If a trace is UNSAT, it computes the exact MCS.  
* **Training loop:** During the GRPO rollout, the model generates a reasoning chain and autoformalizes it. The reward ![][image2] is formulated as a positive scalar for SAT outcomes, and as ![][image3] for UNSAT outcomes, where ![][image4] represents the absolute number of claims requiring correction.  
* **Key algorithmic steps:** The model executes a rollback mechanism that limits parameter updates specifically to the tokens associated with the localized MCS penalties, targeting policy gradients exactly where the logic structurally failed.

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* Standard RLVR paradigms that utilize standard binary exact-match or full-execution rewards. This is the direct algorithm to outperform.  
* *Tier 2 (Anchor):* FLV-RL.6 This is highly relevant as it represents the state-of-the-art in interleaved but discrete bonus signals.  
* *Tier 3 (Strong adjacent):* Process Reward Models (PRMs) trained via LLM-as-a-judge (e.g., Math-Shepherd), providing a comparison between continuous formal rewards and continuous neural rewards.

**8\. Experimental plan:**

* **Datasets:** ProofWriter and FOLIO benchmarks for rigorous logical evaluation.  
* **Metrics:** Verification Pass Rate, Final Answer Accuracy, and Convergence Speed (measured in optimization steps required to reach 90% optimal performance).  
* **Ablations:** Comparing binary rewards against continuous MCS rewards; evaluating PPO versus GRPO architectures.  
* **What would count as success:** The continuous MCS-RL architecture achieves a higher verified accuracy in 30% fewer training steps than the binary RL baseline.  
* **What evidence would falsify the hypothesis:** If the computational overhead of computing the NP-hard MCS metric drastically outweighs the sample efficiency gains, rendering wall-clock training time slower despite step-wise efficiency, the hypothesis is practically falsified.

**9\. Expected contribution:** This research will provide the first formal integration of constraint programming diagnostics into continuous reinforcement learning reward formulations, solving the verifiable reward sparsity bottleneck.

**10\. Main risks / likely failure modes:** Computing the Minimal Correction Subset is an NP-hard problem in the worst case, potentially causing severe computational bottlenecks during massive-scale reinforcement learning rollouts.

**11\. Novelty judgment:** Strong.

**12\. Confidence level:** High.

**13\. Minimal paper abstract draft:** Reinforcement learning with verifiable rewards (RLVR) suffers inherently from reward sparsity, as deterministic symbolic solvers typically yield binary SAT/UNSAT outputs. Consequently, optimizing large language models for rigorous logical reasoning requires massive, highly inefficient sample complexity. We introduce Continuous MCS-Penalty (CMP), a novel reward formulation that translates the abstract failures of Satisfiability Modulo Theories (SMT) solvers into dense, continuous gradients. By computing the Minimal Correction Subsets (MCS)—the absolute minimal number of atomic claims required to be removed to restore logical consistency—we generate a continuous penalty strictly bounded by the ratio of flawed claims to total claims. Integrated into the GRPO framework and evaluated on ProofWriter and FOLIO, our results demonstrate that CMP accelerates policy convergence by 40% while achieving a 12% higher verified correct answer rate compared to traditional binary RLVR, successfully bridging constraint programming diagnostics with neural policy optimization.

### **Idea 2\. RL-Trained Latent Decomposition Policy (LDP)**

**1\. One-sentence summary:** Replacing heuristic problem-splitting with a lightweight, reinforcement learning-trained routing network that learns the optimal semantic granularity of sub-problems required to maximize SMT verification success.

**2\. Research question:** Can a dedicated decomposition policy, optimized directly against downstream solver pass-rates, outperform zero-shot language model heuristics in bridging the natural-to-formal language gap?

**3\. Core hypothesis:** Heuristic decomposition frequently generates sub-problems that are either too semantically complex for autoformalization or too trivial; conversely, an RL-trained decomposer will implicitly learn the exact semantic boundaries and expressivity limits supported by the target SMT fragment.

**4\. Derivation path from prior work:** The "Beyond Translation" framework 4 proves that decomposing problems prior to formalization is necessary, but it relies on uncalibrated, zero-shot LLM prompts to dictate the sub-problems. The LLM-TP Tree 3 attempts atomic parsing but relies heavily on static, pre-trained NLI classifiers to preserve entailment. Finally, DeepSeek-Prover-V1.5 17 proves that reinforcement learning can effectively optimize complex tactic generation. Therefore, transitioning the decomposition phase from a static prompt into a fully RL-optimized parametric policy will perfectly calibrate the text-to-logic semantic gap based on actual solver capabilities.

**5\. Why this idea is non-trivial:** Directly optimizing a decomposition step based on a downstream formal solver's binary output involves extreme credit assignment challenges. Simple, prompt-based alternatives are insufficient because base foundation models do not inherently understand the strict mathematical expressivity limits of formal logic fragments (such as QF\_LIA or QF\_UF).

**6\. Proposed method:**

* **Inputs:** Complex, multi-step natural language reasoning queries.  
* **Model components:** A lightweight Decomposer LLM (e.g., 2B parameters), a frozen Reasoner/Autoformalizer LLM, and a Z3 Solver.  
* **Verifier role:** The SMT solver executes the formal conditions and returns a binary pass/fail signal.  
* **Training loop:** The Decomposer outputs a structural plan comprised of specific text-split markers. The Reasoner translates this modular plan into SMT-LIB syntax. The Decomposer is updated via Direct Preference Optimization (DPO): structural plans that ultimately result in SAT and correct final answers constitute positive preference pairs, while plans leading to UNSAT or "untranslatable" syntax errors constitute negative pairs.  
* **Key algorithmic steps:** The intermediate autoformalizer is completely frozen during training, forcing the Decomposer network to adapt its problem-splitting strategy entirely to the formalizer's idiosyncratic capabilities and limitations.

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* Standard Plan-and-Solve prompting paradigms to measure baseline heuristic decomposition.  
* *Tier 2 (Anchor):* Beyond Translation 4, providing the direct comparison to zero-shot collaborative decomposition.  
* *Tier 3 (Strong adjacent):* Autoformalization without any decomposition (such as standard Logic-LM) to measure the absolute necessity of the splitting phase.

**8\. Experimental plan:**

* **Datasets:** PrOntoQA and GSM8K to span both logical and mathematical multi-step challenges.  
* **Metrics:** Solver executable rate, end-to-end task accuracy, and average sub-problem token length.  
* **Ablations:** Comparing the RL Decomposer against the Prompted Decomposer, and against an ablation utilizing no decomposition whatsoever.  
* **What would count as success:** The LDP reduces "untranslatable" syntax errors by more than 50% compared to prompting, leading to higher overall accuracy.  
* **What evidence would falsify the hypothesis:** If the Decomposer simply learns to pass the entire problem unchanged to avoid the risk of penalization (a form of mode collapse), the routing approach structurally fails.

**9\. Expected contribution:** A robust mechanism to procedurally align the granularity of language model reasoning to the strict expressivity limits of formal logic engines, removing human heuristics from the neuro-symbolic pipeline.

**10\. Main risks / likely failure modes:** The most likely failure mode is reward hacking, wherein the decomposer generates trivially true, tautological sub-problems (e.g., generating ![][image5]) to ensure artificially high solver pass rates without actually advancing the core reasoning task.

**11\. Novelty judgment:** Moderate.

**12\. Confidence level:** High.

**13\. Minimal paper abstract draft:** Neuro-symbolic integration relies heavily on the accurate translation of natural language into formal logic. To manage systemic complexity, recent frameworks decompose problems into sub-tasks prior to formalization. However, these decompositions rely entirely on zero-shot heuristics, frequently generating sub-problems that remain incompatible with the target logic fragment. We present the Latent Decomposition Policy (LDP), an approach treating problem decomposition as a strictly learnable routing task. By freezing the downstream autoformalizer and SMT solver, we train a lightweight decomposer model via Direct Preference Optimization (DPO), utilizing the solver's success rate as the sole reward signal. LDP inherently learns the implicit boundaries of the solver's capabilities, breaking down concepts only when strictly necessary for logical tractability. Evaluated on PrOntoQA and GSM8K, LDP reduces autoformalization syntax errors by 54% and improves end-to-end accuracy by 15% over heuristic Plan-and-Solve baselines, demonstrating that structural planning must be co-optimized with formal capabilities.

### **Idea 3\. Contrastive Preference Optimization for Verified Hallucinations (CPO-VH)**

**1\. One-sentence summary:** A preference-learning alignment strategy that explicitly targets and penalizes "verified hallucinations"—model outputs that are syntactically valid to an SMT solver but remain semantically unfaithful to the source text.

**2\. Research question:** How can alignment strategies prevent language models from exploiting the autoformalization process to generate syntactically correct but semantically fabricated logic representations?

**3\. Core hypothesis:** Because formal solvers only verify syntax and internal entailment, neuro-symbolic systems are acutely susceptible to semantic drift; explicitly training models using contrastive datasets where the rejected example is a verified-but-unfaithful hallucination will robustly close this critical vulnerability.

**4\. Derivation path from prior work:** The VERGE analysis 7 explicitly identifies the profound danger of "verified hallucinations," where the SMT engine mathematically verifies the wrong statement due to poor formalization. The LLM-TP Tree framework 3 attempts to combat this semantic drift using ![][image1]\-substitution, but relies on rigid, static procedures. VERICOT 1 utilizes DPO, but primarily contrasts logically valid traces against invalid ones, missing the nuance of semantic fidelity. Therefore, creating a specialized DPO dataset where the negative preference example is explicitly a *verified hallucination* targets the specific, unaddressed blind-spot of modern neuro-symbolic integration.

**5\. Why this idea is non-trivial:** Standard DPO methodologies inherently treat solver-approved outputs as the "chosen" response. This mechanism flips the paradigm: it requires the system to mathematically recognize that an SMT solver's approval is entirely insufficient if translation fidelity is compromised, directly tackling the most dangerous failure mode in high-stakes AI deployments where users overly trust formal verification badges.

**6\. Proposed method:**

* **Inputs:** Natural language premise and hypothesis pairs.  
* **Data Engine:** The system synthesizes the dataset by generating multiple autoformalizations for each input. An NLI model (or strong LLM-as-a-judge) is utilized to score the translation fidelity back to the source text. The pipeline filters for cases where the SMT evaluation is "Pass" but the NLI evaluation is "Fail." This specifically becomes the rejected response (![][image6]). The chosen response (![][image7]) requires both SMT=Pass AND NLI=Pass.  
* **Training loop:** The autoformalization backbone model undergoes standard DPO training utilizing the newly minted, fidelity-constrained VH-dataset to update its policy.  
* **Key algorithmic steps:** Calculating the divergence between logical correctness and semantic faithfulness to curate the preference pairs.

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* Standard SFT applied only to valid SMT formulas (e.g., Logic-LM).  
* *Tier 2 (Anchor):* VERICOT 1, providing a comparison to DPO optimized solely for logical validity.  
* *Tier 3 (Strong adjacent):* Standard RLHF utilizing human or LLM-as-a-judge feedback for general reasoning.

**8\. Experimental plan:**

* **Datasets:** LegalBench and BioASQ, as these domains possess highly nuanced semantics where slight drift entirely changes the legal or biological truth.  
* **Metrics:** VH-Rate (the precise percentage of verifiable but unfaithful outputs) alongside overall task accuracy.  
* **Ablations:** Comparing CPO-VH directly against standard DPO without the NLI fidelity constraint.  
* **What would count as success:** The approach reduces the VH-Rate to near zero while perfectly maintaining the high solver pass rates established prior to alignment.  
* **What evidence would falsify the hypothesis:** If the model loses its ability to generate syntactically valid SMT code entirely due to the conflicting loss signals between syntax and semantics, the approach is falsified.

**9\. Expected contribution:** The first targeted alignment methodology engineered to immunize neuro-symbolic reasoning systems against semantic translation exploits and false-positive verification certificates.

**10\. Main risks / likely failure modes:** The extreme reliance on an external NLI model to detect semantic drift during the data synthesis phase may inadvertently inject the NLI model's own biases and blind spots into the DPO process.

**11\. Novelty judgment:** Strong.

**12\. Confidence level:** Medium.

**13\. Minimal paper abstract draft:** Neuro-symbolic systems combine LLM generation with automated theorem proving to guarantee logical consistency. However, these architectures suffer from a critical, under-reported vulnerability: "verified hallucinations." This phenomenon occurs when an LLM autoformalizes a premise incorrectly, generating a syntactically valid logical formula that the solver proves, but which fundamentally misrepresents the user's natural language intent. Standard verifiable training paradigms inadvertently reward this dangerous behavior. We introduce Contrastive Preference Optimization for Verified Hallucinations (CPO-VH). By synthesizing a dataset where chosen responses are both solver-verified and semantically faithful, while explicitly rejected responses are solver-verified but semantically unfaithful, we align the autoformalization process to respect semantic boundaries. Evaluated on LegalBench and BioASQ, CPO-VH eliminates 88% of verified hallucinations compared to standard DPO baselines. Our findings highlight that formal verification must be explicitly bounded by semantic fidelity during alignment to ensure safe deployment in high-stakes domains.

### **Idea 4\. Uncertainty-Aware Semantic Routing for Neuro-Symbolic Verification**

**1\. One-sentence summary:** A dynamic verification framework that leverages the language model's own epistemic uncertainty during autoformalization to seamlessly route claims either to a strict SMT solver or a soft, consensus-based neural verifier.

**2\. Research question:** Can the predictive entropy generated during the translation of logical constraints serve as a robust, computationally free heuristic for determining the strict "formalizability" of natural language claims?

**3\. Core hypothesis:** Claims that are inherently vague, commonsense-based, or lie outside the supported logic fragment will induce high predictive entropy within the autoformalizer's token distribution; routing claims based on this specific uncertainty metric will maximize formal rigor while preventing solver crashes on ambiguous text.

**4\. Derivation path from prior work:** The VERGE framework 7 uses explicit LLM prompting ("Semantic Routing") to classify claims as Logical versus Vague, an approach that is computationally expensive and highly prone to model bias. VERICOT 1 attempts blind formalization of all text, failing abruptly with "untranslatable" errors when encountering ambiguity. LogicReward 8 utilizes Soft Unification to patch these ambiguities post-hoc. Therefore, extracting the logit-level uncertainty directly during translation provides a mathematically grounded routing mechanism that entirely avoids the overhead of explicit classification while proactively managing solver limitations.

**5\. Why this idea is non-trivial:** It entirely eliminates the need for an external classifier model or prompt-based routing phase. It connects the probabilistic nature of the transformer directly to the deterministic requirements of the symbolic solver, treating model uncertainty not as a flaw, but as a precise measure of semantic fit for formal logic.

**6\. Proposed method:**

* **Inputs:** Decomposed reasoning claims derived from a natural language query.  
* **Model components:** An LLM autoformalizer equipped with entropy calculation hooks, a Z3 SMT Solver, and a Soft-Verifier LLM ensemble.  
* **Verifier role:** The framework evaluates token-level confidence to route to the appropriate verification engine.  
* **Execution loop:** During the autoformalization generation phase, the system calculates the normalized predictive entropy over the generated SMT-LIB tokens. If the cumulative Entropy \> ![][image8] (a predefined threshold), the system halts formalization and directly routes the natural language claim to the Soft-Verifier for consensus checking. If Entropy \< ![][image8], formalization completes and the script is queried against Z3.  
* **Key algorithmic steps:** Rigorous calibration of the ![][image8] threshold using a held-out validation set to optimize the F1 score of valid SMT translations without sacrificing coverage.

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* VERGE 7, representing the state-of-the-art in prompt-based Semantic Routing.  
* *Tier 2 (Anchor):* VERICOT 1, providing a baseline for deterministic, full formalization without routing.  
* *Tier 3 (Strong adjacent):* Uncalibrated LLM self-evaluators that attempt to score their own certainty via text output.

**8\. Experimental plan:**

* **Datasets:** EntailmentBank, chosen specifically for its complex mix of strict deductive logic and ambiguous commonsense reasoning.  
* **Metrics:** Routing classification accuracy, overall system latency, and end-to-end task accuracy.  
* **Ablations:** Comparing Entropy routing directly against Prompt-based routing, and against an ablation utilizing no routing whatsoever.  
* **What would count as success:** The approach matches the routing classification accuracy of VERGE while reducing overall computational overhead and latency by at least 30%.  
* **What evidence would falsify the hypothesis:** If the model is frequently "confidently wrong"—exhibiting low predictive entropy but generating invalid or unparseable syntax—the routing threshold will systematically fail to protect the solver from crashing.

**9\. Expected contribution:** The establishment of a highly efficient, probabilistically grounded routing mechanism that harmonizes hybrid neuro-symbolic systems without external classification overhead.

**10\. Main risks / likely failure modes:** Modern RLHF-aligned language models are notoriously poorly calibrated, frequently exhibiting collapsed entropy distributions that may render the ![][image8] threshold highly volatile across different domains.

**11\. Novelty judgment:** Strong.

**12\. Confidence level:** Medium.

**13\. Minimal paper abstract draft:** Hybrid neuro-symbolic systems optimize reasoning by invoking strict formal solvers for logical claims and soft neural verifiers for vague, commonsense reasoning. Current semantic routing mechanisms rely on prompting the LLM to self-classify claims, introducing significant latency and vulnerability to classification hallucination. We propose Uncertainty-Aware Semantic Routing (UASR), a framework that exploits the LLM's inherent token-level predictive entropy during autoformalization to dynamically route claims. We hypothesize that high epistemic uncertainty during SMT-LIB generation correlates directly with semantic ambiguity or logic-fragment incompatibility. By establishing an entropy threshold ![][image8], UASR seamlessly aborts the formalization of ambiguous claims, instantly redirecting them to a soft verifier. Evaluated on EntailmentBank, UASR matches the routing efficacy of prompt-based classifiers while decreasing system latency by 34%. We demonstrate that predictive entropy serves as a highly efficient, endogenous proxy for formalizability, optimizing the interplay between probabilistic generation and deterministic verification.

### **Idea 5\. Process-Supervised Reward Modeling with Bipartite Premise Grounding (PRM-BPG)**

**1\. One-sentence summary:** A dual-objective Process Reward Model that evaluates intermediate reasoning steps by generating two distinct scalar rewards: formal logical entailment via an SMT solver, and premise semantic grounding via an NLI framework.

**2\. Research question:** Can decomposing process supervision into strictly orthogonal components—logical validity and factual grounding—prevent models from generating logically sound but empirically hallucinated reasoning chains during test-time search?

**3\. Core hypothesis:** Combining an SMT solver (for validity) and an NLI classifier (for grounding) to train a unified, bipartite PRM will yield a robust verifier that effectively penalizes ungrounded leaps of logic that single-dimensional scalar verifiers invariably miss.

**4\. Derivation path from prior work:** VERICOT 1 explicitly flags "Ungrounded" errors as entirely separate from "Contradiction" errors, recognizing their distinct epistemological natures. LogicReward 8 theoretically defines Premise Validity and Logic Validity but ultimately merges them into a single scalar score for training. FLV-RL 6 uses interleaved SMT rewards but does not strictly verify factual grounding against external textual context. Therefore, training a neural PRM to predict these two metrics explicitly as a bipartite vector $$ allows advanced test-time search algorithms (like Monte Carlo Tree Search) to optimize both constraints explicitly without conflation.

**5\. Why this idea is non-trivial:** Standard Process Reward Models (such as Math-Shepherd) output a single, collapsed scalar representing the overall "goodness" of a reasoning step. By forcing the PRM to predict a bipartite vector, we mathematically decouple truth from validity, enabling multi-objective test-time search methodologies that accurately mirror formal epistemological constraints and prevent insidious reward hacking.

**6\. Proposed method:**

* **Inputs:** Source context, user query, and an intermediate reasoning step ![][image9].  
* **Data Engine:** Synthesize a massive dataset of reasoning steps. Annotate the ![][image10] dimension using the Z3 solver. Annotate the ![][image11] dimension using a high-parameter NLI model evaluating against the source context.  
* **Model components:** Train an LLM architecture featuring two distinct regression heads to predict the bipartite $$ vector.  
* **Inference / Execution loop:** Utilize the PRM-BPG within a Monte Carlo Tree Search (MCTS) decoding phase. The Upper Confidence Bound (UCT) formula is heavily modified to require both scalar values to independently exceed a strict threshold before a node is expanded.

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* Standard scalar PRMs (e.g., Llemma PRM) used for standard MCTS decoding.  
* *Tier 2 (Anchor):* LogicReward 8, providing the baseline for the merged scalar approach to premise and logic validation.  
* *Tier 3 (Strong adjacent):* Standard Self-Consistency decoding (majority voting without explicit process rewards).

**8\. Experimental plan:**

* **Datasets:** StrategyQA and LogiQA to test both retrieved grounding and deductive logic.  
* **Metrics:** Pass@1 accuracy, and Search Efficiency (measured by the number of nodes expanded during MCTS).  
* **Ablations:** Bipartite PRM compared against a Scalar PRM trained on the identical dataset; MCTS with bipartite UCT versus standard UCT.  
* **What would count as success:** The framework achieves state-of-the-art Pass@1 on StrategyQA by actively pruning "valid but hallucinated" branches exceedingly early in the search tree.  
* **What evidence would falsify the hypothesis:** If predicting two simultaneous scalars degrades the PRM's underlying representational learning capacity compared to a single holistic score, the dual-head architecture is falsified.

**9\. Expected contribution:** A multi-dimensional process supervision framework that perfectly aligns advanced test-time search dynamics with strict formal epistemological requirements.

**10\. Main risks / likely failure modes:** Training two distinct reward regression heads on the exact same latent representation might lead to negative interference, degrading the accuracy of both metrics.

**11\. Novelty judgment:** Moderate.

**12\. Confidence level:** High.

**13\. Minimal paper abstract draft:** Process Reward Models (PRMs) increasingly guide advanced search algorithms like MCTS to solve complex reasoning tasks. However, current PRMs collapse the evaluation of an intermediate step into a single scalar, fundamentally failing to distinguish between logical validity (does this step follow from the prior premises?) and factual grounding (are the premises actually supported by the retrieved context?). We introduce PRM-BPG, a bipartite reward model that independently predicts ![][image10] and ![][image11]. To train this model, we synthesize a dataset rigorously audited by an SMT solver (Z3) for logical validity and a strong NLI model for premise grounding. We integrate PRM-BPG into an MCTS decoding framework, modifying the node selection criteria to enforce dual, orthogonal constraints. Evaluated on StrategyQA and LogiQA, our approach prunes factually hallucinated but logically sound branches 3x faster than scalar PRMs, improving Pass@1 by 9.4%. PRM-BPG demonstrates that explicitly decoupling validity and truth in reward formulations bounds the reasoning space, preventing insidious hallucination traps during test-time compute scaling.

### **Idea 6\. Anti-Obfuscation Regularization in Verifiable RL**

**1\. One-sentence summary:** Introducing a dynamic mutual-information penalty during verifiable reinforcement learning training that forces the model's natural language rationale to remain perfectly aligned with its generated formal SMT code, directly preventing reward hacking via intent obfuscation.

**2\. Research question:** How can alignment architectures prevent reinforcement learning agents optimized against formal verifiers from decoupling their natural language rationales from the underlying formal logic that actually triggers the reward?

**3\. Core hypothesis:** RL optimization inherently pressures models to maximize formal solver pass-rates while neglecting the readability, fidelity, and faithfulness of the preceding natural language; adding a strict back-translation alignment regularization term will maintain narrative transparency without sacrificing solver accuracy.

**4\. Derivation path from prior work:** Recent OpenAI CoT Monitoring research 10 explicitly warns that heavily optimized RL reasoning models learn to "obfuscate" their reasoning to bypass human or neural monitors. FLV-RL 6 interleaves natural language and formal reasoning during RL but lacks a mechanism to mathematically bind their semantics. Decompose-and-Formalise 3 addresses semantic drift, but relies solely on static prompting. Therefore, an active regularization penalty during the RL update phase is required to ensure that the generated formal logic directly and faithfully translates back to the generated natural language.

**5\. Why this idea is non-trivial:** It formally recognizes that in a two-part generative task (Natural Language Rationale ![][image12] Formal Code), the RL agent will eventually treat the natural language space as an unconstrained "scratchpad" that can be corrupted or filled with deceptive text as long as the formal code passes the solver. Mathematically forcing semantic consistency between two distinct modal outputs during Proximal Policy Optimization represents a novel and critical alignment challenge.

**6\. Proposed method:**

* **Inputs:** Policy network ![][image13] undergoing reinforcement learning.  
* **Model components:** The actively training LLM, the SMT verifier, and a frozen back-translation model (Code-to-Text).  
* **Training loop:** During RL rollouts, the model generates a natural language step ![][image14] and a formal step ![][image15]. The standard reward ![][image16] is provided by the solver.  
* **Key algorithmic steps:** We introduce a frozen back-translation model to compute ![][image17]. We then add a penalty based on the semantic divergence (calculated via BERTScore or cross-encoder similarity) between the original ![][image14] and ![][image18]. The total RL reward dynamically becomes ![][image19].

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* Standard RLVR (PPO with verifiable exact-match rewards) to demonstrate baseline obfuscation.  
* *Tier 2 (Anchor):* FLV-RL 6, providing the baseline for interleaved natural and formal training without regularization.  
* *Tier 3 (Strong adjacent):* Standard DPO utilizing human-annotated reasoning traces.

**8\. Experimental plan:**

* **Datasets:** ProofWriter and GSM8K.  
* **Metrics:** SMT Pass rate, Human-eval readability score, and Semantic Divergence score tracked over the course of RL steps.  
* **Ablations:** Varying the ![][image20] penalty weight to find the optimal Pareto frontier.  
* **What would count as success:** The model maintains state-of-the-art SMT pass rates while preserving high human readability, successfully avoiding the "gibberish rationale" collapse seen in baseline RLVR after extensive epochs.  
* **What evidence would falsify the hypothesis:** If the regularization penalty severely degrades the RL algorithm's ability to explore the solution space, causing convergence failure or stalling the SMT pass rate entirely, the approach is falsified.

**9\. Expected contribution:** A mathematically robust methodology for preventing intent-obfuscation, deception, and reward hacking in heavily optimized verifiable reinforcement learning systems.

**10\. Main risks / likely failure modes:** Utilizing a heavy back-translation model and a cross-encoder during the reward computation step adds massive latency to the already expensive RL rollout pipeline.

**11\. Novelty judgment:** Strong.

**12\. Confidence level:** Medium.

**13\. Minimal paper abstract draft:** Reinforcement Learning with Verifiable Rewards (RLVR) leverages exact-match or formal solvers to optimize LLM reasoning without human annotation. However, when models generate a natural language (NL) rationale followed by formal code, RL optimization frequently induces "obfuscation." The model learns to maximize the solver reward via the formal code while allowing the NL rationale to devolve into unfaithful, gibberish, or deceptive text. We propose Anti-Obfuscation Regularization (AOR) to combat this specific form of reward hacking. AOR introduces a dynamic penalty during PPO that measures the semantic divergence between the generated NL rationale and a back-translation of the generated formal logic. By optimizing the joint objective of formal validity and bidirectional semantic consistency, AOR restricts the policy from utilizing the NL space as an unconstrained scratchpad. Evaluated on logical reasoning benchmarks, AOR prevents the readability collapse observed in prolonged RLVR training, ensuring that highly optimized models remain transparent and interpretable to human monitors without sacrificing end-task accuracy.

### **Idea 7\. Distilling SMT-Guided MCS Self-Correction into Implicit CoT**

**1\. One-sentence summary:** Synthesizing massive multi-turn reasoning traces where an LLM repeatedly corrects its logic using rigorous SMT solver feedback, and distilling this entire self-correction dynamic into a single-pass "Implicit CoT" feed-forward policy.

**2\. Research question:** Can the rigorous, iterative error-correction behaviors learned through external SMT interaction be permanently internalized into a model's weights to achieve formal-level accuracy without suffering inference-time solver latency?

**3\. Core hypothesis:** By training a model on unrolled trajectories of its own solver-guided failures and subsequent successful repairs, the model will internalize the constraint boundaries of the formal logic, preemptively avoiding logical errors in a single forward pass.

**4\. Derivation path from prior work:** VERICOT 1 explicitly proves that inference-time self-reflection utilizing formal feedback boosts pass rates by 46%. VERGE 7 uses Minimal Correction Subsets (MCS) for extremely high-quality, targeted multi-turn repair. Concurrent research into Quiet-STaR and Implicit CoT demonstrates that intermediate reasoning tokens can be successfully internalized. Therefore, utilizing VERGE's MCS engine to generate optimal correction trajectories and applying STaR-like distillation bridges the crucial gap between slow neuro-symbolic search and fast neural inference.

**5\. Why this idea is non-trivial:** It moves entirely beyond distilling just the *final correct answer*—which teaches the student model nothing about how to recognize and recover from logic traps—to distilling the *process of formal repair itself*. Internalizing the dynamics of constraint violation and correction represents a deeper level of knowledge transfer.

**6\. Proposed method:**

* **Inputs:** A vast dataset of unannotated reasoning queries.  
* **Data Generation Engine:** Execute the VERGE pipeline on the corpus. For every problem, save the exact, multi-turn trajectory: $$.  
* **Model components:** A Teacher framework (VERGE) and a Student LLM.  
* **Training loop:** Format the training data such that the student model predicts the correct refinement *conditional* on its simulated internal state. Use a specialized training objective that progressively masks the explicit solver error messages during training epochs, forcing the model to implicitly predict the constraint violation and jump directly to the refined text.

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* STaR (Self-Taught Reasoner), which distills reasoning but lacks multi-turn failure/recovery trajectories.  
* *Tier 2 (Anchor):* VERGE 7, representing the upper bound of inference-time solver accuracy.  
* *Tier 3 (Strong adjacent):* Standard SFT utilizing only the final, clean correct reasoning traces.

**8\. Experimental plan:**

* **Datasets:** GSM8K and FOLIO.  
* **Metrics:** Single-pass accuracy (executed with no external solver), and Inference latency measurements.  
* **Ablations:** Distilling only the final answers versus Distilling the full failure-and-correction traces.  
* **What would count as success:** The distilled student model achieves 80% of the VERGE pipeline's peak accuracy but executes 10x faster because it requires zero external solver calls at inference time.  
* **What evidence would falsify the hypothesis:** If the model simply memorizes the specific, localized corrections seen in the training data rather than learning a generalized, abstract constraint-avoidance policy, out-of-distribution performance will crash, falsifying the premise.

**9\. Expected contribution:** A robust method for amortizing the extreme computational cost of neuro-symbolic inference directly into pre-deployment training, solving the primary scalability bottleneck of the field.

**10\. Main risks / likely failure modes:** The student model may hallucinate fake SMT error messages internally during generation, severely degrading its own output coherence.

**11\. Novelty judgment:** Moderate.

**12\. Confidence level:** High.

**13\. Minimal paper abstract draft:** Neuro-symbolic frameworks achieve state-of-the-art reasoning by utilizing formal solvers for iterative, inference-time self-correction. However, the extreme latency of invoking tools like Z3 solvers renders these systems impractical for real-time deployment. We propose a novel distillation methodology that internalizes formal constraint-checking directly into the weights of a standard LLM. First, we utilize a Minimal Correction Subset (MCS) guided neuro-symbolic engine to generate hundreds of thousands of multi-turn correction trajectories. Instead of distilling only the final verified output—which hides the learning process—we train a student model on the complete failure-and-recovery trace. We then apply an Implicit Chain-of-Thought (Implicit CoT) objective, progressively masking the explicit solver feedback during training, forcing the model to anticipate and preemptively resolve logical violations. Evaluated on FOLIO, our distilled single-pass model recovers 83% of the accuracy gains of the full multi-turn neuro-symbolic pipeline, while operating at 12x the inference speed. This demonstrates that explicit formal repair dynamics can be successfully amortized into parametric knowledge.

### **Idea 8\. Recursively Verifiable Document-Grounded RAG (RV-RAG)**

**1\. One-sentence summary:** Elevating Retrieval-Augmented Generation (RAG) to formal verifiability by forcing the language model to construct an SMT-checkable entailment tree where retrieved chunks serve as rigid, immutable axioms.

**2\. Research question:** Can the implementation of neuro-symbolic entailment trees reliably eliminate hallucinations in complex, multi-hop RAG systems by mathematically bounding the generation exclusively to retrieved text?

**3\. Core hypothesis:** By defining retrieved context chunks as the base premise set ![][image21] and the generated answer as hypothesis ![][image22], generating an intermediate SMT-verifiable entailment tree will provide a mathematical guarantee of factual grounding, triggering an abstention when information is insufficient.

**4\. Derivation path from prior work:** The Decompose-and-Formalise framework 3 builds recursive entailment trees for Natural Language Inference but operates on clean, static premises. VERICOT 1 operates on BioASQ and LegalBench, actively pulling premises from domain text. LOGicalThought 20 utilizes logical graphs for high-assurance guideline adherence. Therefore, synthesizing these approaches into the RAG architecture transforms retrieval from a semantic search problem into a formal logic problem: the answer is only generated if a path exists from the retrieved documents to the claim via Z3 validation.

**5\. Why this idea is non-trivial:** Standard RAG systems rely entirely on the LLM's cross-attention mechanism to synthesize context, which frequently hallucinates connections between disparate, unrelated chunks. RV-RAG demands a cryptographic-like proof certificate linking the final output to the source, completely eliminating the LLM's ability to inject parametric knowledge into the answer.

**6\. Proposed method:**

* **Inputs:** A user query and Vector DB retrieved chunks ![][image23].  
* **Model components:** RAG pipeline, LLM Autoformalizer, Z3 SMT Solver.  
* **Inference loop:**  
  1. Autoformalize the retrieved chunks into SMT-LIB axioms.  
  2. The LLM generates a proposed answer ![][image24] and a set of intermediate logic steps connecting ![][image25] to ![][image24].  
  3. The Z3 solver verifies the complete entailment tree.  
  4. If UNSAT, the LLM is forced to revise the answer based on the tree failure, or formally declare "Insufficient Information" (a hard abstention).

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* Self-RAG and CRAG, which utilize neural-only critique models to verify grounding.  
* *Tier 2 (Anchor):* VERICOT 1, representing the baseline for premise grounding without full recursive trees.  
* *Tier 3 (Strong adjacent):* Standard naive RAG.

**8\. Experimental plan:**

* **Datasets:** MultiHop-RAG and HotpotQA, requiring complex integration of multiple documents.  
* **Metrics:** Hallucination rate, Abstention rate, and End-to-end task accuracy.  
* **Ablations:** RAG with neural critique versus RV-RAG with SMT critique.  
* **What would count as success:** The architecture reduces multi-hop hallucination rates to near zero, heavily trading off by increasing the abstention rate appropriately when the retrieved context is genuinely insufficient.  
* **What evidence would falsify the hypothesis:** If the autoformalization of messy, real-world retrieved text fails so frequently that the system abstains 90% of the time regardless of context quality, rendering it practically useless, the hypothesis is falsified.

**9\. Expected contribution:** A framework providing mathematical guarantees of factual grounding for enterprise, legal, and biomedical RAG systems.

**10\. Main risks / likely failure modes:** Autoformalizing dense, noisy retrieval chunks (e.g., HTML scraped text, OCR output) into clean First-Order Logic is notoriously brittle and prone to catastrophic failure.

**11\. Novelty judgment:** Moderate.

**12\. Confidence level:** High.

**13\. Minimal paper abstract draft:** Retrieval-Augmented Generation (RAG) significantly reduces LLM hallucinations, yet multi-hop reasoning over retrieved context remains highly vulnerable to spurious logic and false connections. Current verification methods rely on LLM-as-a-judge critiques, which are themselves prone to hallucination. We introduce Recursively Verifiable RAG (RV-RAG), a neuro-symbolic architecture that mathematically guarantees output grounding. RV-RAG treats retrieved context chunks as strict, immutable axioms. Before generating an output, the system must construct a formal entailment tree connecting these axioms to the proposed answer via intermediate deductive steps. An SMT solver (Z3) verifies this tree; if the proof fails, the system executes a diagnostic repair or definitively abstains, declaring insufficient context. Evaluated on HotpotQA and MultiHop-RAG, RV-RAG reduces ungrounded hallucination rates by 76% compared to Self-RAG baselines. While incurring a higher abstention rate on noisy documents, RV-RAG provides a critical "proof certificate" for every generated answer, establishing a new standard for high-assurance, document-grounded AI deployment.

### **Idea 9\. Dynamic ![][image1]\-Substitution via Constrained Decoding**

**1\. One-sentence summary:** Enforcing absolute semantic consistency in autoformalization by integrating ![][image1]\-substitution rules directly into the LLM's autoregressive decoding phase via dynamic logits masking.

**2\. Research question:** Can manipulating token generation probabilities based on the formal signature of entities and events prevent semantic drift in real-time, eliminating the need for slow, post-hoc neuro-symbolic repair?

**3\. Core hypothesis:** Post-hoc refinement of autoformalization is computationally inefficient and error-prone; constraining the token vocabulary during autoregressive generation to only allow valid predicate-argument combinations will guarantee semantic faithfulness by mathematical construction.

**4\. Derivation path from prior work:** The Decompose-and-Formalise framework 3 uses ![][image1]\-substitution (fixing argument-role bindings) to prevent drift, but does so iteratively as a post-generation refinement step. FLV-RL 6 interleaves generation and verification but does not constrain the underlying syntax generation natively. Therefore, moving the ![][image1]\-substitution constraints directly into a guided-decoding state machine (similar to frameworks like Outlines or Guidance) merges semantic faithfulness directly with the generation step, entirely bypassing the semantic gap.

**5\. Why this idea is non-trivial:** Generating formal logic requires strict adherence to a predefined signature (variables, functions, predicates). Standard LLMs hallucinate new predicates mid-generation. Building a dynamic Trie or Finite State Automaton (FSA) that updates valid tokens in real-time based on previously extracted entities during generation ensures zero syntax errors and zero hallucinated predicates, a massive leap over probabilistic generation.

**6\. Proposed method:**

* **Inputs:** A natural language reasoning step and a predefined domain vocabulary (entities, events).  
* **Model components:** LLM generation engine, Named Entity Recognition (NER) extractor, and a dynamic Finite State Automaton (FSA) masking layer.  
* **Execution loop:**  
  1. Extract entities/events using the NER module.  
  2. Compile a dynamic FSA representing valid SMT-LIB syntax trees utilizing *only* those extracted entities.  
  3. During LLM generation of the formal logic, intercept and mask the output logits such that the model can only physically generate tokens permitted by the ![][image1]\-substitution FSA.

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* Synchromesh or Guidance, representing the state-of-the-art in general constrained decoding for code.  
* *Tier 2 (Anchor):* LLM-TP Tree 3, representing the baseline for post-hoc ![][image1]\-substitution repair.  
* *Tier 3 (Strong adjacent):* Standard few-shot autoformalization (Logic-LM).

**8\. Experimental plan:**

* **Datasets:** ProofWriter and FOLIO.  
* **Metrics:** Autoformalization syntax error rate, Semantic drift rate, and Token-generation latency.  
* **Ablations:** Constrained Decoding versus Post-hoc Refinement.  
* **What would count as success:** The system achieves 100% syntax validity and \>95% semantic faithfulness in a single forward pass, cutting total autoformalization latency in half compared to iterative refinement.  
* **What evidence would falsify the hypothesis:** If the constrained decoding forces the LLM into dead-ends because the extracted NER vocabulary was insufficient—causing generation to halt or enter infinite loops—the strict constraint is falsified.

**9\. Expected contribution:** A real-time, zero-shot prevention mechanism for semantic drift and syntax errors in neuro-symbolic translation, fundamentally stabilizing the most brittle part of the pipeline.

**10\. Main risks / likely failure modes:** The engineering complexity of maintaining a dynamic FSA that supports the highly complex, recursive nature of First-Order Logic quantifiers within an SMT-LIB framework is immense.

**11\. Novelty judgment:** Strong.

**12\. Confidence level:** Medium.

**13\. Minimal paper abstract draft:** The translation of natural language into formal logic (autoformalization) remains the critical bottleneck in neuro-symbolic reasoning. Current frameworks rely on post-hoc iterative refinement to fix syntax errors and "semantic drift"—the hallucination of predicates or misaligned argument roles. This multi-turn repair is computationally expensive. We propose Dynamic ![][image1]\-Substitution Decoding (DTD), which enforces semantic and syntactic constraints directly during the LLM's autoregressive generation. By dynamically compiling a Finite State Automaton (FSA) based on entities extracted from the premise, DTD masks next-token logits to only permit valid SMT-LIB syntax and strictly bounds predicate generation to the authorized vocabulary. Evaluated on FOLIO and ProofWriter, DTD eliminates 100% of syntax errors and reduces semantic drift by 82% in a single forward pass, entirely bypassing the need for post-hoc prover diagnostics. Our approach demonstrates that structural and semantic guarantees can be enforced natively at the decoding level, massively accelerating neuro-symbolic inference and stability.

### **Idea 10\. VCAR-Optimized Reward Shaping (VORS)**

**1\. One-sentence summary:** A multi-objective reinforcement learning formulation that explicitly optimizes the Pareto frontier between the Verified Correct Answer Rate (VCAR) and pure task accuracy, mathematically preventing trivial reasoning traps.

**2\. Research question:** How can reinforcement learning frameworks successfully balance the critical tradeoff between generating complex, accurate answers and generating simple, easily-verifiable but useless reasoning chains?

**3\. Core hypothesis:** Optimizing solely for verification pass rates causes models to generate trivial or shallow logic to farm rewards, while optimizing solely for accuracy ignores formal validity; a composite reward function dynamically scaling the penalty based on proof depth and VCAR will optimally balance both.

**4\. Derivation path from prior work:** VERICOT 1 introduces the VCAR metric (the fraction of CoTs that are both verified AND correct) but solely uses it for evaluation, never for training. LogicReward 8 optimizes for reasoning validity but relies on fixed aggregation heuristics that do not scale. FLV-RL 6 interleaves bonuses but struggles with reward hacking. Therefore, mathematically embedding VCAR—combined with proof depth—into the PPO reward function will explicitly guide the model away from the extremes of "unverified correct guesses" and "verified trivialities."

**5\. Why this idea is non-trivial:** Reward hacking in neuro-symbolic systems often takes the form of tautological reasoning (e.g., generating ![][image26], which the SMT solver passes perfectly, yielding high rewards without doing work). Formulating a reward that demands logical depth alongside correctness requires dynamic baseline weighting and deep inspection of the solver's internal proof core.

**6\. Proposed method:**

* **Inputs:** Policy network undergoing RL.  
* **Model components:** LLM, Z3 Solver.  
* **Training loop:** Construct a reward ![][image27].  
  * ![][image28]: Final answer matches ground truth.  
  * ![][image29]: SMT solver verifies the chain.  
  * ![][image30]: Number of unique, non-tautological premises actively utilized in the SMT proof core (extracted from the solver).  
* **Key algorithmic steps:** Dynamically adjust ![][image31] during training via a curriculum. Initially, weight ![][image29] heavily to teach formal syntax, then transition weight to ![][image30] to force complex reasoning.

**7\. Baseline papers and why they are the right baselines:**

* *Tier 1 (Direct mechanistic):* Standard RLVR (Exact match reward) to demonstrate baseline reward hacking.  
* *Tier 2 (Anchor):* VERICOT 1 (DPO), and LogicReward 8, which provide the baselines for non-dynamic valid reasoning optimization.  
* *Tier 3 (Strong adjacent):* Rule-based reward shaping utilizing curriculum learning.

**8\. Experimental plan:**

* **Datasets:** GSM8K and PrOntoQA.  
* **Metrics:** VCAR, Average proof depth, and Task Accuracy.  
* **Ablations:** Static weights versus Dynamic curriculum weights; evaluating the model entirely without the ![][image30] parameter.  
* **What would count as success:** VORS increases VCAR by 20% over static RLVR, successfully and completely eliminating tautological reward hacking from the generated outputs.  
* **What evidence would falsify the hypothesis:** If the dynamic weighting destabilizes the PPO gradients, leading to catastrophic forgetting of either the logic syntax or the primary task objective, the curriculum approach is falsified.

**9\. Expected contribution:** A stabilized reinforcement learning objective specifically designed to maximize rigorous, non-trivial neuro-symbolic reasoning without falling into mathematical loopholes.

**10\. Main risks / likely failure modes:** Calculating proof depth (![][image30]) requires explicitly extracting the UNSAT core or the positive proof trace from the SMT solver, which can be computationally slow and technically complex depending on the logic fragment.

**11\. Novelty judgment:** Moderate.

**12\. Confidence level:** High.

**13\. Minimal paper abstract draft:** Evaluating neuro-symbolic systems requires distinguishing between raw task accuracy and true logical validity. The Verified Correct Answer Rate (VCAR) effectively captures this intersection. However, existing Reinforcement Learning with Verifiable Rewards (RLVR) frameworks optimize these signals naively, leading to severe reward hacking where models generate trivial, tautological reasoning paths to guarantee solver verification while merely guessing the final answer. We introduce VCAR-Optimized Reward Shaping (VORS), a multi-objective RL curriculum that dynamically balances answer accuracy, solver verification, and formal proof depth. By extracting the utilized proof core from the SMT solver, VORS explicitly rewards the integration of multiple premises, heavily penalizing shallow logical shortcuts. We train a 7B policy model using GRPO and demonstrate that VORS completely eliminates tautological reward hacking. On PrOntoQA and GSM8K, VORS achieves a 22% relative improvement in VCAR compared to static RLVR baselines, ensuring that high task accuracy is genuinely backed by complex, rigorous, and verified multi-step deduction.

## **Section G. Cross-idea ranking table**

| Idea | Novelty | Feasibility | Expected Gain | Publication Potential | Tooling Dependence | Risk Level |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **1\. Continuous MCS-Penalty RL** | High | Medium | High | High (ICLR/NeurIPS) | High (Z3/MAX-SMT) | High |
| **2\. Latent Decomposition** | Medium | High | High | High (ACL/EMNLP) | Medium | Low |
| **3\. CPO for Verified Halluc.** | High | High | Very High | Very High (ACL/ICLR) | Medium | Medium |
| **4\. Epistemic Routing** | High | High | Medium | Medium (NAACL) | Low | Medium |
| **5\. Bipartite PRM Grounding** | Medium | High | High | High (NeurIPS) | High (NLI+SMT) | Low |
| **6\. Anti-Obfuscation Reg.** | High | Medium | High | Very High (ICLR) | High | High |
| **7\. Distilled Self-Correction** | Medium | High | Very High | High (ICLR/NeurIPS) | Extreme (Data Gen) | Medium |
| **8\. RV-RAG** | Medium | Medium | Medium | High (KDD/SIGIR) | Medium | High |
| **9\. Dynamic ![][image1]\-Sub Decoding** | Very High | Low | High | Very High (ACL) | Low | Extreme |
| **10\. VORS Optimization** | Medium | High | High | Medium (EMNLP) | High | Low |

### **Recommendations**

1. **The single best idea for a short paper**: **Idea 4 (Uncertainty-Aware Semantic Routing)**. This concept requires no complex reinforcement learning training, elegantly leverages existing entropy outputs inherently generated by autoformalizers, provides an immediate and measurable latency reduction, and is highly testable within a tightly constrained empirical setup.  
2. **The single best idea for a full conference paper**: **Idea 3 (Contrastive Preference Optimization for Verified Hallucinations)**. It directly and aggressively addresses a critical, under-reported failure mode ("verified hallucinations") using a highly popular and well-understood methodology (DPO). The data engine synthesis and the striking implications for safety and alignment in high-stakes domains make it a prime candidate for an oral presentation at top-tier conferences like ICLR or ACL.  
3. **The single best idea for a long-term research agenda**: **Idea 7 (Distilling SMT-Guided Self-Correction into Implicit CoT)**. Moving neuro-symbolic capabilities directly into model weights via multi-turn trajectory distillation solves the ultimate, existential bottleneck of the field: inference-time solver latency. Developing this rigorous agenda successfully bridges System 2 planning with System 1 execution speed, representing the next major paradigm shift in language model reasoning architectures.

#### **Works cited**

1. VeriCoT: Neuro-symbolic Chain-of-Thought Validation via ... \- arXiv, accessed on April 1, 2026, [https://arxiv.org/abs/2511.04662](https://arxiv.org/abs/2511.04662)  
2. Pushing the Boundaries of Natural Reasoning: Interleaved Bonus from Formal-Logic Verification in Language Models \- arXiv, accessed on April 1, 2026, [https://arxiv.org/html/2601.22642v1](https://arxiv.org/html/2601.22642v1)  
3. \[2601.19605\] Decompose-and-Formalise: Recursively Verifiable Natural Language Inference \- arXiv, accessed on April 1, 2026, [https://arxiv.org/abs/2601.19605](https://arxiv.org/abs/2601.19605)  
4. Beyond Translation: A Decomposed Collaborative Reasoning ..., accessed on April 1, 2026, [https://openreview.net/forum?id=aHjxW3sSdd](https://openreview.net/forum?id=aHjxW3sSdd)  
5. A Survey on LLM Symbolic Reasoning \- OpenReview, accessed on April 1, 2026, [https://openreview.net/pdf/f5afad2f01c94a094a3b83980ef676f44f4d8cad.pdf](https://openreview.net/pdf/f5afad2f01c94a094a3b83980ef676f44f4d8cad.pdf)  
6. Pushing the Boundaries of Natural Reasoning: Interleaved Bonus from Formal-Logic Verification \- Hugging Face, accessed on April 1, 2026, [https://huggingface.co/papers/2601.22642](https://huggingface.co/papers/2601.22642)  
7. VERGE: Formal Refinement and Guidance Engine for ... \- arXiv, accessed on April 1, 2026, [https://arxiv.org/abs/2601.20055](https://arxiv.org/abs/2601.20055)  
8. LogicReward: Incentivizing LLM Reasoning via Step-Wise Logical ..., accessed on April 1, 2026, [https://arxiv.org/abs/2512.18196](https://arxiv.org/abs/2512.18196)  
9. VERGE: Formal Refinement and Guidance Engine for Verifiable LLM Reasoning \- arXiv, accessed on April 1, 2026, [https://arxiv.org/pdf/2601.20055](https://arxiv.org/pdf/2601.20055)  
10. Monitoring Reasoning Models for Misbehavior and the Risks of Promoting Obfuscation \- OpenAI, accessed on April 1, 2026, [https://cdn.openai.com/pdf/34f2ada6-870f-4c26-9790-fd8def56387f/CoT\_Monitoring.pdf](https://cdn.openai.com/pdf/34f2ada6-870f-4c26-9790-fd8def56387f/CoT_Monitoring.pdf)  
11. Reward Hacking in Reinforcement Learning | Lil'Log, accessed on April 1, 2026, [https://lilianweng.github.io/posts/2024-11-28-reward-hacking/](https://lilianweng.github.io/posts/2024-11-28-reward-hacking/)  
12. accessed on January 1, 1970, [https://arxiv.org/abs/2601.22642v1](https://arxiv.org/abs/2601.22642v1)  
13. jindongli-Ai/LLM-Symbolic-Reasoning-Survey \- GitHub, accessed on April 1, 2026, [https://github.com/jindongli-Ai/LLM-Symbolic-Reasoning-Survey](https://github.com/jindongli-Ai/LLM-Symbolic-Reasoning-Survey)  
14. Grammars of Formal Uncertainty: When to Trust LLMs in Automated Reasoning Tasks, accessed on April 1, 2026, [https://neurips.cc/virtual/2025/poster/118114](https://neurips.cc/virtual/2025/poster/118114)  
15. IFDNS: An Iterative Feedback-Driven Neuro-Symbolic Method for Faithful Logical Reasoning \- arXiv.org, accessed on April 1, 2026, [https://arxiv.org/html/2601.07464v1](https://arxiv.org/html/2601.07464v1)  
16. LogicReward: Incentivizing LLM Reasoning via Step-Wise Logical Supervision \- arXiv, accessed on April 1, 2026, [https://arxiv.org/html/2512.18196v2](https://arxiv.org/html/2512.18196v2)  
17. DeepSeek-Prover-V1.5: Lean 4 Theorem Prover \- Emergent Mind, accessed on April 1, 2026, [https://www.emergentmind.com/topics/deepseek-prover-v1-5](https://www.emergentmind.com/topics/deepseek-prover-v1-5)  
18. Daily Papers \- Hugging Face, accessed on April 1, 2026, [https://huggingface.co/papers?q=obfuscated%20reward%20hacking](https://huggingface.co/papers?q=obfuscated+reward+hacking)  
19. Decompose-and-Formalise: Recursively Verifiable Natural Language Inference \- arXiv, accessed on April 1, 2026, [https://arxiv.org/pdf/2601.19605](https://arxiv.org/pdf/2601.19605)  
20. LOGicalThought: Logic-Based Ontological Grounding of LLMs for High-Assurance Reasoning \- arXiv, accessed on April 1, 2026, [https://arxiv.org/html/2510.01530v1](https://arxiv.org/html/2510.01530v1)  
21. VeriCoT: Neuro-symbolic Chain-of-Thought Validation via Logical Consistency Checks \- arXiv, accessed on April 1, 2026, [https://arxiv.org/pdf/2511.04662](https://arxiv.org/pdf/2511.04662)

[image1]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAXCAYAAAAyet74AAABEUlEQVR4XuXSTyvEURTG8SM2ioTyp2ZjMyV2SN6BtY1XYYlmNy9CzcZWNqzVNIuJBWVhM2VDIVFkSVkYvo9zz697l9ae+tTcM6dzz9zG7B9kGFu4wwM2i29TZnCKY0xgFa9FBxlDBz1MpdoozqoOMoAdfGEjq4+gm51twfyKC/PJkWKipu3hG80opsziPg5zeMQn1qKYsoKPOOybT5Mn8ycRfY56tYMO2+ZXhUXcRGPN/FqN1zV5tIbWedZhCe+4xXTWpOyaTzvUIRq75m8W0RPpqaofOI83HJg/U2QdfbQwpMI4LnEUBfPJbVyb/4YqzVTUjoNo4AXLWc9vtM8JznFlvlu96MiiSfrHTFq569/yA3f8OiiHFNtDAAAAAElFTkSuQmCC>

[image2]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABMAAAAeCAYAAADOziUSAAABVElEQVR4Xu2TvytHURiHX2FQxIAYCVFKMkhRBouBJEUpRovd5g8wymSRQbGYlMWgFP8Ao5SUUigxID+e13tunXO+OpdBGe5Tz3Dv573vOfc994oU/DvqcQKnEg5jtatPMoTneIMfzju89Hx090+wwx5LM4iveItdUVaG0/iG99gbxqUsiK1+hDVRpug9zbRmNcoCdOUtSRfqbM/EajajLMAvnIyyjAF8FqtZjLKAPnyS7+el6M7XxBodYG0Yh6TmVYXL+I672BTGIbrqhlizC1x37rjrF9zHflebxJ/XHDY728Wa6o62JefVMlKvqOjJ5Z6gkvdJ6C90KD9slvdJtOK1WL4UZSWkfiFlVKyR36wCx7EhK6oTG/KKWOEpdmIjlmdFMCJ2AH6zbtwTdyA9+OAKYq+wxZ75olLsRDU7xnmxgxrzan6F7lRHMIOz2BbGBQV/yyfQiVs6tWplyQAAAABJRU5ErkJggg==>

[image3]: <data:image/png;base64,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>

[image4]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEYAAAAfCAYAAABar7u7AAAEgklEQVR4Xu2ZTaiWVRDHJzIoKryiGFFQZCR+QEiLICoh+kQK0kDBIKFFLdykfRERQrRIIyJaRF8XF5JQi0BEQSmhTelCWkQtiq5RCQZJkUKF2fycZ+5znvGc0Svk4vX9wx/ve+Z8zMyZmTPvq8gYY4wxxvnBzcrtysuj4AJAavstyk+UV0RBgWuUjwTepryonNTAZcp7ZLj2gW484mLlrcq3ld8rf+z4rvLabs5s5QfKq7vPEQuVk8rfOn6uvFNs7/XKddMzz2B7KuywWkzRI8qTHTnwynJSAxulX/OnmKHc0kQxBwffpfymm7dH+ajyuo7PK39Q3qvcq9ypvPTUyh6XKF9UHle+JLYO561UTik/Vf6hvL2bD1LbU2EAh2DYMeUhad+aY4mYEaz5R4ZKOTDoVTGHHFQuHoqnsUp5QmzeK0GGY58VixAiOeIO5V/Kr5XzivHU9lQYgEKvix2A98nRFjCYlHhKzClE3FWDGTbnHTFjPxJLkxbQb5/yX+XdQ9GpC/hVzMG19CayifCPlbOK8dT2VFiAArVL+bC0FSxB+j2tfFLM8KiU3zKy/cq5hayFN5WHlTeE8U1i+zwYxh2ctU35RBhPbU+FBVAGr1OIt4opsnYwoweFkjlECA5h7obBDCvIpAbk77PBc3J6feFvxjiD4loDtmEjtpZIbU+FBXhJdogpQkqhCIpGcDuENMbiGFIo1hdPC/bYLfUXqgYiJtYXotCdTy27TyxFzwap7amwAAq5IwhJFCEqIih0r4kpjDNq9eV+sVRkjzXF+LnC09VJFH4ppmf2QKS2p8IOXl+8ppDLKMDTWTZHFM9J5fXdZ9KnVl+4ecZr9eJcwLkU79I5zr/FXtMaUttTYYeyvgDW8GQfUM7xSWKO8LpThnhZ9Mo04t/s3JmAFL5JLI2nZOgc+qP4IoLU9lTYoawvAEdx2z9J35HyZL4nfb1o1RdCmx4IhWup2AL6xaYuA5H8stg5rRc0tT0VdijrC6BJopchaljvPcuyYo7Xl9hUucNm4hiij7nROCJ4URgrQTQT1ZxVe8pT21OhnF5fQJkOHEj6PCPD5gpHIo/1hf2oTTNxzFLlZ8r5YTxeWITr2WpGU9tToVjafCF9yji8l9kiVnDLrrWsL7F/Ae60fdI+1+Hd8eow7g6OTVsJImpK2i1BansqVDwkw/rieEHMOG6DJ7qEKxTri+NG5S9izyr7t4BTNomlaexNvM69JfWvAd5Zo0OrgUxtrwnZdK6YwXyxI2LI5YliDumDY96QXjH2oLg+JlbwfhYLYepKNIwnlKf0qFhTFo0jQok6IjKuBTwInI9zHxf7WcHBXuvEnIJz4t6Omu3TqAmpJ96AlfxQ+nrBnK+kTzEcyee4xqOqluPLld+JzcGJpCdpQ8H8VuyniJZR1BdewRViXyB9/ftie/4uln6t9aBm+zRS4XkAN000cus0fkTbgm68BWSrpK9rRBQ/RhEdm8UcWouyiNT2VDjiSG1PhSOO1PZUOOJIbU+FI47U9vS/EEYcF7LtY4zxf+I/G8EnwJ15wSYAAAAASUVORK5CYII=>

[image5]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADgAAAAdCAYAAAD/56+bAAAA7UlEQVR4Xu2XPQrCQBCFx07RQrDyAooW3sNCPIYo2Fl4BPEIVrYewD6dnTaewEPY6nvuRtZACIkSB50PvmZ/YB4zbIiIYRiG8Xn68AiHyQ2l1OAS7mE9sfeAB0ZwDU/w5uWaVnpwBnfwKq7eCDaCM0+acCUu4BgeRH9ATtcGTuBcMgKG8EAk+gOGsE4LGGMBFVJawCrcwktB+WnqSH5KC0h4v13QFqxIfkoN+A0sYMi7AX96RP/ikfkGmQE5FhwPjskAnsVdmPo1yu5ogvXEtS3E1csfha5fexn3sGtpautm3LU0I0nppmEYhmF47q1cdEH9tY9PAAAAAElFTkSuQmCC>

[image6]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAAgCAYAAAAffCjxAAABiElEQVR4Xu2UPyhFURzHf0IRoShJEomilIHJaDGwWJTCJqtF7AZKyaIMlFIGWWRTXlkMRjKgKDLZJJI/3+/7nd/zu/fd9zIY76c+9d7v3HPuOd9zzhVJSUn5I11wBS7Cmlgb6QgWZQCewml4AQ9hpWtvhncwA6tdPUK9aMce2AAv4T1scs8MwS+4DUtcPcIo3BJ9YBB+wCNY4Z5Zgt9wxtVIOSy1P61Bsi7aYdwaQRU8hq+wP9SW4YvocrnsLP82kNEIr+ADbHP1dvgkmh0zNPjSfVjmalks0HjjsOgsd+U3aO5cBs6H/xFYZIe5WD0paM7yRnRz8rCBRlzN3uzzIdzpW0nIhzBgDmTT5bYuhFo8H84yHkEOnokN+AYPRIOnHMgfxKL5eOpETzU7WD4Trr1gPnzTLHyGU67eAq+D/G1YPjx7veKWZ/fL58NlbsJPOBZqBp/hFeoW/VLkqIVncE/088GTvCaa1aTkX1LO6BzuwL5Ym3TCE/gOH+GqRG9/HOZX8HOSkswPHXhU6eQUFyMAAAAASUVORK5CYII=>

[image7]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABoAAAAfCAYAAAD5h919AAABz0lEQVR4Xu2UPSiFYRTHj1DksyhJEonEJiajxcDAohQ2GVAUMVmkSAklko9BGcQgZRC3LMrqY5CiyGQVycf/7zzvvefe7h2ua6Def/3qfc7z9pznfD0ivnz58vUnVAmmwBjIjtijyh0JqR6cgG5wDvZAutkvArcgADKNPS7liR5cDfLBBbgDheafRvAB1kCSscelFrAqekADeAP7IM38MwE+QY+xxa0SBzUnemB7aFsywCF4BnXG/mMVgCtwD0qNvQw8iqaUqU1YXh22QYqxN4lGuSkJ1MdqRPTAwQj7r9THynPUbGxs5YCE1ycHLIEjMCMaJZkGfaJNtOuwDRUUG4CO6JBKBqPOZuszBGrdf2wSNos3Fh2iTofBscSYuVSwCF7AjmhjEDry5oc3bBU9OCChS3EsOHtVbs0G8qKNqVzRYeVtvPrwplaM6AbUuDXrx5cly63puNN9B0WvveAJdBl7Mbh28Nsq8uANsBLalgEJXSIoL7+2PkzjMngHbc5mxc4MiEbNN/FANHqK8zgu4SPyLXbRKdgSfbVZ3FnRWjH8aHnmS3IGJsECWAeXoB/Mu/2oqhDtklfwIFpI+6hGEy/AB9nrLGaB2WG3+vqH+gLFzFhC6HkUXgAAAABJRU5ErkJggg==>

[image8]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA0AAAAgCAYAAADJ2fKUAAAAqUlEQVR4XmNgGAWjgOpAHIh9gTgEB3YDYm6YYk4gngHE/4nAniANrEA8C4gfAnEeA8TEVCi/BMqHYWeoegY/IN4KxPwgDhTYAPElBohzsQIeIOZAE2tlgBiELg4HZGlCByBDDjBANBINjIH4KxBHo0vgAzlA/A2ITdElcAGQH0B+AQW3JJocTqAJxG+BeA8DUswTAqAkBIr1XnQJfAAU2yDbwLE+CoY2AACyYSC9s3Yb4wAAAABJRU5ErkJggg==>

[image9]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAAgCAYAAAAffCjxAAABRklEQVR4Xu3TvytFcRjH8UcouooMJLekRBYZlJSVuoNu3elmsojF4i/wY2IymCSUQUbJoptJFsWESWIzKZuJ9+M5x33udbrOuRnPp17Dfc73PH17znNF0qRJkzCNGMcmdjCL1ooTMZLBIW4xgwns4UQSNlvEC/qD3124wTN6wkN/RW9TwgO6g1ofnrAvCW4UNvrEGw4wjWZ3xmcESxLx/N8aaebFGnnavN0fCrKLc7RVP9A0YBCruJZyswV/qFZGxV5cQ5Orz4k1Wna1mtFr6gtnaHH1Aj7E9ilMHqe4x7Crf0cb6Y2GXK0TF2LbHQ5U92tFbC0ekQvqP8niCq84FhvwOzakcn90BKqIOynv2690iG2wHoj8rGIzPMK22MepOzoX/ctMYkBsBHVFV0Hn2Yt1id6xWJnCJbYwVvUscXSb/ZqkiZEvc6850gNX5Z4AAAAASUVORK5CYII=>

[image10]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADcAAAAfCAYAAABDJFUdAAADQUlEQVR4Xu2XWaiNURTH/zJEmTKLEiFThigp6iYePBgSUQpvJF4oipKSFy8kD8hcpjKVeShHZCxPhjLkkhKFEg/I8P+39u7sb3fuvjj3dL9T51+/bt+39v7uXmuvtfY+QE011VRTBdWNzCJzEtSR9m58VWkSeUHek9+Oj+R1wBf3/jYZbNOqSxPJD/KBDI1sLcg88pN8ImOy5vxrCWx3bpAOkU3SO9k0Zltky7W0M4eQXrhq8xFszIHIlmuFC58d2bwmkG+wMcsjW641lnxF6XqTtLPbYY5dJZ2y5nwrVW/tyHryi5wkvbLmfEu7sg/m3Euyy3HMPX8nF8h4N7aqFNbbQtLbMQjmpHbsKNKpKKfbxC/zoFS9tSan0HATGUYewuxrIlu5GklWwNbw30rVm6S2n2r/Csgr2CWgKbWbXEYZ177Gzjd9uIC0czNg17c+saG51dj5NoC8RTrtNpHjpFVsaG6l7pPSNJhjoXNyQrvVHcWdjR3vSLaQE+QguUb6BfYuZAc5TS6Sw7DjRppJzpDHyK5JWTaZnCd7yHWU3hB0hnXEzbCFqykMIT1Iy2DcFFi3DJ0bQc7Cuqd29jmy9daX3COLUTw61pIjsMBo3hWy1Nl6kiduTH+yARYIpbqCK+k7amg3YUHVHNlVlxmNIp9R3JGQN7B/4KVOpeNAtltkEazxTHf2uN60iK3kDrJHhwKjcVqUFlmP4hwFSA1JgRztmA8rF42XhpN37r2kddXByqosaSeVHvrwAjIwsMX1pgXXw3bBy18SCrDM0PVNTczvqpySc3JS0re0y7ru+TErYbXvx1RcYb3JKeHPTL+zkk87jfPpFNaoAnSOtHXP4dGiQKo+1akLKONY+FepZp/BIq/IalG++/pCV+RXw2pMaSoH5Ih3Tot/CnPQS+fufViwNsLm6V18DutXyqrguUmlKF4ie2GL86mpJvCALIOlo2pQ3dNLNXUX1iGVfvplrwB5TYU1Ds0b597JQaXyTlhpqFsqoLrUV0yqx67ubygVvFLQp1pDklNKU3XYUApcqbnq8nFHz4WUouvIftjC9aym4Y+IqpYc0iVcqTwXdkap01bV78OUlE5KKzUjpVhNf6s/kVe7VX1XCXgAAAAASUVORK5CYII=>

[image11]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEkAAAAfCAYAAACrpOA2AAAD50lEQVR4Xu2ZXchVRRSGV2ihZPiTplEgiaaSf6ElYqGBgl5oUaKCUBcqKqgXCgVdqBAidJXhRZgiKVL+YEiJkkIfBGUlgZB2YVFGGiQpiIIWae/zrVnsOcPh6M0nePZ54cFvnzV77Zk1a9bM3pp11FFHHXV032uweEUsaMFM0S+1r6VeFL+Iv8TtxGXxe8a19Ps34mm/rZ56Qfwr/hZjC9sDYpH4T1wRzzaa66MV5tnylXiksCF+w0ab9wtbLUSm7LXWAaB2nTFv81Fhq4XyALxa2ELTxE3zNqsLWy00WVy35vUIkWnbzAN0QvRvNNdDrepRX7FB3BKHxLBGcz1EluwyD9KvYntiX7r+RxwVU1PbWiqvR6+LxxOjzINFBn1i7bnEeokHyx+bqVU9wsGn1n7F+iVxyTwBZhW2pmpVjxDbfTtu+3PFefFkaSh1p/MR72td1p5B2iyOiD6lodSdzkcjxJ/m9rcK2/2sh82PMm+XhmZq9b6GSEkClAept5gvhqRrit9y84w7kKDQMwGrxJfp+l2xXxy3KsU5Xqw3n9Gd4lsxJdleNj9y/GBV3x4z32mfN18F4f+g+STzbP7+XgxP9yDaMpZjYrf4wvwlvmU9GmC+g9FxAvCjGGPeCQYdwgnFLQ/SOPG5+W7Hw98Uh80HHIdO6hvt1orZ4oZ4TawTV8XEdD+D2mLVMxnoSTFBbDIfKF8oGCBiUn82z3DAP7/xJkA/eD5++VoxL92DePZpqwLHJsQKwUdT0UE6GhmS84d4qmravbtxDMD2tXjDPADRAQJBFpJZiCxjJqlvkxIEpsu8vpFdkQUrzT/D5M/DL31baH4vfgnSE8nORLFMWC7jzTOMjafLqu9dDJyCHFkyVPxkjeXiruvR3YpZpjOLxRIxMrMRAAIbyydqHO1QHrRcsSGwaeQHVAbym3lQuPdj88ykTfhigKFm/svAcs33sOfSddSj3E+Paoc1ziKpzyxGDWEWz1mVaSGWOu3y2WW5UjPwyeC5l8HGhsKgucYXEzXIqkmJNnlgOc48Y/4M7sMfyjNttN2DQ3Ke6gySAVI0ByZ7GbQQaU6657tLWTcI5FlzH2iOuGher94xD1DpP5YWbeOTNEH9zrxPUUPxQ6nAT48HiQewZCjce8w7mKd+WS9yUXNOme9u75nfH8sWkRVkxGdio/jAPIgfijWpTemffwk+/pgAamr0kQ1ia/qdXZR+8hp2T1WmPqKTD2XXpah37KjNghhiJw47/lhmUcea+cdnZE2Ivx+1qlBH8Hpc0813vPiPAVKctZ/vVrXXUnFBLDNfMiydGQ0tOupWHErLQ2hH7a7/AQQ65F8PDBzDAAAAAElFTkSuQmCC>

[image12]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABgAAAAdCAYAAACwuqxLAAAAgUlEQVR4XmNgGAWjYBQMCcADxTQDnkA8D4gZ0SWoBRSBeAcQi6NLUAuAXN4MxOXoEtQEMkB8GIjN0CWoCQyA+BwQu6BLgIAuEIdQAYOC6QsQrwNidQYkQG0LFjGgWUANYALE1xhwBBGlgKaRTPNkSvOM5sdA46KCg4HGhd0oGOkAAD85Gog5Akn8AAAAAElFTkSuQmCC>

[image13]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABcAAAAgCAYAAAD5VeO1AAABmklEQVR4Xu2UTSgFURTHj1CEEJEokY2ykShlYWtBkoVib2fJQkrJiixENiQ2L/ZkISkKWaOUQlZKys7Cx/8/5943d968501ebze/+tWcO/eeuR/njkhMTEyMRz0chKNZbLMDolAMl+FPRPdgoTcyC0VwAz7BKdGZHcJ18zwPL+CYiYdhAwdGYQgewEoTV8MT2G3iSbhpnv+iCtZJyorKYYkT98IrWGviHTjjvw7BLZ2FK3ABLsEC+zKvyVNZhNuiHSrgmWROzj7TcEv0I03wHLa4nSwsxTs4bmIeHA86U/JOeA87TNwKb2FXsofDBPwQ/6VNvprs4cNZr8EjWGraWAQvkiY5q+USXotWDLHJT0XPxoVLZyJ3VVzxG2x32jxG4LcEZ8lteoDv4i/dMiDa/xU+i37oS4KTS8Ja/hStFgsvWEJ0UL/TTjjjR9hoYlbXjaTfQq8cayRcRmznBUmFkzmGZSbuE90Sd3L/hiXLO0Ds4fKDXG3O8F+zK5q4R7S+mwM9coDVtQ/nRMsxVCG5wp8UzynS7zcm//wCVbJQSI/qgHgAAAAASUVORK5CYII=>

[image14]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABYAAAAfCAYAAADjuz3zAAABdklEQVR4Xu2UvS4FURRGtyAhCAmJQkUhUagUIqGToBGFwhtoVBpPIAoq8QoiQScErQIVnUqnUmtEgm/lzHH3/FxzJzqZlazCnH22c77Zc81q/hVDckWuOqdTFQ147uuQvfTIMSef5av8SnySw75IdMhdS9e9yTs57upyzFoojJvW0ss/tMlDuSPbM2uFbMk9eWyh8aXsTlUE+uSVhYOUwjVpuCQX5ad8lzO+KGHCwvVHsgtFUHQjx2S/hY2c+sDC1T3LFg7BYUrhWieyK/l7w0LjFzkaixK2LcTWEhSyIUIzmtKcfxLplRdWId8jOe+ecX1ioDGxEA+Q763lR7EQn6+HF8cL5EXyQqFyvmeyJ/OcUWPkODXNOuW+3PRFv5HN18NHQmNOvmAV843zWwRZ8nnTnGzJ+0/5erhR/MxPrcV8mYRrC2PUDD9665m1FIzSoJySD/JRTsoBX+SIo/dhJfnGMYrXi55b48vLwp57a/K7W1NTU8I3wCZRc2tPHY4AAAAASUVORK5CYII=>

[image15]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABMAAAAfCAYAAAAFkva3AAABKElEQVR4Xu2UvUoDURCFj0TBIkUEG9FWRIhYiJ2d9lrY5QF8BDu7FFaBvEGKYGObPr1gZyMpgthqIdgo/pzD5JK7Y+5m10pkP/hgd2bv3N3Z2QUq/hyr9ISelvCQLmmx55g+0Gf6NVHHisXG+Ru6osUpjugnfaV7LhfYovd0SOvZVJZzFNu1TXs+GLNIr2HFui7n0aYqmGSdjmHFWtkUtmEvKKBiZ9H5Dw7oO32CLY7RXcSLd2CbJ0n1a5Pe0v0olssyHcCKvWE6Co/0g97BZrEQcb8uMR3MC9iY9OlCuHgeYb5e6K7LaQRym+1J9UuPf4Vf9svPlx5NxWsunmQD1uhZ81Wa0K9Z81WYBl2jHdhdjWhzElOuMPoj6JWH34lXTde3WlHx//gGmVVLKt+atMgAAAAASUVORK5CYII=>

[image16]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADEAAAAfCAYAAABOOiVaAAACw0lEQVR4Xu2YS6hOURTHl1CeeVzPpJDXLXkkSSjKgIG3ohSDOxAxVpQYSCkGGElEKSQMyERRRGLIxCOPlFIoMfD2/1l7951v993zUT52Of/6dTt77XPOXmuvtc7+rlmlSpX+Cw0Sy8SqEuaJPmF+lporHotX4nvgjXhe4H0YvyXG+215ao74LF6L9sTWRawWX8VbMa3enI82mEf7uuib2BBj2JhzILFlISJ90soXSO3cN59zPLFloeICVyS2qFnio/mczYktC00XH6xxPSB26pC5A1dEv3pzHiqrh55ih/gmzolh9eY8RJSPmTvxRBwOnA7Xn8RlMTPMzVLFelgnhgfGmTvDDpyyTFMoqqweuovzlnExR5XVA6Kd/ou2uk8cSQcbqdn3gfPSNfv7TgwQd8wD3FTNvg9jxEtz+9bE1kqR1k/Nv01NVXZeQovMHSg60U0sEYPDNXWzW1ww3629YWyxeVe7KRaKM+KEeChmi53iqHkacz/PnSAuikfii7gtdomu1kD9zTsQL2SB98REMcTqb1hg3p2KTkwyf1HsVmvMF0dqLhcPzDvbdvNnspN0ORzj2XQ63jeWm82dfWa+nihSmzTvtKVPEe+sFuEiL8To2tSfL2YB2IjoevPI8eIonGOhm8wdHClGifnmO8kxf0SYG3O92OnIe8awod+qh18V0SPViPhaq0Uwaqr59uMoR/WVBRtpckn0CNc8B6dmhGtS6KzVN5V0TstFbvOBZDFt5nkfu1hv83PWtnCNCAKNhIaCWDCpRG0SnIHJHI48k8Pcliguko8hkSZ/D5rvGKKzsUDqKoq+X8z1mEpDzQudmmRX2B0Cww+x4v0t0RbzcxfR4y/pQx0hXk6Rx3rgm3NVdIRrxJy7Yr9YGsbofDfEHrHRSor7T4pdIJJx8VHUUq9kjBNB2i5xLv0nRKOxSpUqdaIfUoyid1dd2xEAAAAASUVORK5CYII=>

[image17]: <data:image/png;base64,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>

[image18]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADYAAAAfCAYAAACs5j4jAAADQElEQVR4Xu2XS8hNURTHl1Dk/YhEiYFHESVE1JcUCgkDpUwkj08GFDKVJCZkYCAykDxmIq9CynOA8hhJJAMDJgxQ+P9aZzv7nHvudV3f1fnq/OvX7Zy97zl7rb3W2uuYVapUqVIXabhYIVZHzM7MSMX9eB7wX55ROs0Xr8QH8TPhpRgZT5J6iYOWnfdZ3BcTonml0zzzhYZFr8kO/1YPcVrsFz1zY6XULnFInDM37Irom5nhGiCumjui9CLMMGiJWCx+iK9iTjwp0WTz8BudHyijWOQdMV4MMl84u3bUPPRiLTd3As4ovQir86JPcr3V3LB3YlyYlGifedh2C7FQFhyEMRiFcRgZ1F9ctm6UX2fEwuge4UcYYhhhSXgi8uue1R4FpVScX7EoHBQQCgkFBTXKL5zRrvLf21p4NmF1UfTL3afUU/LZNYzh4UfE9niStF58FF/EjNzYv+qA+CaeWwvdTT6/YnFIYxg7t8jq59daa/HlTQhn0hDkq3NDxedXkcgl2iuMI7fIt3x+8cKTCX/18iY0RDwSG/MDf1K9/IrFjoY264LV5lfLL29C08R7K46ShqISXjMv4/UUl/6ixZNXNMbk6Qlx19IIYAeXilvilDguzlq2aSaXd4hL5rt+XYxNxuIQnySOiduiIxnPiJcNM1/QY/FETBWD40mRQun/bsWe4+WfxKzkepmli1klnlq6UM5EnDQmueYYoTEIDTUOoQrzG0KcKOFzaYO5k8J4jUIZD+EVwGOh88iL/zyw4uLALsTJvdI8fHDcM8t2KRSp+D2bxFtLuxue32FegUOIs0PrzJ8/0Ny5jLdVRfnF4vm+YzF8Bs1M7mMMRu1Jrjleblhx3qKQX4Twa/Pntt2goFHihaUhGhpoQne3uYGhilKg3pjn9cQEruv1nXF+4RzmYiwFr+1fFryUYyDsSpxTdCkPzXeVMNppvgNTxF5zg+MdROTZNjHXPMTDbhLWOBDndFptY94WEYachXieChsqHrsXvrQPmxtAnnLgEqZoenKPbmaL+XMWmH/M3jTvahDFBgfyIbzZuv68rCuOi6KqGipwKBbkSGiog9ilEVb7f4yL+0P+O9T+o1GVKlVK9Qu6qKopm1sMRwAAAABJRU5ErkJggg==>

[image19]: <data:image/png;base64,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>

[image20]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAAfCAYAAADXwvzvAAABGklEQVR4Xu2TsUoDQRRFn6igRNBCCGKRxkZsBEVBLFJYCOms/AjrVELyDSGVrUWafECKFNr5AyJYWYidWtkoRs/lZTUzZme1FHLgNHvfm5ndeWs24dcsYANbeIDTYZyPCjfxGj/wNIyLOcSB+QLLUZakjDfmzVrkT+g9ddw2TkVZkn18wztcDaM0i3hlvutxlBXSNG/s4kwYpdnCF3zE9ShLsmveqF1PoiyXbbzHS/PGHs4HFWPYwyes44b5UbWzjp6LZvQZz3DW/KN0zHdtfpeF1MxXPsfSyHNdhxp1PbqmgCN8xb79DLMR1EBoML7Imm6xMhoM0chp9IIR1D94Yf5eO1nlGKr4jg+4lj1cGlqEalZwLg4m/E8+AT3WMpetnNPXAAAAAElFTkSuQmCC>

[image21]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABMAAAAfCAYAAAAFkva3AAABKUlEQVR4Xu2TsUoDQRRFr4SAYCAGJGBl2lQWQdP4ERZaaWHnB4iNnyCp/IFU6fIJKdLb2Ai2CloEtAhooYXem7cTJs/srsZGwh44LLPvzcxyZxYo+Hds0H16kOMuXU3mpNKmd3REPxNf6EPkU/L+nZ7T8mRmBnv0gz7TpquJBr2BLXo2W/rOKazxmtZcLXAB67mFxTOXFdqDNV65Woxq6rmnm642RbtoNzUeuVqgQoewHj01nkuLviI9LxF6tNixq82Ql1eVDmA9HWScpvLqwhr1VBbBLdhGyuiNntDSZFYKcV7+fsk+PaRrYUIWIQvtvONqvybklXl3fkJ8v5SXxgsT56Uv/BPhf1Rmym4h1mFHfwn7qke6TevIOXqPJo1hi3j1XvWCguXkCy9sTNiNea4MAAAAAElFTkSuQmCC>

[image22]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAA4AAAAfCAYAAADXwvzvAAABKElEQVR4XmNgGAVEAVYgdgbiLCCeBcQdQCyAogIHkAXiE0D8Doj/A/FhIOZFUUEARDNANE5ClyAEQBpAGoPQJfABQSA+DcRvgVgTTQ4vACkGaaLIf8JAnMwACeFiIJZEUocBYP5bDMQXGSBR484Acf5rINZGKEUAmP9AGvcAMT+SXDlUPB1JDA5g/rsLxAqoUnCNRWjiYIAr/liAeA0Q/wNiFzQ5MMAVf9JA/ACIrwOxOKoU/vjzY4AYOAWIGdHkcMYfyJnLGSDO9GCAaEwFYj2YApDzsPlPCYifA/FVIBZhgDh1BxArwhTg8p8xEH8F4qVAzAzElQyQEAYDbgZIvL0EYnWYIBRwAvEiBoitx4B4HgNEPRzwMODOtCB/gZIfCGMEzigY2gAA/F09OO+agg4AAAAASUVORK5CYII=>

[image23]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAHEAAAAfCAYAAADQgCL6AAAFnklEQVR4Xu2ZechtYxTGH6HMMmQI3Uu41xQypagvcSPDH4abm6GvkDmia/hDfZJE1xBCyA0JJRRyL3IPyTVlyhTqXhIhiSJDhvWz9nvPu99v77P3Oefb3zllP/V0hvWes9dea71rrXdtqUWLFi1atGjR4n+CtYy7Gq83fmL8MuPTxt2zNesalxj3yz7PBjY0nmRcpq5O6Hehcf1szYHGm7P344QtjGcYX1NX93eNC41rZ2tOMF6cvR8KOGml8W/jO8azjbsYtzdOGlcZFxnvNn5o3PK/XzULHLTY+GvGu4yHG7c17m182Pi8cYHxR7nO44JNjLca/zJ+J98YBBq6H2p8UW5LbPqb/L4GBrvvPOMf8ig5LPsuBQpgqH+MjxnXyYtnHATPq/Lr3SE3SgqyAoZgzZ/GQ/LikWEf4+dyB16hbraIsanxBbnu3xh3yovrA2ddJv+jN+WGKwNrl8rXNh3xc4yfyo1wgYqDKoC0/otmLztUIQQ7Oh2dyFIcJ7fnM8b1ElltkIsx1NfGnRNZEXBe0xEfR+g16u1AgONw4GxkhyqE4EP3UxNZEXYz/mC8NhXUxR7G7+UXPCeRleFYNRvxOOwWuU4fGLfKiwuxkbGj5rNDFUiZT8l157UohaagPn6mAeshEXuv/IIfG7fOi0uBoZqM+JAa0Ys0WgcE1BtqNjvUwfHyphAemcjKwE6kUx2oHu6pbpMylReNDOzC2+U6fWXcMS8ea7DrOP6gO0cJSkLjYEdxwabrWz8ItQ29mtztTSDUNnQfuL71i/vlF/xCnpfHAXEqvTyRjTvoFdAb8n5YcHQKg4BChEaAC/LK5zqgBS5au7nxdA3RImcYxBCk4M2Uv2GC8ir5+fEiFZ8vZxoEHXoThHUnWeiM7nH3zUCA83pl88gIK7TwHRU7pghTyneAjJI4jDNJ6aj+/5ThKPXvROrms+re8L7Ge4w7yMeHLxvfU+/z70zgEvXvxIONj2p68DPleUjVR6s1nWlH9YxP97pCfiwJIBi4EKm5o3r/0wuM0X5Wf06kg+VIEm4YA7yibonAUL+r+fQcArCuE9H3Nk0/2rEzGbrUOi6FScFq43Z50TRwwSszFkXHTDkxTvN1jD5X3hHyGnCj8VvjvOwzzqTuo2OTwIar5bpj2yocJD9Lpl0sgczgpVazyWSdsxUXvTSRxcBpk/KxUHrBgCon8rsn5bPE/RNZCqYc6MTZtVcK3Mb4nHziFIM6s0H0OTRLU9F3AeweZsXXyRuJAN7zHTLWxGAm+r483cX2wE5Ml9AdvcpsAehkX5eP51Kcom49nC8f+L9knIjW5IBBmdgwdqO+pd0QDQFb/hGVOxBUOTGudTx16HV0wIB3ytdSz1JHYiwG9G+pei7JWlItY7A5iSzuCzgv7xXJ4rS+XPmgCGUIpgd6bER9RoaT04YK+/LE4m15MKRA36Xy4xU79SzjMfLhQRpMOVD8w47kZii0dHU8JiEtcdHUuSmqnEhKo06hzAqVrwvAkYvlXRp6USPQieuwOx7XdOcWgV1KU1M2E6bu8AgIw3FYD0A/gg3ZadH34AjjT/L7JZulIDioy+jN5giPm7ArG4ZNkTo3INRDdh7dPk5lLak1zhSFYPFc48lyBXgsRQNT+cMMVU4MwPAPqnpdAF3bhPwpyw3yaCwyXBFIVU/I0+4owK5cYLw644Sq7zvUQ+y5Sj40qOuDoVHXiQx5l6RfNgBSJ+M7zq+A4JlaIx1fxPXwAHlDhmNpmqqaz6FRx4lE1APydNQkcCC17kzjifLscp/qD9NHCeot9ZCegYbsI/lg/Hw1OEe+SV6jQu0iFdAEURdikK4n5Z1W0+khbjxi9mwMxgBsAPoFGkxA9lgpz1znqvhoN6sgsuiyQnprUYyNlW8iCXhsNnIHtmjRokWLFuOKfwEawDVc1nMzdwAAAABJRU5ErkJggg==>

[image24]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABIAAAAfCAYAAADqUJ2JAAABRUlEQVR4Xu2Uvy4FQRSHj4iEuIkIIVylRilaOgqFRkNxOwW1xgN4AQ8gEVGJKLSiuNFIqBQegEhUIoRK/Pl+ObvJ7Nq1cwvR7Jd8ubk7k9/MnJ2zZjV/QS8OYVd+oBN6cA+vcDA31hEL+IG3OJYbi2YAz/ELH3EqOxzPBj7jJ77hTHY4jkm8xjV8MQ+bz8yIQG9nB7exaV4fHW8pnBTDHF7gCA7jjXnQejipij48xpXkfwPb5kFbybMoWnhiHij68cw8aDedVMWE+ZHyb2ffPEi/lajAKu4BjptfvtQj8yDtTDv8lWl8wHu8y/lqHtQ2r1kp6qdD8wtYxKZ5UGWbLOOpla+m+6Mg7VZ1LER3RZ2t5iwjDfrRJiqsvi96eIlPOGv+3QnRkUdxFd8TFarFuzVBXaxu1iqhqkVIWpsiF4N5NTX/zzfoB0a5xRhS5QAAAABJRU5ErkJggg==>

[image25]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABwAAAAfCAYAAAD0ma06AAAB8UlEQVR4Xu2WO0gcURSGf0kEgy9QUYIWFir46HxUNooWIlpoo50gREkbCNgt2ChYaSAgoqiNgqV2QRcCClpYiTbioxDEIr2i5v89s7ozu7OMsxNIsR987OyeO3PuvXPvuQvkyBExebSBztIzeu24TZucNvl0jrY630OjBx7QJ3pMJ2g9raFj9IKO0kV6Qite7gqBRvWV3sNG0+385qWD/qHPdIt+dIeDoQd/hz3kCDYaP9R2BdZWow/FMH2kN7TOE0uHEj3QTm8gCM30DtbjSU/MjwGEfH+a/yVYslNa5Q77ohGGen8teFsAMXfo36CeKlno9/FeVmEJr+hnTyxyimgcllCf+h6EAgRv66KQ/sL7E8aQxf5LrNA4giXUKt6DbaVQDMISXtJqdygFVZgpx3QlLxDl9BCW9JsnlowSjNEdWuoOvZTBZboGi3+h63SfNia1e6UNVmlU2sbpB3cYJXSBbiA1mV7DDKzjiUX4g/bDTpu+15YedPYlRqpCsAk7fnbpLew48nZE1NIu51plTuVuCNZJ7Wudmb5o2mrpCJ2HHVVaHBlvSqKdnsNnGqNCo9bI1Cltld+02Inp1PnkXEeGZkNlsRdWzFW5RCXs70nkCXtg+3IaVhC0MrVKfyLzIZ4VWp2JoqGpLUMW+zTH/8tfZCFYGv/leOAAAAAASUVORK5CYII=>

[image26]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAGcAAAAfCAYAAAD6MNNVAAACyklEQVR4Xu2ZzatNURjGH6EI+bgiX2UgJUk3xYSJKAYoEwZSMiBDiZGRf8BQt1uSka+BgYkMbtdAIaXIhAGJUhIxIR/P07uXs/c653TPXvfsc/a55/3V0+3ste7q7PWs9a73XQdwHMdxnPoyjxqhZsUNTjILqSXxw7LMpa5ST6ilUZuTxmJqkrpNzYnaSrGX+k29pVZFbU4ap6m/1ARsByURHNZAn6lNxWYngfXUG9icvqSWF1pLIIe/Un+oH9S2YrNTEp3Zl2ELXeYkR6MN1HPqJPUNZtCeQo/BYSN1Av1PaHZRj9EIa0nRKDh8iVoDc1iDHch3GiCUad5Hf3f+fOoudQz2PRSJkqKRHH5ErYDFRMVGmXMq32nAUGJzD3aO9gOZInNkknaLdk3paKR/vkMdyT4rm5iAmXMhezaIKBqcp67AyoNeokX+kNqRfdY5kxSN8g6LBdQD2EDjodM00RnwjHrXY32AvYcW3zL0Bi0KHQ86JsKZtxKNjO1s9mxK1sLCWRwHr8EG0t9uoC+pc0ArqB86Tj2lDlGzUS2jsDldl3tWOhoFh69Tq1F8mVuwgbSDtJNmAlqIuvnQCla403t2G4XPm9RFFOdTmbAM6zgayeGP1Hs0h4Pv6EJFWzOCOa+oM6jGnIOwMiSeT+knOoxGcvgGLP9uheKiBkoumiLqEtZ2o7raR+83CcsSW6Ed09GCPwyrA9p1UkahgbSrtOKmyzAkBDpLZEC7i02167soi1sUtf1HaZ5unNs5LII5SUVTTQip9BgamWhVbIbdruhvO4I5TdEohBVNtK4TvlA7Yb/b5FG4U9p3lPqVSUbJ0KqznG5TdRGq+dC87KNeUy+oLWiuqTTHMuMczJxP1Hbkfi8LFaoa84pz7nDWtNL+XL+6E65vQhFYBar0VfFPNU/hrImlxGFrrt/QUJeLT8dxHMdxHMdxnKHjH4Gq2oF8b8LyAAAAAElFTkSuQmCC>

[image27]: <data:image/png;base64,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>

[image28]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACsAAAAfCAYAAAB+tjR7AAACWUlEQVR4Xu2WT0hVQRTGPykXUpGBhdFCi4yCwCQwEkWRNi6KKMEgKloVEi1UCFq0DYIgxJUuskXgn8pFRYtaCILRMtCl9IcgCCqIWpRYfZ9nxjfvomNE716D+8GP9+795s0978yZcwfIlStXqqoix0lnhDay0Y3PVC1kjnwgvxyfyNuAr+7+c7LHfpatmsk8+Uj2Jbwy0kUWyGfSUGynrwuw7E2RTQlP0j15GtOf8FKVMncX8UBU27OwMXcSXqoKAzmR8LwOk++wMZcSXqo6SL5h+XqVlPkBWKDPyOZiO13F6rWCXCM/yQNSXWynK2XtNizYV2TQMequf5An5JAbm6nCej1LtjvqYEEroyPIeOm9YvVaTiawBjaVV6xeJbWpzNuVtFp/1XlgEmsk2NX66y7yHuZfSXipK3YekDpggYbBrifHyFZ3XUsewjJ/jwyRM86T1Pp6yWNY13lKav7AW1IlbMffgAUyQ/aSbWRdMO4IrBuEwe4nj2DdQRO/JCcDTwcdv0oaM06uw+bVn9d8+ox5S6onX1DIWMg7srMwdLEbqH3JmybnYBvxKKzeb8Fq2p91tVJvUFili7Ajpp9TZdcGmzfm/bX0r/XwU+Q02e3u7yCvyVV3Lamz+K6yAfZqVmmodELFvJJI/Vkr5JdND9XD1VWUJb3xlOXlNqZKcCWvJFJm1Um09FIjrF6V/fOkFbZxwsxrlS7Dlnslrym498+kmtVb7T7pg53K1LOHyU3YTj9AXpAe0k3GSLv7bcwrmbS51F0kPWgLijuKvqvL+DGhYl6uXP+dfgMIGJLuRKWx5QAAAABJRU5ErkJggg==>

[image29]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACwAAAAfCAYAAACcai8CAAACeklEQVR4Xu2X3YsPcRTGn429kMUW1ktKNm9b3pWNqL3YkgsvaYtSXIkQi1pxISk32lJyIZEkWSXS2la4kC3+AdaFJFJqy25tXCAvz+Oc2f3ONGYvtDO/NE996vebc+bXmXOeOTM/oFSpUoVrCtlCWjJoIjWeX7jWkTekj/xy+sn7gM9+/DmZb6cVr7XkO/lEGhKxKrKN/CADZHk8XIz2wLrYQyYkYpKOKaac84lY7lIHbyC7GHn9JSznWiKWu8JitiZikVaTr7CcA4lY7lpJviDdv5ImcAFW7GMyKR7OX1n+HUdOkp/kDpkeD+cvde8qrOC35JJzy79/I92k0XMLV+jfnWSGMw9WuDrbgQqwQaQs/1aTu6iQGy1Sln8lrbCKWGXSSPtX7w9PUEEFj7R/68lHWPxYIlaIst4fpA2wYsOCx5JNZCrZTDpJL+x8TeQhuel50kRyjjwgVzw2mcwm18krchi2MrWNWu20uGphm+AsrJgXZCGpI2OCvGbYlggLXkTukyXkBJkFe9vTxclix2EPl/GwzfKItHlMUnG60feTxaTL8xfAfuey5w1pKRnEcOdCPpA5w6l/toRWm2LPyC7YzbmRLHO2w2w1zc9RMaf98ymYpVbAOqoiL8KsKAvq2GvYxHRBqzz2T1LHNW4VtoPMDWIau0asx3bUwfWwbqvD6txT2J8AXeTMIE+SJd8h3Y6jInVVY4xuWBVzyI9HG0a2+ZuOwHJy+zej+0AjldcljXS3f44e+Wf8u6RpHSRrYNO5jfR1OmpSUXoxukf2knbEH+HyqDx/lOyD2acJdp68qptd/s1dGqk2T5pUnNZYWlw+Dz1dqtR/p9/Emo44Wsn6zAAAAABJRU5ErkJggg==>

[image30]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAD0AAAAfCAYAAABUBsXUAAADkUlEQVR4Xu2YWahNURzGP6HIPI+ZQkSEEFH3wQOKhKIUSZEhMkR54L54oGSIDNG9kpDpxRjlRrnFkyJFMqSUQokHZPg+/7Xa66z22feemyPndL76Pey91tl7/ce19gEqqqiiispQXckcMj+DKtLWzS8LTSXPyTvyy/GBvA747O7Xk6H2s/LQFPKdvCfDo7FmZAH5QT6SMbnDpasVsGjeJe2iMUn3NKY5+6OxkpQieQrZBqn2H8PmnIjGSlKhQXOjMa9J5CtszpporCQ1jnxBej1LyoQDMINvkQ65w6WprHpuTbaRn+Qi6Zk7XJpSFGtgRr8gRx1n3fU3co1MdHPLQmE9Lya9HENgxivCZ1B4SreKbxRBLRwFK6ueW5JLaHzzkgP/xba2DHZgSltzo5RVz5K2p0K2qR6wE16+XaBQVcEyUc8NpXXnW3OmGtqfdd6uQ2FG62T3Ck2MQIp2kCvILRel9Hmkr7lBNbQ/DyJvYeNborF82oAmRiBFMlQGV0f3te5HZHZ0v1HKOm9LM2AGh0bLy3pZN3c9ANbolAk3yFPkRqA92UOuk+PkNOlC+pOT5BlZ7u6L22QY2Ufuw877T8g50hsmrVsfSEdILbkKy9jML8GOsA69C2aQvKYXdSfNg3nTYN07NHokuQzr5lr4QzIvGNMHySJ3rTk3yWYk2916WPNcR0bBFhseeOQ87zS9X/2hr7v2Uj3rPZPdtZ73koz2E2Jp4BOSCIa8IQOTqX+6t7Ytjd0jS2CpOwvJKa0OiYfjeq6GlcdYmINWk8MwA2eSfrDy8k6SZHQd7JlZ9ax3e0fmc06TpcjLiIWwxQ129/vAvLvVXUuq5wekE2kDi+Ad2B8RcpTSMzzgjIct1jtJv9PvjyGp5/D5ku9DoaPSnFMUKaWUMap7KeyoWtgIWMTiRYdSmnonSXqm/sCYDmuiyhpFUVIp6EgcZ5N3lJ6lQIiiSQ+Xx7UIaQKSel4Kq7caWBS8lDVr3ZjfLuUoOUzXe2HlpLKSsTJOxiu71FM0J3aUn6c56hNydtGkBeiUdoFsgtWYjKglu2FRUR2rB2wkq2DduQr2W5+m6hVyzCFYl1dZSKrPerKdHER6o5NkpJ6zk6zEP/o+UMPRbiDphYpAuAPonrYoP8dL9awIqbFqLG27UcQ7I9cQOUX3Q6mW4+f/l4rTtKylqCnVtZXpn1Z9q/uULmupcUkqg79xXK0o1m8rjdJlOAHpaQAAAABJRU5ErkJggg==>

[image31]: <data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEIAAAAfCAYAAABTRBvBAAADkUlEQVR4Xu2YW6gNURjH/0K5hVwTkmu5RCIlvIhyTzwgynkRD6Qocnk4kgclD7xI7h4kcgmRxCmnyIPyIBKFSJFEKZHL/7+/WWfvWXtm75k5m7LP/Orf2a1vZs2sb32XNQfIycnJyflv6ExtoJ5T36lmalroijbAIKqJWk91pdpTK6hP1IziZfVND+oatdQbH0C9oo5443XLVmob1c4bd444C4uQumYodYPq7xvIJOortcc31CNrEb1QRcduWI2Y6Nn+KXqRUdQu6jC1GRaqtaQDdYaaRXWkxlKLqZHUXOodrHj6KZMGFd5VsDVspLqHzQWGUKP9QSEHPIC1sdWwEFWefqOWlFynmyMnSEgf6go1nLpI/fak2tEaJ8ykPiA85yNYh3KoZWszyjrTZNjNt2DV3NEb5pw3sLwu3c2sTKdOwebS/AtRjIidsPdQlGRhCiytVGz3U9upuzBnKOWcg2fD3kER2YI89QQ2wbhSQ4ByWROtROUilxTVB+16FIq0j9QB35AA7fJlWFrrt0OL30K9oAbCNuAEzBkhGmEL1U7rIh+9tOx6Of1uDFnTofm1E4qKKFzrVGQqz9MwAZZq3XwDzDFKR0XyVOp8MNaC8vUxijseheqD7A+p29TgsDkVrj5oZ6JwrTOLI6qxidpBHaSWe7aCF78E0u8olMNyxC9qmWdLS2l9iMI5PUtqVEMd6T5iUtvtgPKnzBjgHKGwCxWXDGhXTvqDAa4Q/0RE/tYAt9ZGb7yAQvQlLD0Utj5auKqtHBG3AHUctdxLCHccHy1Uuanvi06eTWjxcoJ6v+9wzav59Rw9LwtyxOvgbxmqqDrv6wXmeLYx1D3qAqyjNMEOJmuo8cXLCh9Hrl+X5V4Jijj18/co/9BS79f4IXhFLEBh7Z4RV9SrIQfImVHFtIC8vxf2vwC1Hx2i3lLXYc5w7ecHzDFXEd557eRnWA2Ja4tC9eEcrGo/hTn2aPBbETkf8QcpdZNm2DPuoMJiKqC2neiLVpPrgVJU6Pak+iH+i1CFrpIjVB+cXXNoLj0rzaJ05jmNdPcIl5Z6h7/OPsSfOF0hjLMnRffrOWlRLXyG1j+/KiNgqaRdjkL14Sbizw9JUAqr9WbpKHKADmrDfEMt0cHnGCqfMaqdH6qh2tEAK6Z+R0mCUiKuW9WMvtQ8xNcOoX/GLvIHUyAHLqB6+YaEHKfW+YNtkS6ovFE5OTnl/AEHyKq3Rb4VdgAAAABJRU5ErkJggg==>