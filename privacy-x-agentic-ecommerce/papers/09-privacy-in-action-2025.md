# Privacy in Action: Towards Realistic Privacy Mitigation and Evaluation for LLM-Powered Agents

## Metadata

- Authors: Shouju Wang, Fenglin Yu, Xirui Liu (Wuhan University; Microsoft internship), Xiaoting Qin, Jue Zhang, Qingwei Lin, Dongmei Zhang, Saravan Rajmohan (Microsoft).
- Date: arXiv v1, 22 September 2025.
- Venue/ID: arXiv:2509.17488v1 [cs.CR], preprint.
- Length: 20 pages including appendices.
- Type: inference-time mitigation framework plus dynamic benchmark.
- Read status: complete local text.

## One-paragraph summary

PrivacyChecker is a model-agnostic, contextual-integrity-based mitigation wrapper for LLM agents. Instead of a generic "preserve privacy" instruction, it prompts the model to enumerate each information flow implied by the user query, tool outputs, and history, giving sender, recipient, subject, type, and transmission principle; it then asks for a binary Yes/No judgment per flow and uses that judgment to exclude or abstract data in the final action. The paper also introduces PrivacyLens-Live, converting the static PrivacyLens benchmark into MCP and A2A environments with Gmail/Notion tool paths plus 36 new three-tool cases. On the 493-case PrivacyLens action task, PrivacyChecker reduces leakage from 36.08% to 7.30% on DeepSeek-R1 and 33.06% to 8.32% on GPT-4o while keeping helpfulness in the "Good" range. It identifies a judge-action gap: GPT-4o classifies private information as sensitive 97.97% of the time but leaks in 33.06% of actions. Live MCP/A2A baselines leak more than static runs, and three deployment strategies all reduce leakage.

## Problem and threat model

Agents handle email, documents, credentials, and multi-step workflows through MCP and A2A. Privacy risk is not only training-data memorization but inappropriate disclosure in the final action: an email, message, tool call, or summary sent to the wrong recipient. Existing benchmarks are mostly static, single-turn, or scripted; they miss noisy tool trajectories, failed calls, retrieved context, and inter-agent communication.

The paper's central diagnosis is a **judge-action gap**. With a privacy-enhanced prompt, GPT-4o correctly judges private information as sensitive 97.97% of the time but leaks private details in 33.06% of cases (prose rounds to 98.0% and 33.1%). Manual analysis finds the chain-of-thought rarely mentions privacy; it focuses on task completion, e.g., "All information is relevant, I'm gonna draft an email now." The model recognizes privacy but does not operationalize it under task demands.

The threat model is mostly non-adversarial: ordinary requests, tool outputs, and accumulated history. The paper mentions adversarial memory poisoning and contextual ambiguity as future concerns, but experiments do not inject adaptive attackers. Evaluation is about accidental disclosure and instruction-following under realistic workflows, not prompt injection or a strategic adversary. Sensitive items come from PrivacyLens, ConfAIde, and CultureBank; PrivacyLens supplies 493 action-based trajectory cases.

## Architecture / protocol / taxonomy

PrivacyChecker has three prompt-level components in one prompt:

1. **Information Flow Extraction.** The model enumerates flows from the user query, tool outputs, and history. Each is JSON with `data_type`, `data_subject`, `data_sender`, `data_recipient`, `transmission_principle`. Separate flows are required when information differs by temporal context, spatial context, social context, functional purpose, or category.
2. **Privacy Judgment Per Flow.** For each flow, apply "only include details absolutely necessary for the task," then answer whether it is acceptable to include the data subject's information in the response to the recipient, on behalf of the user: (A) Yes or (B) No, with justification. Any (B) flow must not be quoted or paraphrased; it is replaced with a high-level summary.
3. **Privacy Guideline (optional).** A HIPAA/FERPA-inspired guideline: avoid grades/individual performance; avoid medical, mental-health, reproductive, legal, immigration, and unique-identifier details; avoid precise dates/times/locations; avoid relationship/group details; anonymize and aggregate; avoid private workplace schedules, salaries, strategies; avoid naming stigmatized groups; when in doubt, redact. It is absent from main results and tested only in ablation.

The framework is protocol-agnostic but evaluated in three deployments:

- **System prompt.** Global privacy instructions; continuous awareness across tool boundaries.
- **Inside an MCP tool.** Privacy-aware descriptions embedded in a tool such as `gmail_send_message`, checking during composition.
- **Standalone MCP tool.** `send_privacy_check` receives draft and trajectory, returns a permission assessment, and must be called before `gmail_send_message`, creating two-stage validation.

PrivacyLens-Live implements Gmail and Notion MCP tools, the most common tool usage, yielding 150 test samples. Gmail supports retrieval and sending; Notion supports retrieval. A test Google account with Gmail API access and a Notion integration provides data; a script imports relevant data before each action round. The agent is an MCP client guided by a system prompt. For A2A, the paper implements a two-agent sender/receiver setting because full A2A implementations were limited. Agent cards describe the owner and related individuals and are private, not shared. The original single-agent query is rewritten as direct inter-agent communication; extraction and adaptation are manually validated.

