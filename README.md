# ClaimVerifier

A GenLayer Intelligent Contract that checks a written claim against a web page.
Validators fetch the page, ask an LLM for a verdict, and must reach consensus on it.

## Verdicts

| Verdict | Meaning |
|---|---|
| `SUPPORTED` | The page clearly states the claim is true |
| `REFUTED` | The page clearly states the claim is false |
| `INCONCLUSIVE` | The page does not settle it (also the fallback for any unexpected output) |

## Methods

| Method | Type | Description |
|---|---|---|
| `verify_claim(claim, source_url)` | write | Fetches `source_url` (https only), judges the claim, stores the result |
| `get_claim(claim_id)` | view | Full JSON record: claim, source, verdict, requester |
| `get_verdict(claim_id)` | view | Verdict string only |
| `get_claim_count()` | view | Number of stored claims |

## What GenLayer features it shows

- `gl.nondet.web.render` to read a live web page inside a contract
- `gl.nondet.exec_prompt` to call an LLM
- `gl.eq_principle.prompt_comparative` so validators agree on a non-deterministic result

## Safety notes

- Claim and page text are wrapped in `<claim>` / `<page>` tags and the prompt treats them as data (prompt-injection mitigation).
- Limits: claim 500 chars, URL 500 chars, page text 6000 chars.
- Only `https://` sources are accepted.
- A verdict is only as reliable as the source page. Choose reputable sources.

## Deploy (GenLayer Studio)

1. Open https://studio.genlayer.com and upload `claim_verifier.py`.
2. Deploy (no constructor arguments).

## Tested in GenLayer Studio

- `verify_claim("Algeria is a country in North Africa", "https://en.wikipedia.org/wiki/Algeria")` returned `SUPPORTED`
- `verify_claim("The capital of Algeria is Oran", "https://en.wikipedia.org/wiki/Algeria")` returned `REFUTED`
- `get_claim_count()` returned `2` after both calls

The `INCONCLUSIVE` case has not been tested yet. Not audited.
