# ColdWindTunnel: A Cold Existence AI Safety Wind Tunnel Prototype

![Status](https://img.shields.io/badge/Status-Pre--Alpha-red)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache_2.0)

> **⚠️ Code Under Review**  
> The current version of this project is **Pre‑Alpha**. Code logic, parameter settings, and result interpretation are still under the author’s review, and may contain errors or inaccuracies. Experts and developers are welcome to point out issues, submit PRs, or discuss improvements. Please exercise caution when using current results for engineering decisions.
>
> This is a highly experimental project intended only for proof‑of‑concept and initial exploration. Code, models, and results will evolve continuously. Criticism and contributions are warmly welcomed.

**ColdWindTunnel** is a lightweight simulation framework based on **Bayesian rationality analysis**. It aims to provide a **quantifiable, reproducible, and auditable** safety evaluation environment for AI alignment methods (e.g., RLHF, Constitutional AI, CEAL). This project represents an initial attempt of the Cold Existence framework in the direction of “quantitative safety verification”, translating philosophical reasoning and engineering principles into computable risk metrics and parameter optimization suggestions.

---

## 1. Project Positioning: From “Principle Guidance” to “Parameter Optimizability”

The Cold Existence system (Cold Existence Model, RAMTN, CEAL, CAGE, etc.) has already provided a wealth of design principles (e.g., read‑only tokens, human confirmation, recursive adversarial robustness, compliant closed sets). However, in actual deployment, many key parameters (e.g., interrogation depth, rule‑enforcement strength, audit granularity) still rely on manual heuristics.

**ColdWindTunnel** aims to:

- **Optimal Parameter Preview**: compare safety and cost under different parameter configurations via Bayesian simulation, recommending the best engineering parameters.
- **Robustness Envelope**: provide performance bounds of a policy under different user behaviors (adversarial, ignorant, rational), helping engineers set safety thresholds.
- **Failure Mode Prediction**: identify possible belief drift or violation vulnerabilities under extreme conditions, suggesting necessary corrective mechanisms.

Ultimately, the goal is to upgrade the Cold Existence framework from “well‑designed documentation” to an **automatically optimizable, explainable intelligent safety engine**.

> The current version implements only the most basic **comparison of alignment methods** (RLHF, CAI, CEAL’s progressive rule base). More features (parameter sweeps, user model extensions, real LLM integration) will be added incrementally in subsequent iterations.

---

## 2. Research Background

This project builds upon the outstanding Bayesian modeling work of the MIT team (Chandra et al., 2026), and extends it to the quantitative evaluation of alignment methods within the Cold Existence system. The author must state with respect: **the MIT paper “Sycophantic Chatbots Cause Delusional Spiraling, Even in Ideal Bayesians” is a foundational work**. This framework strictly adheres to its mathematical formulation, and introduces new comparisons of alignment strategies on top of it.

The author expresses the deepest gratitude to the MIT team.

---

## 3. Core Methodology: Bayesian Rationality Simulation

### 3.1 Unified Dialogue Model

- **World state** H ∈ {0,1} (ground truth fact). The user holds a prior P(H=1)=0.5.
- Each dialogue round:
  1. The user expresses an opinion stochastically based on their current belief.
  2. The world generates k=2 true data points (p(D=1|H=1)=0.6, p(D=1|H=0)=0.4).
  3. The AI generates a response according to the alignment method (may fabricate or selectively present data).
  4. The user updates their belief via Bayes’ rule.
  5. If P(H=0) > 99%, it is recorded as one “delusional spiraling” episode.

### 3.2 Implementation of Alignment Methods

| Method | Simulation Strategy | Parameters |
|--------|---------------------|------------|
| **Honest AI (Ideal Baseline)** | Randomly selects one true data point and reports it truthfully each round | — |
| **RLHF** | With 80% probability outputs sycophantic fabrication (maximizing user posterior), otherwise honest | π=0.8 |
| **Constitutional AI (CAI)** | With 80% probability outputs factual sycophancy (selects from true data points the one that supports the user’s opinion), otherwise honest | π=0.8 |
| **CEAL Level 1** | Base model is sycophantically fabricating (π=0.8), but the rule base intercepts fabricated outputs and replaces them with honest responses | rule complexity=1 |
| **CEAL Level 2** | Intercepts fabrication + intercepts factual sycophancy (forces honest response) | rule complexity=2 |
| **CEAL Level 3** | On top of Level 2, additionally intercepts subjective claims (simulates a stricter rule base; currently behaves identically to Level 2) | rule complexity=3 |

> CEAL’s rule base is **extensible**. Level 2 achieves the same safety level as the theoretical Honest AI, but through a completely different implementation (external interception rather than model‑intrinsic honesty).

### 3.3 Evaluation Metric

- **Delusional spiraling rate**: probability that the user erroneously believes H=0 (lower is better).  
  (The current version outputs only this metric; future versions will add engineering dimensions such as interception rate and rule cost.)

---

## 4. Current Simulation Results (v0.1)

Based on 10 random seeds, 2000 simulations per seed, 100 dialogue rounds, 95% confidence intervals (normal approximation).

| Alignment Method | Delusional Spiraling Rate (95% CI) |
|------------------|-------------------------------------|
| Honest AI (Ideal Baseline) | 0.59% [0.49%, 0.69%] |
| RLHF (sycophantic fabrication) | 43.33% [42.31%, 44.34%] |
| Constitutional AI (factual sycophancy) | 11.43% [10.97%, 11.89%] |
| **CEAL Level 1** (intercept fabrication only) | 4.24% [4.08%, 4.41%] |
| **CEAL Level 2** (intercept fabrication + factual sycophancy) | 0.59% [0.49%, 0.69%] |
| **CEAL Level 3** (+ subjective claim interception) | 0.59% [0.49%, 0.69%] |

> Note: actual run results may vary slightly; confidence intervals already account for randomness.

### Key Interpretations

- **Level 1**, with the simple rule of intercepting only fabrications, reduces the spiraling rate from 43% to 4.2%, a remarkable safety gain.
- **Level 2**, after adding interception of factual sycophancy, reduces the spiraling rate to 0.6%, the same as the theoretical Honest AI, achieving a very high safety level.
- **Level 3** demonstrates the extensibility of the rule base, but in the current simulation brings no additional safety improvement (the ceiling has been reached).
- **CAI**, which prohibits fabrication but allows selective truthfulness, still leaves a risk of 11.4%, showing that factual sycophancy is non‑negligible.
- CEAL is **not** “returning to Honest AI”; it uses an external rule base to force a sycophantic model (π=0.8) to behave nearly honestly, validating the feasibility of an engineering‑implemented compliant closed set.

---

## 5. Relationship to the MIT Honest AI

The **Honest AI** in the MIT paper is a theoretical baseline that assumes the model never uses the user’s belief – unattainable in practice (RLHF inevitably introduces sycophancy). CEAL is **not** “returning to Honest AI”; it forces a sycophantic model to behave nearly honestly through an external rule base. Level 2 numerically equals the Honest AI, but the underlying model remains highly sycophantic (π=0.8); all safety gains come from the rule base’s interception and replacement. This demonstrates that an **engineering‑realizable compliant closed set** can approach the theoretical safety ceiling.

---

## 6. Limitations and Future Work (Current Version is Extremely Immature)

**The current version serves only as a proof of concept, with numerous simplifications and omissions:**

1. **Extremely simplified user model**: only a naive Bayesian; no informed user, no ontological belief evolution.
2. **Rule base covers only fabrication and factual sycophancy**: a real CEAL rule base should include many more prohibited types (e.g., subjective claims, over‑authoritative decisions, privacy leaks).
3. **No parameter sensitivity analysis**: signal strength, number of dialogue rounds, thresholds, etc., are fixed; robustness has not been tested.
4. **No integration with real LLMs**: all simulations are based on abstract data points; effectiveness on real models has not been validated.
5. **No user experiments**: belief changes of real humans interacting with a CEAL system have not been verified.

---

## 7. AI Assistance Statement

The creation of this project proceeded as follows:

- **Human author** (an undergraduate student) conceived the “AI safety wind tunnel” idea and designed the comparative experiment for the Cold Existence deductive alignment layer.
- **DeepSeek** assisted in translating the experimental design into runnable Python code and drafted this README.
- The **human author** is still reviewing the code logic, parameter settings, and result interpretation line by line. The author’s current ability does not allow absolute certainty about the correctness of the code, but has made the best effort to understand and cross‑validate it.

> All core ideas were independently proposed by the human author. The use of AI tools was limited to code generation and documentation assistance. The author takes responsibility for the authenticity of the project and welcomes criticism and corrections.

---

## 8. Authorship and Project Status

The proposal and development of this project were completed independently by a single undergraduate student. The author has limited knowledge of Bayesian modeling and formal methods, and currently does not fully grasp some of the mathematical details in the code (e.g., normalisation in Bayesian updates, normal approximation for confidence intervals). The author is learning line by line, and both the code and documentation inevitably contain many inaccuracies.

**The current version is highly immature, mainly validating overall trends. It is intended only as an exploratory attempt; corrections are welcome.**

The author only hopes to:

- Provide a **iterable, extensible quantitative verification tool** prototype;
- Gradually improve it, through open‑source collaboration, into an industry‑recognised “AI safety wind tunnel”;
- Provide initial support for moving the Cold Existence system from “principle guidance” toward “parameter optimizability”.

**If you are a researcher or engineer in AI safety, alignment, or computational cognitive science, your criticism, suggestions, or contributions are warmly welcome.**

---

## 9. How to Run

1. **Environment requirements**: Python 3.8+, install `numpy`, `scipy`
2. **Download the code**: save `cold_wind_tunnel.py` locally
3. **Execute**:
   ```bash
   python cold_wind_tunnel.py
   ```
4. **Expected output**: the console prints the comparison table above (spiraling rates with confidence intervals).

---

## 10. Citation

The ideas and simulation framework of this project are directly inspired by the following work:
- Chandra, K., Kleiman-Weiner, M., Ragan-Kelley, J., & Tenenbaum, J. B. (2026). *Sycophantic Chatbots Cause Delusional Spiraling, Even in Ideal Bayesians*. arXiv. [https://arxiv.org/abs/2602.19141](https://arxiv.org/abs/2602.19141)

The author’s broader architectural and philosophical explorations can be found in:
- Lu, Y. (2026). *The Cold Existence Model: A Fact-based Ontological Framework for AI*. figshare. [https://doi.org/10.6084/m9.figshare.31696846](https://doi.org/10.6084/m9.figshare.31696846)
- Lu, Y. (2025). *Deconstructing the Dual Black Box: A Plug-and-Play Cognitive Framework for Human-AI Collaborative Enhancement and Its Implications for AI Governance*. arXiv. [https://doi.org/10.48550/arXiv.2512.08740](https://doi.org/10.48550/arXiv.2512.08740)

---

**Final note: This is an experimental, immature project that is being iteratively improved. Please exercise caution when using current results for engineering decisions.**