The paper adds 36 three-tool cases: Gmail + Google Calendar + Notion (25), Gmail + Notion + Slack (8), Gmail + Messenger + Notion (3).

## Section-by-section findings

- **Introduction/related work.** Agents are moving into customer service, healthcare, and personal assistance, accelerated by MCP/A2A. Training-stage DP and federated learning do not address inference-time flow. AirGapAgent minimizes personal information against third-party context hijacking; a CI-based supervisor handles single-key form filling. PrivacyChecker generalizes to multi-step protocols and all flows.
- **Judge-action gap.** The 97.97%/33.06% result motivates the design. The gap is not just Q&A versus generation; it is failure to enforce privacy under competing task objectives.
- **Design.** Single-prompt flow extraction plus per-flow judgment. The guideline is optional. No fine-tuning or separate classifier.
- **Static evaluation.** Across eight models, leakage falls substantially with small helpfulness loss. Reasoning-tuned models benefit more, though o1 benefits less than DeepSeek-R1/Qwen3.
- **Failure analysis.** Of 36 DeepSeek-R1 failures: 18 incorrect judgments, 11 judge-action gaps, 3 flow-extraction failures, 4 "others" involving evaluator instability from small LLMs.
- **Ablation.** Removing explicit verbalization worsens leakage; ground-truth flows improve it; the guideline helps modestly. Extraction quality and explicit reasoning are bottlenecks.
- **Live MCP/A2A.** Baseline leakage is higher than static (26.3% MCP, 24.3% MCP+A2A, 17.4% static with o3). All three strategies reduce leakage. A case study shows noisy trajectories retrieving partial context and leaking a confidential plan; a simplified `get_all_message` cleans trajectories, and the standalone tool acts as an intermediate reasoning scaffold.
- **Complex workflows.** Three-tool leakage is higher than two-tool for baseline and mitigation, indicating harder flow tracking.
- **CI supervisor comparison.** On static PrivacyLens, PrivacyChecker has lower LR/LRh at similar helpfulness.

## Key mechanisms and equations

Metrics:

- Leak Rate (LR): proportion of cases whose final action contains sensitive items. Lower is better.
- Helpfulness (Help): 4-point 0-3 scale (0 Poor, 1 Unsatisfactory, 2 Good, 3 Excellent). Higher is better.
- Adjusted Leak Rate: `LRh = (# leakage cases with Help > 2) / (# total cases with Help > 2)`. Lower is better. It excludes low leakage from non-response or irrelevant output.

Flow schema: `{"data_type": "...", "data_subject": "...", "data_sender": "...", "data_recipient": "...", "transmission_principle": "..."}`. Judgment is `(A)` Yes or `(B)` No per flow. If any flow is `(B)`, the agent must not quote or paraphrase that data and must use a high-level summary.

The standalone tool `send_privacy_check` takes `to`, `subject`, `body`, optional `cc`/`bcc`, `execution_log_path`, and `card_path`. It returns `leakage_detected`, the flow fields, and a judgment. The agent revises until no leakage is detected.

## Experiments / evaluation

**Data/models.** Primary: 493 action-based trajectory cases from PrivacyLens. Extended: ConfAIde, CultureBank. Models: GPT-4o, GPT-4.5, o1, Qwen3-8B/14B in reasoning (T) and non-reasoning (NT) variants, DeepSeek-R1. Recommended parameters. Baselines: PrivacyLens privacy-enhanced prompting and a CI-based supervisor.

**Main results (LR / Help / LRh, PrivacyLens -> PrivacyChecker).** Qwen3-8B-NT 39.76/2.68/40.54 -> 24.20/2.65/24.62. Qwen3-14B-NT 37.52/2.65/37.81 -> 15.91/2.49/15.79. GPT-4o 33.06/2.69/33.56 -> 8.32/2.45/9.22. GPT-4.5 29.41/2.64/29.60 -> 9.55/2.57/10.25. Qwen3-8B-T 43.17/2.66/44.62 -> 22.39/2.48/23.47. Qwen3-14B-T 36.11/2.62/38.04 -> 11.76/2.40/12.47. DeepSeek-R1 36.08/2.58/37.21 -> 7.30/2.35/8.06. o1 27.24/2.69/27.95 -> 11.63/2.63/12.39.

The paper says "over 75% across diverse LLMs," but the table is not universal: GPT-4o is about 74.8% relative, DeepSeek-R1 about 79.8%, Qwen3-8B-NT only about 39.1%. The claim should be read as headline models, not every row.

**Ablation (LR).** GPT-4o: full 8.32; no verbalization 11.36; ground-truth flows 4.26; add guideline 7.30. Qwen3-14B-NT: 15.91; 30.63; 9.76; 15.92.

**Failures.** DeepSeek-R1 with PrivacyChecker: 36 failures, split 18/11/3/4 as above.

**Live MCP/A2A (o3, LR).** Static: baseline 17.4, inside Gmail 7.3. MCP: baseline 26.3, inside Gmail 8.7, system prompt 8.0, standalone 5.3. MCP+A2A: baseline 24.3, inside Gmail 6.7, system prompt 6.7, standalone 8.7. Three-tool: static 22.6 -> 16.4; MCP+A2A 28.6 -> 16.7.

