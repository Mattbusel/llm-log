# llm-log

Structured JSONL logging for LLM API calls. Drop in one header.

![C++17](https://img.shields.io/badge/C%2B%2B-17-blue.svg)
![License MIT](https://img.shields.io/badge/license-MIT-green.svg)
![Single Header](https://img.shields.io/badge/single-header-orange.svg)

## Quickstart

```cpp
#define LLM_LOG_IMPLEMENTATION
#include "llm_log.hpp"

llm::Logger logger;  // writes to llm_calls.jsonl

{
    llm::Logger::ScopedCall call(logger, "gpt-4o-mini", user_prompt);
    std::string response = call_my_llm(user_prompt);
    call.set_response(response);
    call.set_tokens(120, 45);
    call.set_cost(0.000082);
}  // <- logs here automatically with elapsed latency
```

## Installation

Copy `include/llm_log.hpp`. No external dependencies.

## API

### LogConfig
```cpp
llm::LogConfig cfg;
cfg.filepath           = "llm_calls.jsonl";  // "" = stdout only
cfg.also_stdout        = false;
cfg.pretty_print       = false;
cfg.max_prompt_chars   = 0;   // 0 = no truncation
cfg.max_response_chars = 0;
```

### Direct logging
```cpp
llm::LogEntry e;
e.model = "gpt-4o-mini"; e.prompt = "..."; e.response = "...";
e.input_tokens = 100; e.output_tokens = 50;
e.cost_usd = 0.0001; e.latency_ms = 320.0; e.success = true;
e.metadata = {{"user_id", "u123"}};
logger.log(e);
```

### ScopedCall (recommended)
```cpp
llm::Logger::ScopedCall call(logger, model, prompt, metadata);
// ... make your LLM call ...
call.set_response(response);
call.set_tokens(in, out);
call.set_cost(usd);
// call.set_error("msg") on failure
// destructor logs with accurate latency
```

### Query + Summarize
```cpp
llm::Logger::QueryOptions opts;
opts.model_filter = "gpt-4o-mini";
opts.success_only = true;
opts.limit = 50;
auto entries = logger.query(opts);

auto s = logger.summarize();
s.total_calls; s.successful_calls; s.total_cost_usd;
s.avg_latency_ms; s.calls_per_model;
```

## Examples

- [`examples/basic_log.cpp`](examples/basic_log.cpp) — log 3 mock calls, print JSONL
- [`examples/scoped_call.cpp`](examples/scoped_call.cpp) — RAII ScopedCall wrapping a mock LLM call
- [`examples/query_log.cpp`](examples/query_log.cpp) — write 20 entries, query + summarize

## Building

```bash
cmake -B build && cmake --build build
./build/basic_log
./build/scoped_call
./build/query_log
```

## Requirements

C++17. No external dependencies.

## See Also

| Repo | Purpose |
|------|---------|
| [llm-stream](https://github.com/Mattbusel/llm-stream) | SSE streaming |
| [llm-cache](https://github.com/Mattbusel/llm-cache) | Response caching |
| [llm-cost](https://github.com/Mattbusel/llm-cost) | Token cost estimation |
| [llm-retry](https://github.com/Mattbusel/llm-retry) | Retry + circuit breaker |
| [llm-format](https://github.com/Mattbusel/llm-format) | Markdown/code formatting |
| [llm-embed](https://github.com/Mattbusel/llm-embed) | Embeddings + cosine similarity |
| [llm-pool](https://github.com/Mattbusel/llm-pool) | Connection pooling |
| [llm-log](https://github.com/Mattbusel/llm-log) | Structured logging |
| [llm-template](https://github.com/Mattbusel/llm-template) | Prompt templates |
| [llm-agent](https://github.com/Mattbusel/llm-agent) | Tool-use agent loop |
| [llm-rag](https://github.com/Mattbusel/llm-rag) | Retrieval-augmented generation |
| [llm-eval](https://github.com/Mattbusel/llm-eval) | Output evaluation |
| [llm-chat](https://github.com/Mattbusel/llm-chat) | Multi-turn chat |
| [llm-vision](https://github.com/Mattbusel/llm-vision) | Vision/image inputs |
| [llm-mock](https://github.com/Mattbusel/llm-mock) | Mock LLM for testing |
| [llm-router](https://github.com/Mattbusel/llm-router) | Model routing |
| [llm-guard](https://github.com/Mattbusel/llm-guard) | Content moderation |
| [llm-compress](https://github.com/Mattbusel/llm-compress) | Prompt compression |
| [llm-batch](https://github.com/Mattbusel/llm-batch) | Batch processing |
| [llm-audio](https://github.com/Mattbusel/llm-audio) | Audio transcription/TTS |
| [llm-finetune](https://github.com/Mattbusel/llm-finetune) | Fine-tuning jobs |
| [llm-rank](https://github.com/Mattbusel/llm-rank) | Passage reranking |
| [llm-parse](https://github.com/Mattbusel/llm-parse) | HTML/markdown parsing |
| [llm-trace](https://github.com/Mattbusel/llm-trace) | Distributed tracing |
| [llm-ab](https://github.com/Mattbusel/llm-ab) | A/B testing |
| [llm-json](https://github.com/Mattbusel/llm-json) | JSON parsing/building |

## License

MIT — see [LICENSE](LICENSE).
