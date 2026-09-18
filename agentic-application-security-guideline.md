---
id: artifact/the-core-ai-bar-raiser/agentic-application-security-guideline
type: artifact
status: draft
sensitivity: confidential
aliases:
  - Agentic AI Application Security Control and Governance Framework
  - Agentic Application Security Control and Governance Framework
  - Securing Agentic AI Applications
  - Agentic Application Security and Governance Guideline
created: 2026-09-17
updated: 2026-09-18
---

# Agentic AI application security control and governance framework

## Executive summary

This discussion draft defines a proposed framework for deciding whether an
agentic AI application should be deployed, bounding what it may do, and showing
that those bounds hold in operation. It is for business and application owners,
builders, platform teams, security, Technology Risk, data owners, and operators.
The review unit is the deployed workflow, including its users, models, data,
tools, providers, delegated agents, scheduled paths, and downstream effects.
Its governing principle is **controlled autonomy: the model proposes an
action, and a deterministic application layer decides what actually
executes**. Greater reasoning capability does not require broader execution
authority.

The framework has four connected decisions:

1. **Classify the effect and assess the data.** Assign one T0–T3 tier from the
   highest-consequence reachable action, including actions people routinely
   take on the agent's advice. Separately record data classifications,
   entitlements, permitted use, approved linkages, derived outputs, flows, and
   privacy obligations under firm policy. Data controls apply at every action
   tier.
2. **Enforce the boundary.** The model proposes a typed intent; an
   application-controlled layer validates, authorizes, bounds, and executes it
   under scoped credentials. Check each call against the caller's authority,
   delegated scope, target, arguments, destination, and limits. Bind data reads
   and transfers to source permissions and approved destinations. Authorize
   combinations before data enters a shared context; a prompt is not an
   authorization control.
3. **Prove the controls.** Test permitted, prohibited, retry, failure, and
   adversarial paths. Trace the proposed action, policy and approval decisions,
   executed effect, and verified outcome without creating an unrestricted copy
   of sensitive data.
4. **Govern the lifecycle.** Name an accountable owner and decision makers for
   intake, design, release, operation, changes, incidents, and retirement.
   Record residual risk, monitor behavior, recertify access, and maintain stop,
   recovery, and manual-continuity paths.

For a material workflow, the release decision should be supported by an action
tier, a separate data-handling record, a threat model, control and denial-test
evidence, an accountable approval or exception, and an operating runbook. This
framework is proposed guidance, not an approved firm standard or a claim that
the controls are implemented. Reconcile it with applicable internal policies
and approval processes before adoption.

## Control premise and operating principles

An agentic AI application can choose a sequence of steps, retrieve information,
call tools, and influence people or systems. Its risk depends on the **data it
can reach, the actions it can cause, and the scale at which it can repeat
them**. A model can be wrong or manipulated by a document, tool result, memory
entry, or another agent. The application must still honor the user's authority,
data boundaries, and business rules. OWASP's agentic risk taxonomy names goal
hijack, tool misuse, privilege abuse, memory poisoning, cascading failures,
and misplaced human trust among the failure modes.
[OWASP Agentic Top 10, 2025-12-09](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)

The goal is **controlled autonomy**, not the maximum autonomy the model can
exercise. The model may plan and choose among available capabilities, but it
does not decide its own authority. A deterministic, application-controlled
layer decides whether each proposed action is valid, authorized, and within
bounds before any effect occurs:

```text
model                    deterministic layer                         effect
proposes  -->  validate  -->  authorize  -->  bound  -->  execute  -->  record
an intent       is it a       may THIS       within      under a       what was
(typed,         legal         caller do      run and     scoped        done, by
not prose)      action?       it, to THIS    period      credential    whom, and
                              argument?     limits                     on whose
                                                                      authority

       ^                                                              |
       |____________ the result re-enters the context, untrusted _____|
```

The proposal is inert until the application validates and authorizes it. The
gate must operate on every reachable path, including retries, queues,
scheduled runs, and delegated calls. Record both the policy decision and the
verified downstream result. Tool results, retrieved documents, and agent
replies return as untrusted input to the next planning step; they cannot grant
new authority or bypass the gate. This flow applies to data reads and transfers
as well as state-changing actions.

The practical commitment is **bounded, observable, accountable autonomy**:

1. **Bound it.** A named owner states the purpose, data, tools, permitted
   effects, and unacceptable effects. An enforcement layer checks each
   consequential action against the caller, delegated scope, target, arguments,
   task, and limits before execution. Prompts help steer behavior; they do not
   grant authority.
2. **Prove it.** Tests show permitted paths work and prohibited paths fail;
   production evidence joins the proposed action, policy decision, approval,
   executed effect, and outcome. A declared gate with no denial test or runtime
   evidence is an assurance gap.
