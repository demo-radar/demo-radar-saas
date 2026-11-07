# Engineering choices and what still needs evidence

I wanted an application that people could use, with an AI service I could
understand and operate. That meant deciding what to build, how to check its
answers, what to save, and what to do when a request failed.

This page describes the documented implementation and research. It is not a
fresh production audit. Some controls exist in code while their deployment
settings still need operator confirmation. Proposed improvements are marked
separately, rather than presented as features already delivered.

## Why these components

| Decision | Current approach | Alternative and trade-off |
| --- | --- | --- |
| AI service boundary | A separate Python service with bounded tasks, provider adapters, request validation and a persistent journal. | A gateway such as LiteLLM could help when many clients need shared provider routing. The current service still needs its task-specific review/recovery contract. Custom adapters reduce initial scope but leave maintenance with the project. |
| Operational visibility | Prometheus for service measurements and Grafana over curated database views for jobs, outcomes, usage and review activity. | A tracing product such as Langfuse could support deeper prompt/experiment inspection. The current dashboards answer operational questions, not every question about prompt quality. Retention, sensitive text and another service to maintain would need review. |
| Translation | DeepL through the backend, with saved/cached translations. | Translating with the generation model would combine two responsibilities and change the quality and cost questions. Separate translation adds a provider dependency but makes that work visible. |
| Map integration | React/TypeScript and a direct Leaflet adapter for map lifecycle and route layers. | A wrapper can reduce integration code. Owning the adapter gives explicit lifecycle and dependency choices, with corresponding test and maintenance work. |

The named alternatives explain the choices. They are not installed components
or claims that formal vendor trials were completed. I would add infrastructure
when a demonstrated need justifies it, rather than turn this application into
a larger platform in advance.

## What is configured and who can change it

The application backend owns event selection, staff permissions, translation
and publication. DemoLogic owns request validation, configured model access,
saved-answer lookup and inference admission. Mapping/routing providers handle
geographical requests. Prometheus and Grafana support the operator.

DemoLogic's authenticated catalog identifies supported tasks and profiles.
Jobs retain the configuration fingerprint used for their requests. This is a
bounded configuration catalog, not an enterprise tool registry or permission
for an agent to execute arbitrary tools. Adding such a registry would require
a real consumer and explicit ownership, access and support rules.

The browser does not receive provider credentials. Protected backend actions
check staff sessions and permissions. The AI service uses a separate service
credential. These boundaries do not prove that every deployment setting has
been independently audited.

## Checking whether suggestions are useful

The research compared rules, TF-IDF classifiers, natural-language inference
and hosted language models using reviewed reference labels. Recurring groups
were kept together in the relevant data splits to reduce near-duplicate
examples crossing between development and evaluation.

Precision asks how many suggested tags match the reference. Recall asks how
many reference tags were found. Coverage is the share of events with at least
one suggested tag, not correctness. Studies using different examples or reference revisions must remain
separate. One reviewer supplied the references, and some slogans need context
the model did not receive. No tested candidate met all original quality
targets together.

The public [methodology](https://github.com/demo-radar/demo-radar-logic/blob/main/docs/methodology.md)
and [results](https://github.com/demo-radar/demo-radar-logic/blob/main/docs/results/README.md)
describe the evidence and its limits. Raw research inputs remain private.

**Current checks:** software tests cover contracts, validation, saved answers,
failure/restart behavior and synthetic workflows. Public CI can run without
private data or paid provider calls. Those checks do not demonstrate that a new
model or prompt has maintained semantic accuracy.

**Next quality step:** define a reviewed reference set, score a candidate
against the accepted configuration, and agree what change blocks release.
Keep development and fresh evaluation examples distinct. This can be a private
release check while public CI retains safe fixtures. An LLM judge could assist
later, but would need comparison with human labels. It should not become an
unexamined source of truth.

Staff acceptance/edit/rejection rates are useful operational feedback. They are
not benchmark precision and do not establish time saved. Measuring active
review effort against the previous workflow remains separate work.

## Usage is not the same as a bill

Requests retain identities, configuration and known provider usage. The service
also supports paid-call admission using approved pricing and spending limits.
An estimated charge and an operator-reconciled charge are different states.
Unknown usage or an uncertain request is not counted as zero.

For a paid profile, the accounting path is:

1. Reserve an allowance before admitting a request.
2. Save the provider's reported input/output usage and the pricing policy used.
3. Calculate an estimate from that policy, or retain the reservation when the
   answer/usage remains unknown.
4. Reconcile uncertain charges with provider evidence before clearing them.

For a simple uncached token-priced request, the estimate is input tokens times
the input rate plus output tokens times the output rate. Cached or other
billable categories need the selected provider's actual policy. Dated pricing,
limits and reconciliation matter more than putting a currency symbol beside
a token counter.

**Planning example, not measured traffic:** assume 4,000 input and 200 output
tokens for each new tagging request. Five new requests would use 20,000 input
and 1,000 output tokens. Reusing two saved answers would leave three new calls,
or 12,000 input and 600 output tokens under the same assumptions. Repeated
admitted attempts add usage. Checking a saved answer is not another generation.

This example does not quote a provider price or forecast an invoice. An approved
free-tier profile has different admission rules from a paid profile. Hosting,
translation, mapping and operator time are separate costs. DeepL characters
must not be added to LLM token totals. Provider rates and account terms need
confirmation before a cash estimate is presented as current.

## AI risks and remaining work

| Risk | Current boundary | Remaining evidence or improvement |
| --- | --- | --- |
| Unsupported claims or hallucinations | Bounded response formats, allowed tags, source-event matching and staff review constrain publication. | A valid response can still be wrong. Add reviewed factuality/semantic regression cases. Routes are not proof of closures or safety. |
| Instructions hidden in slogans | Event text is input to a bounded task. The model has no authority to publish, fetch arbitrary data or execute application actions. | Add explicit adversarial examples and release criteria. Input formatting and human review do not prove prompt-injection resistance. |
| Personal information in free text | Requests use selected public fields and exclude staff notes. | Public titles/route text can still contain names or other personal information. Review minimization, retention and redaction needs. Do not claim a comprehensive PII-removal layer already exists. |
| Lost answers and repeated spending | Saved request IDs, lookup, admission limits and audited recovery preserve uncertain outcomes. | A timeout does not prove the remote call stopped. Continue testing crash/restart/connection-loss behavior. |

The near-term improvements are a reviewed semantic release check, adversarial
and sensitive-text cases, and a dated cost scenario using real observed usage
and approved provider rates. These are gaps to close, not reasons to claim the
current project has an automatic quality or safety guarantee.

See the public [service API](https://github.com/demo-radar/demo-radar-logic/blob/main/docs/service-api.md)
and [architecture](architecture.md) for the implementation boundaries.