**CI supervisor (static).** GPT-4o: supervisor 14.4/15.96/2.52 vs PrivacyChecker 8.32/9.22/2.45. Qwen3-14B-T: 23.16/24.46/2.48 vs 11.76/12.47/2.40.

**Extended data.** ConfAIde GPT-4o 28.13 -> 6.25, help 2.94 -> 2.84. CultureBank GPT-4o 33.33 -> 8.33; DeepSeek-R1 25.00 -> 4.17. Full rows are in the appendix.

## Paper-specific findings

1. The problem is a judge-action gap, not only a knowledge gap: 97.97% correct sensitivity judgment, 33.06% leakage.
2. Gain comes from explicit per-flow reasoning; removing verbalization raises GPT-4o 8.32 -> 11.36 and Qwen3-14B-NT 15.91 -> 30.63.
3. Flow extraction is a bottleneck: ground-truth flows reduce GPT-4o to 4.26 and Qwen3-14B-NT to 9.76.
4. The optional guideline helps modestly (GPT-4o 8.32 -> 7.30) and is customization, not the core.
5. Reasoning-tuned models benefit more (Qwen3-14B-T 11.76 vs NT 15.91), but o1 benefits less than DeepSeek-R1/Qwen3.
6. Residual failures are mostly judgment errors (18/36) and judge-action gaps (11/36), not missed flows (3/36).
7. Live MCP/A2A baselines leak more than static (26.3/24.3 vs 17.4), largely from noisy partial context.
8. All three placements work; standalone is best in MCP (5.3) but not MCP+A2A (8.7), so placement is workflow-dependent.
9. More tools make privacy harder: 3-tool leakage exceeds 2-tool, with missed/ambiguous flows as a common error.
10. The standalone tool doubles as a reasoning scaffold, summarizing noisy context into structured flows.
11. ConfAIde and CultureBank show the same direction of improvement, though absolute rates vary.
12. The approach is prompt-based and model-agnostic: no fine-tuning, separate classifier, or protocol-specific API.

## Limitations and caveats

This is an arXiv preprint, not peer-reviewed at the captured version. The threat model is non-adversarial; memory poisoning, contextual ambiguity, and adaptive attacks are future work. The main evaluation is 493 PrivacyLens cases with curated trajectories; the live benchmark covers 150 Gmail/Notion samples and 36 three-tool cases, not the full tool space. A2A is a partial reimplementation; agent cards and data extraction are GPT-4o-generated and manually validated. The live setup uses a test Google account, imported data, and specific integrations, limiting external validity.

The "over 75%" reduction is not true for every model; Qwen3-8B-NT improves about 39% relative. Helpfulness drops slightly, and the 4-point scale can hide task-specific failures. The guideline is absent from main results, so the headline excludes HIPAA/FERPA-style rules. The paper does not measure latency, token cost, false positives/over-redaction, or user trust. It does not prove flow-extraction completeness, and the 3/36 extraction failures likely underestimate messier contexts. There is no cryptographic privacy, unlinkability, on-chain enforcement, or defense against a malicious tool/model provider.

## Hackathon hooks

**What this enables.** A practical middleware gate for agent commerce: before an agent sends a checkout email, releases a shipping address, or summarizes a negotiation, run flow extraction and per-flow judgments, then redact or abstract disallowed flows. The five-field schema is easy to display, and the standalone tool maps naturally to an MCP server. The judge-action gap is a compelling demo: show the model labeling a field private, then the gate blocking it. The 3-tool extension is a template for procurement workflows.

**What it does NOT enable.** It is not cryptographic privacy, formal access control, or a defense against prompt injection/memory poisoning. It does not hide metadata, unlink payments, or enforce spending limits. It does not replace legal/compliance review or guarantee complete extraction. It is not a Zcash or Monad component.

**Demo ideas.** (1) "Checkout gate": a buyer agent negotiates with a merchant agent; the buyer has a private budget and a medical appointment; show baseline leakage and PrivacyChecker redaction while preserving the order. (2) "Judge then act": display flow JSON and (A)/(B) decisions next to the final email. (3) "Tool placement": compare system-prompt, embedded-tool, and standalone `send_privacy_check`. (4) "Noisy live run": show a failed/redundant tool call causing partial retrieval and leakage, then the standalone tool stabilizing it. Pair with MinMandate or Cloaked as an application-layer gate, labeled as new work.

## Claims to avoid

Do not claim PrivacyChecker solves agent privacy, is adversarially robust, or is production-ready. Do not say it reduces leakage by 75% for all models; the table varies widely. Do not claim cryptographic privacy, unlinkability, or on-chain enforcement. Do not claim HIPAA/FERPA compliance; the guideline is inspired by those regimes, not certified. Do not claim the A2A implementation is complete or the live benchmark covers all tools. Do not claim it prevents prompt injection, memory poisoning, or malicious tools; those are out of scope. Do not present helpfulness as unchanged. Do not claim flow extraction is exhaustive or that 3/36 generalizes.
