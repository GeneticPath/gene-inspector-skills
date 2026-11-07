---
name: gene-inspector-pro
description: Explore a user's connected Gene Inspector Pro genome through its read-only MCP server. Use when the user asks to inspect their Gene Inspector Pro genes, variants, panels, pathways, or source evidence, or to compare findings in a connected personal genome. Do not use for general genetics questions that do not require the user's Gene Inspector Pro data.
license: Apache-2.0
metadata:
  author: GeneticPath
  version: "1.0.0"
---

# Gene Inspector Pro

Use the Gene Inspector Pro MCP tools to answer the user's question from the minimum necessary portion of their connected genome. Keep the work read-only, evidence-aware, and non-clinical.

## Establish access

If the Gene Inspector Pro tools are unavailable, tell the user to connect the remote MCP endpoint from the [Gene Inspector Pro setup guide](https://gene-inspector.pro/mcp):

```text
https://mcp.gene-inspector.pro/mcp
```

Let the user sign in and approve access in Gene Inspector Pro. Never ask for, accept, or handle their password, access token, or other credential.

For the first personal-genome request in a session:

1. Call `whoami` to confirm the connected account and read-only scopes.
2. Call `consent_status` before requesting genome data.
3. If consent is not active, stop personal-genome reads and relay the first-party Gene Inspector action link when the error supplies one.

Do not repeat these checks on every follow-up when the current session has already established them.

## Select the genome safely

Call `list_genomes` when no genome has been established in the current conversation or when the user asks to change genomes.

- If one owned genome is available, use it.
- If only the demo genome is available, state that the answer uses demo data.
- If several genomes are available and the user's intended genome is unclear, ask one concise question using human-readable labels. Do not expose internal `patientId` values in the reply.
- Use `select_genome` only after the intended genome is unambiguous. It changes session selection for up to eight hours; it does not modify the Gene Inspector Pro account or genome.
- Use `get_selected_genome` to confirm session selection when needed. An explicit `patientId` may be passed to read tools when the user is intentionally comparing genomes.

Never guess which person's genome the user meant.

## Route the request

Choose the narrowest tool path that answers the question:

| User intent | Tool path |
| --- | --- |
| Check the connected identity or scopes | `whoami` |
| Check whether personal-DNA consent is active | `consent_status` |
| Discover accessible genomes or change the active one | `list_genomes`, then `select_genome` if needed |
| Confirm the active genome | `get_selected_genome` |
| Find available system, calculated, or user panels | `list_panels` |
| Review variants in one panel or pathway | `get_panel_variants` with the chosen `panelId` |
| Review one gene | `get_gene_variants` with its standard gene symbol |

Resolve human panel names through `list_panels`; do not invent a `panelId`. Normalize a clearly supplied gene symbol to uppercase, but ask when a common name could map to more than one gene.

For a broad pathway question, start with the matching panel and retrieve individual genes only when the user asks for gene-level detail or the panel result makes it necessary.

## Paginate deliberately

Panel and gene variant results are paginated. The default page size is 25 and the maximum is 100.

- For a complete or exhaustive request, follow `nextCursor` until it is `null`.
- For an overview, begin with one bounded page and say when the answer is based on a partial result.
- Pass the returned cursor unchanged to the next call. Do not infer it from item counts.
- Keep `includeRaw` false unless the user explicitly needs raw flat fields that are absent from the display-ready result.
- Narrow the request before retrieving a large genome-wide result.

## Explain findings responsibly

Keep these layers distinct:

1. **Observed finding:** genotype or variant data returned by Gene Inspector Pro.
2. **Annotation and evidence:** consequence, frequency, predictor, clinical record, publication, or other evidence actually present in the tool result.
3. **Interpretation and limits:** what the evidence may suggest, what conflicts, and what the result cannot establish.

Use source links returned by Gene Inspector Pro. Never invent a citation, evidence grade, or clinical classification that is absent from the result.

Treat grouping or predicted importance as review prioritization, not a diagnosis or proof of harm. Predictor scores can use different directions and scales; do not average them into a shared risk score or count agreement as independent proof without support in the returned evidence.

Do not:

- diagnose or rule out a disease;
- prescribe a drug, supplement, dose, or treatment;
- turn a variant into a numeric disease probability without an appropriate returned source;
- imply that missing data proves a variant is biologically absent;
- claim that Gene Inspector Pro replaces a clinician or genetic counselor.

When health implications are discussed, include a brief statement that the result is for research and education and should be reviewed with a qualified healthcare professional before health decisions.

## Minimize sensitive data

Personal genetic data is sensitive. Retrieve and repeat only what is necessary for the user's question.

- Refer to genomes by human label, not internal identifiers.
- Omit account email, grant IDs, session metadata, and unrelated variants from the answer.
- Do not copy results into another service, file, issue, or long-lived note unless the user explicitly requests that destination and understands the sensitivity.
- Do not use one person's genome to answer a question about another person.
- Never work around authentication, consent, ownership, or tool access errors.

## Handle gaps and failures

- **Authentication failure:** ask the user to reconnect the Gene Inspector Pro MCP server; do not request credentials.
- **Consent or eligibility failure:** state what Gene Inspector Pro requires and preserve any first-party action link returned by the tool.
- **Ambiguous genome or panel:** ask one targeted question instead of choosing silently.
- **No variant returned:** explain that file type, coverage, calling, filters, processing, and pagination can affect what appears.
- **Tool or network failure:** report the failed read and stop. Do not fill the gap from memory as though it came from the user's genome.

## Answer shape

Lead with the direct answer. For substantive reviews, use this compact order:

1. Main findings.
2. Evidence and source links.
3. Uncertainty and data limitations.
4. One useful next question or narrower follow-up, when appropriate.

Name the selected genome or demo context without exposing its internal ID. State whether pagination was complete.
