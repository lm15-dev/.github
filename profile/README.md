<p align="center">
  <img src="https://raw.githubusercontent.com/lm15-dev/.github/main/assets/banners/banner-1200x300.png" alt="lm15" width="600">
</p>

<h3 align="center">One request and response model for every AI model provider.<br>The same behavior in Python, TypeScript, Rust and Go. No dependencies.</h3>

<p align="center">
  <a href="https://lm15.dev/docs/">Documentation</a> ·
  <a href="https://lm15.dev/playground/">Playground</a> ·
  <a href="https://github.com/lm15-dev/lm15-python">Python</a> ·
  <a href="https://github.com/lm15-dev/lm15-ts">TypeScript</a> ·
  <a href="https://github.com/lm15-dev/lm15-rs">Rust</a> ·
  <a href="https://github.com/lm15-dev/lm15-go">Go</a> ·
  <a href="https://github.com/lm15-dev/lm15-contract">Contract</a>
</p>

---

lm15 lets a program talk to OpenAI, Anthropic, Google Gemini, xAI, Groq,
DeepSeek, OpenRouter, Z.AI, Moonshot, Meta, the clouds (Azure, AWS Bedrock,
Google Vertex) and models on your own machine through one set of types.
Write a request once; change the model string to change the provider.

```python
from lm15 import LMRouter, Message, Request

router = LMRouter()   # API keys come from the environment
response = router.complete(Request(
    model="anthropic:claude-haiku-4-5",   # or "gpt-4.1-mini", "gemini:gemini-2.5-flash", "ollama:qwen3.5:0.8b"
    messages=(Message.user("What eats acorns at night?"),),
))
print(response.text)
```

It is a **foundation library**: typed requests, responses, stream events,
tools, media, errors and exact JSON, built on each language's standard
library. No hidden tool loop, no retries you didn't ask for, no prompt
templates. It is the layer you build your own opinions on.

## Languages

| Language | Version | Install |
|---|---|---|
| [Python](https://github.com/lm15-dev/lm15-python) | **1.0.1** stable | `pip install lm15` |
| [TypeScript](https://github.com/lm15-dev/lm15-ts) | 1.0.0-rc.1 | `npm install @lm15/lm15` |
| [Rust](https://github.com/lm15-dev/lm15-rs) | 1.0.0-rc.1 | `cargo add lm15` |
| [Go](https://github.com/lm15-dev/lm15-go) | v1.1.0-rc.1 | `go get github.com/lm15-dev/lm15-go@v1.1.0-rc.1` |
| [Julia](https://github.com/lm15-dev/lm15-jl) | in development | from GitHub |
| [R](https://github.com/lm15-dev/lm15-r) | API in design | — |

All four released languages pass **every check of the shared contract**
at the version they pin: the same program builds the same request and
reads the same answer from the same reply in every language. A login saved from one
language is used, and renewed, from another. Early ports in
[Java](https://github.com/lm15-dev/lm15-java),
[Ruby](https://github.com/lm15-dev/lm15-ruby),
[Swift](https://github.com/lm15-dev/lm15-swift) and
[.NET](https://github.com/lm15-dev/lm15-dotnet) were written against an
earlier contract and are not published.

## The universal type

Four nouns, and one more for streaming:

```
Part  →  Message  →  Request  ⇢  Response
                        ⇣ (streaming)
              start → Delta… → end
```

- **Part**: the atom of content, one of twelve kinds: `text`, `image`,
  `audio`, `video`, `document`, `binary`, `tool_call`, `tool_result`,
  `thinking`, `refusal`, `citation`, `data`.
- **Message**: a role (`user`, `assistant`, `developer`, `tool`) and parts.
- **Request**: a model, messages, and optionally `system`, `tools` and a
  `config` (length, temperature, reasoning, caching, tool choice,
  structured output…).
- **Response**: an assistant message, a `finish_reason` from a closed list,
  and `usage`, where a missing number means "the provider didn't say",
  never a silent `0`.
- **Delta**: while streaming, typed fragments between exactly one `start`
  and one `end` event. They assemble into the same Response a non-streamed
  call returns.

Provider-specific settings go in through `extensions` and provider-specific
data comes out through `provider_data`, both passed through untouched. When
a provider can't do what a request asks, lm15 either adapts and records what
it changed, or refuses before sending: never a silent drop.

## Providers

Direct APIs: OpenAI, Anthropic, Google Gemini (with Live sessions), xAI,
Groq, DeepSeek, OpenRouter, Z.AI, Moonshot / Kimi, Meta, TypeSafe. Clouds:
Azure (OpenAI and Anthropic), AWS Bedrock, Google Vertex AI. Local and
self-hosted: Ollama, vLLM, SGLang and any server that speaks OpenAI Chat
Completions. Accounts: ChatGPT (Codex), Claude, xAI, GitHub Copilot, Kimi
Code and OpenRouter sign-in.

Beyond chat: streaming, function and built-in tools, structured output,
judgments with probabilities, reasoning controls, prompt caching, files,
batches, image and speech generation, video, realtime sessions, the model
catalog, and reading an OpenAI Chat Completions request into lm15.

## The contract

[lm15-contract](https://github.com/lm15-dev/lm15-contract) is the single
source of truth; no implementation is.

- **A written specification**: every type, field, default and validation
  rule, 53 numbered invariants, 16 mapping rules, and the rules for exact
  JSON.
- **Recorded provider traffic**: real requests and replies, each with a
  receipt of when and against which model it was captured.
- **A language-neutral harness** that grades an implementation in 18
  directions (requests, responses, streams, errors, serialization,
  credentials, models, files, batches, caches, live sessions, sign-in…) and
  never trusts the implementation under test.
- **Evidence rules enforced by CI**: recorded traffic changes only with a
  new live capture; the specification changes only with a written decision.

## Footprint (Python, measured 2026-06-11)

| | install size | dependencies | cold import | memory after import |
|---|---:|---:|---:|---:|
| **lm15** | **0.5 MiB** | **0** | **152 ms** | **16.6 MiB** |
| openai | 18.0 MiB | 15 | 468 ms | 35.3 MiB |
| anthropic | 17.1 MiB | 15 | 589 ms | 41.2 MiB |
| google-genai | 37.2 MiB | 24 | 934 ms | 60.8 MiB |
| litellm | 133.0 MiB | 54 | 2298 ms | 161.0 MiB |

Method and full results:
[BENCHMARKS.md](https://github.com/lm15-dev/lm15-python/blob/main/benchmarks/BENCHMARKS.md).
