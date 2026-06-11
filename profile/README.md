<p align="center">
  <img src="https://raw.githubusercontent.com/lm15-dev/.github/main/assets/banners/banner-1200x300.png" alt="lm15" width="600">
</p>

<h3 align="center">One canonical representation for LLM APIs. Specified, conformance-tested, multi-language. Zero dependencies.</h3>

<p align="center">
  <a href="https://github.com/lm15-dev/lm15-contract">Contract</a> ·
  <a href="https://github.com/lm15-dev/lm15-python">Python</a> ·
  <a href="https://github.com/lm15-dev/lm15-rs">Rust</a> ·
  <a href="https://github.com/lm15-dev/lm15-go">Go</a> ·
  <a href="https://github.com/lm15-dev/lm15-ts">TypeScript</a>
</p>

---

## What

lm15 is a **low-level foundation library** for talking to LLM providers: one canonical type system (`Part → Message → Request → Response`), exact serialization, a provider-agnostic error taxonomy, and thin adapters per provider — built on the standard library alone. No SDK dependencies, no magic call loops, no DSL. It is the layer you build *your* opinions on top of.

## Why

Every LLM SDK reinvents the same request/response shapes, drags in dozens of dependencies, and behaves subtly differently across providers and languages. lm15 inverts that: the **behavior is the spec**, the spec is machine-checked, and every implementation in every language must produce byte-identical wire requests and identical canonical parses.

## How

The [lm15-contract](https://github.com/lm15-dev/lm15-contract) repository is the single source of truth — not any implementation:

- **A written constitution** ([AUTHORITY.md](https://github.com/lm15-dev/lm15-contract/blob/main/AUTHORITY.md)) defines which artifact wins when things disagree: live provider behavior > provider docs > fixtures > implementations.
- **A ratified spec**: 61 types, 25 closed vocabularies, 49 numbered invariants, normative serde and mapping rules.
- **A 304-check conformance corpus** — 110 request, 102 response, 8 stream, 16 error, 68 serde checks — driven by a language-neutral harness that never trusts the implementation under test.
- **Evidence discipline, enforced by CI**: wire fixtures change only with a live-capture receipt; canonical fixtures change only with a spec citation; every fixture carries provenance.

The Python package is the *reference implementation*, but it holds **no oracle authority** — when it disagrees with the contract, Python is wrong.

## Languages

| Implementation | Status | Conformance |
|---|---|---|
| [lm15-python](https://github.com/lm15-dev/lm15-python) | **1.0.0a1** — reference implementation, full client layer | 304/304 |
| [lm15-rs](https://github.com/lm15-dev/lm15-rs) (Rust) | Client layer landed (blocking HTTP, complete/stream) | 304/304 |
| [lm15-go](https://github.com/lm15-dev/lm15-go) (Go) | Client layer landed (net/http, complete/stream) | 304/304 |
| [lm15-ts](https://github.com/lm15-dev/lm15-ts) (TypeScript) | Client layer landed (fetch, complete/stream) | 304/304 |
| lm15-jl (Julia) | Planned — pre-conformance port frozen, will be rebuilt against the contract | — |

## Providers

Covered today, with identical canonical behavior:

- **OpenAI** (Responses API) and **OpenAI Codex**
- **Anthropic** and **Claude Code**
- **Google Gemini** (including Live/WebSocket sessions)
- **Every Chat Completions-compatible server** — Groq, OpenRouter, DeepSeek, vLLM, SGLang, Ollama — via one dialect adapter with typed compatibility policies

Beyond chat: streaming, tools (function + builtin), reasoning, caching, embeddings, file upload, batch, image generation, audio generation, live sessions.

## Performance (Python, measured 2026-06-11)

The suite is auto-generated and re-run against real competitors — full methodology in [BENCHMARKS.md](https://github.com/lm15-dev/lm15-python/blob/main/benchmarks/BENCHMARKS.md):

| | install size | transitive deps | cold import | import RSS |
|---|---:|---:|---:|---:|
| **lm15** | **0.5 MiB** | **0** | **152 ms** | **16.6 MiB** |
| openai | 18.0 MiB | 15 | 468 ms | 35.3 MiB |
| anthropic | 17.1 MiB | 15 | 589 ms | 41.2 MiB |
| google-genai | 37.2 MiB | 24 | 934 ms | 60.8 MiB |
| litellm | 133.0 MiB | 54 | 2298 ms | 161.0 MiB |

- **Time-to-first-byte tax vs raw urllib: ~0 ms** — the abstraction costs nothing on the wire.
- **Connection pooling**: 99 ms/call pooled vs 178 ms/call fresh-connection urllib against Groq.
- Hot path: request build in 12 µs, stream pipeline at ~110k events/s.

## Where this is going

- **1.0 stable** for the Python reference: the chat core (types, serde, errors, request building, response parsing, streaming) is already frozen by the contract; 1.0.0a1 is hardening toward final.
- **Client-layer parity** for Rust, Go, and TypeScript releases, each gated on the same 304-check corpus.
- **Julia** rebuilt against the contract.
- **Broader provider surface** under the same evidence discipline — new providers land as fixtures with live receipts first, code second.