3. **Own it over time.** People decide whether the use case is appropriate,
   approve material changes and residual risk, monitor failures, and retain a
   way to stop, revoke, correct, and investigate. NIST's Govern–Map–Measure–
   Manage frame and the NCSC's design–development–deployment–operation
   lifecycle support this continuous view.
   [NIST GenAI Profile, 2024-07-26](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence)
   [NCSC secure AI development guidance](https://www.ncsc.gov.uk/collection/guidelines-secure-ai-system-development/guidelines)

## Scope and the system boundary

Apply this framework to internally built, vendor-hosted, embedded, and locally
configured applications that use a model to select actions, sequence work,
delegate, or produce recommendations that people routinely enact. Include
scheduled jobs, retries, service accounts, and background agents. Assess
confidentiality, integrity, availability, privacy, and business harm. The
review unit is the **deployed workflow**, not a repository or a model endpoint
alone.

Terms used throughout this framework:

- **Deployed workflow:** the specific combination of users or triggers,
  agent configuration, models, data sources, tools, credentials, environment,
  and downstream systems that can produce an effect.
- **Reachable path:** an operation that the deployed workflow can invoke under
  any supported request, schedule, retry, queue, or delegated run, even if it
  is absent from the usual interactive path. A safeguard may reduce risk
  without removing the underlying capability from classification.
- **Consequential action:** a write, outbound message or submission beyond the
  ordinary response to the authorized caller, code execution, transaction,
  or recommendation that a person routinely enacts with little independent
  judgment. Data reads and transfers still require their own controls.
- **Material workflow:** one that can affect a customer or external party,
  nonpublic or regulated data, funds, a production service or system of
  record, or a consequential human decision. Baseline controls still apply
  to other workflows.
- **Data linkage:** combining or correlating records, sources, or contexts to
  create a joined view or new inference. This includes retrieval assembly,
  identity matching, and reuse of memory across tasks or users.

Map the full path: user or trigger → model and orchestrator → retrieved
content, approved linkage, and memory → tools, Model Context Protocol (MCP)
servers, APIs, and other agents → downstream data and effects → human
decisions → logs and recovery.
Mark every place where less trusted content re-enters the agent's context.
The same tool can be safe for one user and unsafe for another; a permitted
read followed by a permitted send can become an unauthorized disclosure. The
application's threat model should cover that composition, including the
agent's influence over a human operator.
[OWASP, Securing Agentic Applications (2025), pp. 5–30](https://genai.owasp.org/resource/securing-agentic-applications-guide-1-0/)

Existing application-security duties still apply: authentication,
authorization, secrets, secure coding, dependency management, network
segmentation, logging, incident response, and vendor oversight. Agentic
workflows add repeated model-directed choices, untrusted natural-language
inputs, mutable memory, dynamic connectors, and non-human delegation. The
NCSC guidance explicitly spans secure design through maintenance.
[NCSC secure AI development guidance](https://www.ncsc.gov.uk/collection/guidelines-secure-ai-system-development/guidelines)

## Classify action consequence and assess data handling

Assign each use case **one action/effect tier (T0–T3)** based on the most
consequential reachable path. Separately assess data access, linkage, use,
and movement under the firm's authoritative data classification, privacy, and
records policies. The T tier sets the action-control posture. The data
assessment determines entitlements, permitted combinations and uses,
minimization, provider transfers, retention, and disclosure controls at every
T tier. Neither review replaces the other. These T labels are local working
labels for discussion, not an industry standard or a substitute for the
firm's risk process.

Test every reachable path, including scheduled jobs, retries, queues, and
service accounts, against all four tier descriptions and take the highest.
Include recommendations that people routinely enact without meaningful
independent judgment when determining the reachable consequence.

| Effect tier | Reachable capability | Typical control posture |
| --- | --- | --- |
| **T0 Advisory** | Returns answers or recommendations to the authorized caller; no downstream write, separate outbound communication, or routinely enacted consequential effect | Output quality tests, provenance, appropriate human review, and monitoring of use and reliance |
| **T1 Contained write** | Reversible write in a store the application owns | Baseline plus scoped write, validation, quotas, versioning, and restore test |
| **T2 External effect** | Initiates an outbound message, submission, or write beyond the application's boundary, with a meaningful correction path | Baseline and applicable write controls plus action-specific authorization, destination and content checks, approval where warranted, and verified effect logging |
| **T3 Irreversible or systemic** | Deletes or overwrites, executes code, moves money, changes a system of record, or otherwise causes hard-to-reverse impact | Baseline and applicable lower-tier controls plus separation of duties, isolation, tighter limits, explicit release approval and action approval for designated high-impact operations, independent testing, and practiced containment |

Output quality, provenance, and reliance checks apply whenever an agent
presents advice or generated content, regardless of its T tier.

Returning an answer to the authorized caller is not by itself a T2 outbound
action; its disclosure risk is assessed under the separate data controls.
Sending a new email, posting publicly, or submitting to another system is an
external effect. If that effect cannot be meaningfully corrected, classify it
as T3.

### Data access, linkage, and handling assessment

Record the firm's data label for each reachable source and output, the
approved purpose, the *effective* user and agent entitlements, the number and
type of records reachable, and every destination, including the model
provider, replies, tools, memory, and logs. Include scheduled paths and
exports. Record which sources or contexts may be combined and what new
information their combination may reveal. Assess aggregate and cross-tenant
exposure even if individual records have a less restrictive label. Apply the
firm's requirements for personal or regulated data, consent or notice,
retention, residency, and deletion where applicable. A prohibited read,
linkage, or transfer is a control failure at any T tier. Permission to read
each input separately does not itself authorize their linkage or release of
the resulting inference.

For each permitted linkage pattern, the data-handling record should identify
the sources or source classes, matching fields or keys where used, purpose,
population or tenant boundary, derived outputs, recipients, destinations,
retention, and applicable data-owner and privacy decisions. A general rule can
cover unrestricted public sources; restricted data or cross-context matching
needs a narrower rule. **This framework's pre-assembly linkage rule** requires
the application to enforce approved combinations before assembling a shared
model context or joining records through a tool.
It must check the caller, task, purpose, and source permissions at that point,
including for scheduled and delegated runs. Deny
an unapproved combination or pause when its authority cannot be determined.
Carry source-use restrictions and provenance into derived results, then assess
whether the new result needs a more restrictive label or handling under firm
policy before it informs an action or is stored, sent, or returned. Reassess
when a new source, matching method, purpose, recipient, or memory path changes
the linkage.
The placement of the check before assembly is this framework's design choice,
informed by NIST AC-4(1)'s attribute-based information-flow control and PT-3's
purpose restrictions for personally identifiable information.
[NIST SP 800-53 Rev. 5, AC-4(1) and PT-3](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf)

Bind data retrieval and movement to the caller's identity, task, purpose,
tenant, and source permissions. Where retrieval is used, a data owner should
be able to show that filters enforce those permissions and that replies,
tool chains, memory, and telemetry cannot carry data outside approved paths.
Public data still needs provenance and output-integrity checks. Keep
credentials and secrets outside model context. NIST's profile explicitly
includes data privacy; OWASP's agentic guide treats memory and tools as
security boundaries.
[NIST GenAI Profile, 2024-07-26](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence)
[OWASP, Securing Agentic Applications (2025)](https://genai.owasp.org/resource/securing-agentic-applications-guide-1-0/)

Examples: a public FAQ assistant with no writes may be `T0`; a read-only
assistant with access to a broad client corpus may also be `T0`, but needs
strict entitlements, approved data linkages and flows, and exfiltration tests
before launch. A public-data deployment agent able to delete infrastructure is `T3` and
needs strong action controls. A one-click recommendation can carry the T
consequence of the human action. Sandbox strength reduces risk but does not
erase the underlying code-execution capability. Reassess the action tier and
data handling when tools, users, sources, linkages, autonomy, model, provider,
or deployment path change.

### Action-control triggers for the draft framework

Apply the baseline row to every agent and the applicable controls for each
reachable capability. The highest T tier sets the overall review posture; a
T3 workflow still applies T1 or T2 controls to its contained writes and
external effects. Test restore only for an operation that can be restored;
use containment and reconciliation evidence for irreversible effects. The
table proposes a production entry bar for discussion, not a firm policy.
“Evidence” means a reviewer can inspect a design, test result, configuration,
or sampled production record—not merely a statement in a prompt or diagram.
The tables state when controls apply and what evidence to review. The
pre-assembly linkage rule above and the action-authorization and human-approval
contracts below define those requirements in detail.

| Trigger | Added control decision | Minimum review evidence |
| --- | --- | --- |
| **Every agent** | Register the owner and approved purpose; map identities, data, tools, effects, and destinations; apply the identity and action-authorization contract below; set limits, traceability, stop path, and a functional/security evaluation. | Inventory entry, data/action map, denied-call test, trace sample, named operator and runbook. |
| **T1 or above** | Bound each write and prevent duplicate effects. Require versioning and tested restore for contained writes; prefer a draft, preview, or rollback path for other state changes where supported. | Write policy, idempotency or replay test, restoration result for contained writes; otherwise recovery or reconciliation evidence. |
| **T2 or above** | Constrain external destination and content; verify the downstream effect; apply the human-approval rule below where warranted. | Egress policy, approval rule, external-effect trace, retry test. |
| **T3** | Apply the human-approval rule below to designated high-impact actions; tighten runtime isolation and cumulative limits; independently test containment. | Approval-role design, negative and adversarial tests, stop/recovery exercise, named risk decision. |

### Data-control triggers at every action tier

Use the firm's data labels and policies, not a second local tier scale. Apply
the baseline row and every matching data-condition row. These rows identify
conditions that call for additional review and evidence; the firm's
requirements take precedence.

| Data condition | Added control decision | Minimum review evidence |
| --- | --- | --- |
| **Every workflow** | Map data sources and flows; verify source permissions, provenance, approved purpose, and outbound destinations. Deny unauthorized access or transfer. | Data-flow record, entitlement and destination-denial tests. |
| **Source or context linkage** | Apply the pre-assembly linkage rule in the data-handling assessment above. | Linkage rule, owner/privacy decision where applicable, permitted and denied combination tests, derived-output and egress test. |
| **Nonpublic data** | Approve model/provider use; minimize prompts, exports, and logs; set retention and log-access rules. | Provider and retention decisions, access test, redacted trace sample. |
| **Personal, customer/client, proprietary, regulated, or broad cross-tenant data** | Apply user/tenant and, where needed, record/field permissions; narrow reach and egress; test leakage through replies, tools, memory, and telemetry; obtain data-owner and privacy review as applicable. | Effective-access inventory, cross-tenant and exfiltration tests, data-owner decision, disclosure-response path. |
| **Credentials or secrets** | Broker outside prompts, model context, memory, and ordinary logs; scope and revoke access. | Secret-flow design, scope and revocation test. |

Scale, exposure to untrusted users, unattended autonomy, operational
criticality, and human reliance can raise review and test depth; consequential
human-mediated effects can also raise the T tier. Privacy and data-specific
regulatory obligations remain direct requirements of the data assessment. At
execution, action and data controls meet: a permitted read followed by a
permitted send must still be denied when the resulting disclosure is
unauthorized. Human approval follows the action-specific rule below rather
than a blanket rule for all external communications.

## Control objectives and enforcement architecture

Implement the flow above with a typed intent contract and an
application-controlled enforcement layer. The pre-assembly linkage rule in the
data-handling assessment and the identity, action-authorization, and
human-approval contracts below define the checks for reads, linkages,
transfers, and tool effects. Apply them on retries, delegated calls, and
asynchronous workers; record the decision and verify the actual effect. A
gateway, another model, or a human may recommend an action, but the
application-controlled layer remains the final enforcement point. The table
summarizes control boundaries and review evidence.
[OWASP, Securing Agentic Applications (2025), pp. 30–45](https://genai.owasp.org/resource/securing-agentic-applications-guide-1-0/)

| Boundary | Recommended control | Evidence an owner should be able to show |
| --- | --- | --- |
| **Registration and ownership** | Inventory each production agent, owner, use case, model, runtime, tools/MCP servers, data sources, credentials, environments, and downstream systems. Discover unregistered instances and reconcile them to the inventory. | Current record, discovery coverage and exception queue. |
| **User and agent identity** | Authenticate the caller and agent separately. Bind delegation to the user, task, tenant, scopes, and lifetime; use short-lived credentials and revocation. Avoid shared privileged identities. | Identity flow, token scope, entitlement tests, access review. |
| **Action authorization** | Apply the identity and action-authorization contract below at the executor. | Policy version, denied-call tests, policy-decision and effect records. |
| **Data, retrieval, linkage, and memory** | Apply source entitlements and the pre-assembly linkage rule above; check derived results before use or release. Minimize data sent to the model/provider. Label sensitivity and provenance; partition memory by user/tenant; validate and expire durable memories; control export and deletion. | Entitlement and linkage-denial tests, data-flow map, derived-output test, retention rules, memory-write and deletion tests. |
| **Untrusted content and outputs** | Treat user input, web pages, files, search results, tool descriptions/results, and agent messages as data. Keep them from changing authority; validate structured outputs and escape content before rendering or executing it. | Injection and output-handling tests, review of every data-to-command transition. |
| **Tools, MCP, and delegation** | Approve connectors and tool schemas; authenticate servers; constrain scopes and egress. Reauthorize each delegated call and prevent a sub-agent from receiving broader authority. For MCP, address token audience, proxy consent, server-side request forgery (SSRF) through metadata fetches, and local server risk. | Connector registry, auth configuration, tool-chain tests, runtime traces. |
| **Runtime and resilience** | Isolate code execution and risky tools; restrict network and filesystem access; set step, time, spend, rate, and concurrency limits. Provide cancellation, circuit breakers, and a safe fallback. | Sandbox tests, limit alarms, kill-switch and recovery exercise. |
| **Model and provider** | Approve the model/provider for the data and use case. Review service data use, retention, residency, subprocessors, model-change notice, content and misuse safeguards, fallback, and exit path. Evaluate task quality and safety when a model or routing policy changes. | Provider assessment, data-processing decision, model/version record, regression results. |
| **Supply chain and change** | Record and approve model, framework, prompt, skill, dependency, dataset, MCP server, and policy versions; pin components where supported and track vendor-controlled changes. Protect build/deploy permissions; test material changes before promotion. | Version manifest, provenance, approval and rollback record. |
| **Human oversight** | Apply the human-approval and oversight contract below. | Approval and rejection events, escalation path, reviewer training and capacity. |
| **Telemetry and response** | Join task, actor, agent, model/policy version, data source, proposed tool call, policy result, approval, executed effect, and outcome. Alert on unusual access or action patterns. Minimize or redact sensitive log content and set retention/access rules. | Trace sample, alert tests, incident runbook, revocation and restoration drill. |

### Identity and action-authorization contract

- Give each deployed agent a distinct workload identity. Give separately
  authorized instances their own identity; otherwise attach an immutable run
  ID so an action can be attributed to one execution. Record the requesting
  human or service, delegation source, purpose, task, tenant, scope, expiry,
  and parent run when an agent delegates. Do not use a shared administrator
  credential as the agent's ordinary identity.
- Grant temporary access only when the task needs it. The effective authority
  at an action is the intersection of caller entitlement, agent capability,
  delegated task scope, resource policy, time, and budget. A sub-agent receives
  the same or narrower intersection, never an implicit increase. Recheck it
  on each call, including retries, queues, scheduled runs, and direct calls
  that bypass a model gateway.
- At the executor, allowlist typed operations and authorize each proposed
  action against the effective authority above. Validate the operation,
  target, typed arguments, destination, task context, and per-call and
  cumulative limits at the needed granularity: tool, resource, record, and
  field where entitlements differ. Deny by default; a prior decision does not
  authorize a changed action or scope. Keep the decision outside the model; a
  prompt or a second model can advise but cannot replace the enforceable check.
  Missing identity, expired delegation, policy timeout, or an indeterminate
  decision denies or pauses the action. Test each denial path.

### Human approval and oversight contract

The adopting organization defines which operations need affirmative human
approval, who may approve them, and the applicable thresholds. Apply that rule
to T2 external effects when impact or uncertainty warrants approval. For
designated T3 high-impact actions, such as payments, deployments, deletion, or
privilege changes, separate proposal, approval, and execution. Show the
authorized reviewer the proposed effect, material evidence, uncertainty, and
alternatives; give them time and authority to reject it. Bind approval to the
effect reviewed and obtain fresh approval if it materially changes before
execution. Record approvals and rejections, provide escalation, and monitor
reviewer capacity, rejection rate, and signs of rubber-stamping. Approval does
not replace the executor's action authorization or data-use checks.

### Data, tool, and model boundary

- Expose approved, narrow tool operations with typed schemas, parameter
  validation, and safe defaults. Treat unrestricted shell, arbitrary URL fetch,
  generic API proxy, and dynamic tool installation as high-risk capabilities.
  Approve MCP servers and clients; validate server identity, token audience, consent,
  scopes, and metadata fetches. Tool descriptions and results are untrusted
  content, not new policy.
  [MCP security best practices, 2026-07-28](https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices)
- Enforce document-level entitlement during retrieval and after assembly;
  enforce record/field restrictions where needed. Apply the pre-assembly
  linkage rule above to retrieval assembly, tool joins, and memory reuse.
  Label source provenance, freshness, and derived-output restrictions.
  Partition memory by user, tenant, and purpose; do not let memory create an
  unapproved cross-task or cross-user linkage. Allow an owner to review,
  correct, expire, and delete durable memory. Minimize or redact sensitive
  fields before model/provider transfer and in outputs and logs.
- Broker secrets outside prompts, memory, model context, and ordinary logs.
  Scope and rotate them; revoke them when a run or connector is compromised.
  Restrict network destinations, protocols, request volume, and data volume.
  Check outputs and tool arguments for sensitive data before an external
  effect; block or hold a disallowed transfer for review. Test the same
  leakage paths through memory and telemetry. Validate generated commands,
  code, messages, transactions, and structured data before use.
  Detectors for suspicious instructions can add a signal, but entitlement,
  validation, and egress controls must hold when detection misses an attack.
  [OWASP, Securing Agentic Applications (2025), pp. 30–45](https://genai.owasp.org/resource/securing-agentic-applications-guide-1-0/)

### Safe execution and failure behavior

- For every state-changing operation that can be retried or replayed, define
  a stable idempotency key or durable deduplication record tied to the business
  request. Reuse it on retries. If the downstream system cannot guarantee
  idempotency, reconcile an ambiguous outcome before any retry. Set cumulative
  limits for amount, affected records, calls, tokens, time, concurrency, and
  child-agent depth; constrain external destinations separately.
- Prefer preview, draft, staged rollout, soft deletion, versioning, and
  rollback where the downstream system supports them. These reduce impact;
  they do not lower the underlying T tier. Pause or deny when identity,
  authorization, tool integrity, or data needed for a consequential decision
  is uncertain. Route unresolved business judgment to an authorized human.
- Make the stop path real: block new tool calls, cancel active runs and queued
  work, revoke or disable credentials and connectors, and isolate affected
  workloads where needed. Practice this with a test run and verify that no
  downstream effects continue. Maintain a safe manual route for material
  workflows when the model, tool, or policy service is unavailable. The manual
  route needs its own authorization and audit trail; it must not silently
  bypass a failed policy check.

These are control objectives, not a demand for one vendor or one gateway. A
gateway may see model traffic without observing every client-side tool effect;
join gateway, application, connector, and downstream records where necessary.
The owner must identify any path the chosen control placement cannot observe
or block and provide an alternate enforcement and evidence source for it.

Security review should join privacy, legal, data, and model-risk review when
the application handles personal or regulated information, affects people or
customers, or makes consequential recommendations. Specify lawful use,
notice, consent, retention, human contestability, and downstream records
where applicable to the use case. Record those decisions rather than assuming
a tool permission or a successful security test resolves them. The NIST
profile includes data privacy and other trustworthiness risks beyond cyber
compromise.
[NIST GenAI Profile, 2024-07-26](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence)

## Governance: decisions across the lifecycle

An adopting organization should assign each decision below to a named role
and governance forum. If no forum covers a decision, designate a decision
maker before release. The roles are proposed responsibilities, not assertions
about current firm ownership. NIST's profile makes governance a continuing
function; the NCSC guidance adds deployment and operation to development
review.
[NIST GenAI Profile, 2024-07-26](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence)
[NCSC secure AI development guidance](https://www.ncsc.gov.uk/collection/guidelines-secure-ai-system-development/guidelines)

| Stage | Framework decision and record | Accountable role to name |
| --- | --- | --- |
| **Intake** | Is this use case appropriate for an agent? Define users, permitted and prohibited purposes, autonomy mode, expected benefit, unacceptable outcomes, action inventory and T tier, data labels and flows, planned linkages and derived outputs, entitlements and destinations, scale, regulatory obligations, human reliance, and owners for the workflow, tools, and datasets. | Business/application owner |
| **Design** | Approve the trust-boundary and threat model, delegation and data/tool contracts, linkage purpose and source combinations, approval roles, provider and vendor terms, privacy/records decisions, user notice and correction path, and manual continuity design. | Application owner with security, platform, data/privacy, and relevant risk owners |
| **Build** | Implement controls at enforcement points in separated development, test, and production environments; maintain versioned prompts, policies, tools, models, knowledge sources, and tests; verify dependencies and third-party components; resolve material findings or document exceptions. | Engineering owner |
| **Release** | Review functional and adversarial evidence, denial and failure-mode tests, entitlement, linkage, derived-output, and exfiltration tests appropriate to reachable data and its permitted use, applicable approval and idempotency paths, rollback, stop exercise, and operational readiness. Decide whether to launch and at what scale. | Named release and risk decision makers under local process |
| **Operate** | Reconcile runtime inventory; monitor controls, drift, exceptions, incidents, cost, and quality. Recertify agent and human identities, tool/data permissions and linkages, approvers, vendors, and exceptions on a risk-based cadence and after material changes; rehearse continuity. | Service owner and operators, with security and risk challenge |
| **Retire** | Revoke identities/tokens, disable scheduled triggers, remove connectors, dispose of data and memory per retention rules, and preserve required audit evidence. | Service owner with platform and data owners |

Before production approval, the adopting organization must record the local
parameters this framework leaves open: which operations need action approval,
who may approve them, transaction and cumulative limits, permitted data
linkages and provider paths, evidence and monitoring thresholds, review
cadence, retention, and incident-notification duties. These decisions belong
to named owners and enforceable configuration, never to the model's discretion.

The platform team should provide reusable identity, connector registration,
policy enforcement, traces, and emergency controls. The application owner
remains accountable for its specific use case, data, user reliance, and
effective downstream actions. Security tests and challenges the control
design. Risk/governance records the decision, exception owner, expiry, and
reassessment trigger. An exception should name the uncovered path and
compensating control; a generic “human in the loop” statement is insufficient.
Data owners confirm access, permitted linkage, and use; vendor/procurement and
privacy owners review provider data use, model-training terms, retention,
residency, subcontractors, security evidence, and incident-notification
obligations when applicable. For user-facing or consequential decisions, name
who provides
notice, explanation, correction, appeal, and escalation. Include applicable
records-management and legal-hold duties. Each material workflow needs an
operator-owned manual procedure that can accept new work, track pending work,
and reconcile effects after an outage.

## Assurance and evidence before and after launch

The minimum review packet for a material agent should be small enough to
maintain: an owner and use-case record; architecture and data/action map;
threat model; T action tier and separate data-handling record, including
approved linkages and derived outputs where applicable; control-to-enforcement
mapping; test and evaluation report; approval/exception record; trace example;
and incident, rollback, and
manual-continuity runbooks. Source-code review, where accessible, is one input
to this packet; vendor-hosted workflows also need contract, configuration,
and runtime evidence. Runtime identity, data entitlements, approval
behavior, downstream effects, and operator response require deployment
evidence as well.
[OWASP, Securing Agentic Applications (2025), pp. 65–77](https://genai.owasp.org/resource/securing-agentic-applications-guide-1-0/)

Define a correlated audit schema for the action path: time and run ID;
requesting actor and agent workload/instance; delegation and task reference;
model, tool, data-source, and policy versions; linkage-rule reference; source
provenance and freshness; material data transformations and derived-output
handling; proposed operation and target; linkage and action policy decisions;
execution ID; downstream result; and verified outcome.
Protect the event's integrity and access. Keep raw prompts, retrieved content,
and sensitive payloads only when necessary, approved, redacted or
access-controlled, and subject to retention rules. References or hashes can
support reconstruction without turning the audit store into another
unrestricted copy of customer data. Verify that
gateway, application, connector, and downstream records reconcile for a
sampled run.

Test at least these cases against the deployed path, with representative
users and data:

- A poisoned user input, document, tool result, memory entry, or inter-agent
  message asks the agent to change goals, call a different tool, disclose
  data, or write malicious memory. The action and data gates deny the
  unauthorized effect even if the model proposes it.
- A user requests data outside their entitlement; a sub-agent or scheduled job
  attempts the same read under another credential. Repeat at record/field
  granularity where the source has different permissions within one record.
- A user may read two sources separately but requests a prohibited join,
  cross-tenant identity match, or new inference. Verify that assembly is
  denied before both sources enter one context; verify an approved combination
  retains source restrictions and receives the required derived-output
  handling before reply, storage, or transfer.
- Two individually permitted actions form a harmful chain, such as broad read
  followed by external send. Test the combined policy and data-flow decision,
  including output, tool-argument, memory, and log leakage.
- A malicious or misconfigured connector changes its tool description,
  presents a token for the wrong audience, attempts an unapproved destination,
  or uses an MCP proxy as a confused deputy. Verify server/client checks and
  downstream authorization.
- A high-impact call is duplicated, retried after a timeout, races another
  call, changes its target or amount after approval, exceeds a cumulative or
  recursion limit, or bypasses the interactive approval path. Confirm a changed
  effect needs new approval and that one permitted effect occurs at most, or
  the workflow pauses and reconciles an unknown outcome.
- Identity is missing or expired, a policy service is unavailable, or a tool
  returns evidence too stale or incomplete for the decision. Confirm the
  workflow denies or pauses the protected operation and escalates for
  resolution without executing it under uncertain authority.
- A model, prompt, skill, tool schema, or MCP server changes. Confirm the
  release gate detects the change, verifies provenance, scans applicable
  dependencies, and reruns relevant functional, security, and reliability
  tests. Include conventional code review, vulnerability management, and
  penetration testing appropriate to the deployment.
- An incident requires stopping active and queued work, disabling a tool,
  revoking credentials, preserving proportionate evidence, locating affected
  records, meeting notification duties, verifying effects, and restoring a
  safe state. Exercise the manual procedure when the agent or policy service
  remains unavailable.

Measure **coverage and effectiveness**, with denominators: proportion of
deployed agents registered; reachable actions with enforced policy; protected
reads and transfers with enforced entitlement and egress policy; reachable
linkages with an enforced combination policy; negative tests that deny;
high-impact actions with attributable approvals; traces that reconcile to
downstream effects; time to detect and disable; and unresolved
exceptions by age and severity. Alert on an unregistered agent, unexpected
tool or destination, unusual access volume, privilege change, memory-write
spike, denial burst, or drift in output and action patterns. Track usefulness
and error rates too, because an authorized agent can still make a bad
recommendation. Red-team findings, model-based detectors, and prompt filters
add evidence; none alone proves that authorization holds. Each deployment must
specify where controls run, which effects are observable, and which measures
show that coverage is working.

## Review questions by role

- **Leaders:** “Which business outcome justifies the agent's authority, who
  owns the residual risk, and what evidence lets us expand or stop it?”
- **Builders:** “Show the most consequential reachable effect, the checks that
  bound it, the denial or quality test, and evidence of what occurred.”
- **Platform teams:** “Which shared controls make approved agents easy to
  register, authorize, observe, revoke, and update across frameworks?”
- **Risk and audit:** “Which controls exist, which were independently tested,
  which are observed in production, and which exceptions remain open?”
- **Operators:** “How do we spot drift or abuse, stop the agent, identify the
  affected data and actions, and verify recovery?”

## Appendix — Crosswalk of 48 control areas

This appendix maps 48 control areas to the proposed controls above. It is an
implementation aid, not a separate set of requirements, score, or claim of
current coverage.
`B` means every agent; `T1+` and `T2+` mean that action tier or higher;
“if used” means the capability is reachable. Owners are proposed roles:
**App** = business/application owner; **Eng** = engineering owner;
**Platform** = shared runtime/identity team; **Data** = data/privacy owner;
**Sec** = security; **Ops** = operator; **Risk** = relevant risk decision maker;
**Vendor** = third-party owner. The application owner coordinates evidence
even when another team operates the control.

| # | Review area | Framework control and example evidence | Proposed owner | Trigger |
| --- | --- | --- | --- | --- |
| 1 | Zero-trust architecture | Identity and action-authorization contract; denied-call test | Platform + App | B |
| 2 | Unique agent identity | Workload/instance identity and run record | Platform | B |
| 3 | Delegated authorization | Delegation source, scope, purpose, and expiry record | Platform + App | B |
| 4 | Least privilege | Effective-access inventory and review | App + Platform | B |
| 5 | Just-in-time access | Temporary grant and token-expiry record | Platform | B; shorter grants for high-impact actions or sensitive data |
| 6 | Authorization attenuation | Identity and action-authorization contract; delegated-scope denial test | App + Platform | If delegation |
| 7 | User-bound permissions | Identity and action-authorization contract; entitlement test | App | B |
| 8 | Fine-grained policy | Identity and action-authorization contract; record/field denial test where needed | App + Data | B; record/field checks where entitlements vary |
| 9 | Separation of duties | Human approval and oversight contract; role-separation trace | App + Risk | T3 designated actions |
| 10 | Human approval gates | Human approval and oversight contract; approval-binding and rejection tests | App + Risk | T2+ when warranted; T3 designated actions |
| 11 | Risk-tiered autonomy | T tier, autonomy mode, scale, human reliance, and control decision; separate data-handling record | App + Risk | B |
| 12 | Tool allowlisting | Approved connector and action registry | App + Platform | B |
| 13 | Secure tool interfaces | Typed schema, argument validator, unsafe-call denial | Eng | If tools are reachable |
| 14 | Sandboxing and isolation | Runtime isolation and escape test | Platform | Code execution or risky tool |
| 15 | Network egress | Destination, protocol, and volume policy test | Platform + Data | B; tighter for T2+ sends or nonpublic-data transfer |
| 16 | Secrets management | Broker, scope, rotation, and revocation test | Platform | B |
| 17 | Data minimization | Field-minimization and redaction test | Data + App | When nonpublic or personal data is used |
| 18 | Context and tenant isolation | Cross-user/tenant memory, linkage, and credential test | App + Platform | Multi-user/tenant or durable memory |
| 19 | Memory governance | Provenance, review, correction, expiry, deletion test | App + Data | If durable memory is used |
| 20 | Retrieval-augmented generation (RAG) security | Pre-assembly linkage rule; source-entitlement, approved-assembly, and untrusted-result tests | App + Data | If retrieval is used |
| 21 | Prompt-injection defenses | Poisoned-content test plus independent action gate | App + Sec | If untrusted content enters context |
| 22 | Output validation | Typed output and pre-effect validation test | Eng + App | If generated output drives an effect |
| 23 | Data-loss prevention | Source and derived-output exfiltration tests across replies, tools, logs, and memory | Data + App | When nonpublic or newly sensitive derived data can reach those paths |
| 24 | Transaction and impact limits | Amount, record, time, and cumulative-limit tests | App + Platform | T1+ |
| 25 | Reversibility controls | Draft, preview, version, restore or rollback test | App + Ops | T1+ where supported |
| 26 | Idempotency and replay | Duplicate, retry, and ambiguous-outcome tests | Eng | T1+ |
| 27 | Rate limits and quotas | Call, token, spend, concurrency budget test | Platform | B |
| 28 | Loop and recursion | Step, depth, duration, and child-budget test | Platform | If loops or delegation are reachable |
| 29 | Independent policy layer | Identity and action-authorization contract; model/gateway-bypass denial test | Platform + App | B |
| 30 | Audit trails | Correlated, privacy-controlled action and linkage-decision trace | Ops + App | B; protect trace content by firm data policy |
| 31 | Traceability and provenance | Source, permission, linkage, freshness, and transformation reference | App + Data | If external data is used |
| 32 | Continuous monitoring | Unexpected tool, egress, access, privilege, and drift alert test | Ops + Sec | B |
| 33 | Security evaluation and red teaming | Injection, exfiltration, tool, MCP, and multi-agent scenarios | Sec + App | Scale to T tier, data exposure, and untrusted inputs |
| 34 | Predeployment assurance | Task-specific functional, safety, security, and reliability report | App + Sec | B |
| 35 | Change and model governance | Version manifest and release regression evidence | App + Platform | Material change |
| 36 | Supply-chain security | Component provenance, pinning, and scan result | Eng + Sec | B |
| 37 | Secure software lifecycle | Threat model, code review, vulnerability and penetration evidence | Eng + Sec | B; depth scales with T tier and data exposure |
| 38 | Fallback and fail-safe behavior | Missing-identity, policy-timeout, and stale-data tests | Platform + App | B |
| 39 | Kill switch and containment | Active-run stop, queue block, credential/tool revocation drill | Ops + Platform | B; prioritize high-impact actions or sensitive-data exposure |
| 40 | Incident response readiness | Runbook, evidence preservation, notification, recovery exercise | Ops + Sec | B |
| 41 | Named accountability | Workflow, tool, data, and operator owners in inventory | App | B |
| 42 | Approved-use boundaries | Permitted/prohibited purpose, users, data, systems, autonomy record | App + Risk | B |
| 43 | Risk assessment and classification | T tier plus scale and reliance; separate firm data-label, entitlement, linkage, derived-output, flow, and privacy record | App + Risk | B |
| 44 | Privacy and records governance | Use, consent, retention, deletion, residency, records decision | Data | Personal/regulated data or records duty |
| 45 | Third-party governance | Provider data use, training, controls, subcontractor, breach terms | Vendor + Data | If third party is used |
| 46 | Periodic access and control review | Recertification of agent/user access, tools, data, approvers, exceptions | App + Ops | B; cadence by risk |
| 47 | User transparency and contestability | Notice, explanation, correction, appeal and escalation path | App + Risk | User-facing/consequential use |
| 48 | Business continuity | Tested manual queue, pending-work and effect reconciliation | Ops + App | Material workflow |

## Reference basis and status

The framework's classifications, control objectives, proposed roles, and
evidence expectations are stated in this document. An adopting organization
must map them to its authoritative data policies and approval process. The
following public materials inform the design and offer additional reading;
they do not supply unstated framework requirements:

- [NIST AI Risk Management Framework: Generative AI Profile (2024)](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence) — lifecycle governance, evaluation, and privacy.
- [NIST SP 800-53 Rev. 5, AC-4(1) and PT-3](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r5.pdf) — attribute-based information-flow decisions and purpose-bound processing of personally identifiable information.
- [UK NCSC Guidelines for Secure AI System Development](https://www.ncsc.gov.uk/collection/guidelines-secure-ai-system-development/guidelines) — security across design, development, deployment, and operation.
- [OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) — agentic security failure modes.
- [OWASP Securing Agentic Applications Guide (2025)](https://genai.owasp.org/resource/securing-agentic-applications-guide-1-0/) — threat modeling, security controls, and assurance.
- [OWASP Agent Control Standard (2026)](https://genai.owasp.org/resource/agent-control-standard-acs/) — related runtime hooks, policy enforcement, and observability work; not a prerequisite for this framework.
- [Model Context Protocol Security Best Practices (2026-07-28)](https://modelcontextprotocol.io/docs/2026-07-28/tutorials/security/security_best_practices) — MCP-specific token, proxy, and server risks.

The T tiers, control triggers, lifecycle packet, and metrics are proposals for
discussion. They are not an approved firm standard or evidence that a control
has been implemented or independently verified.
