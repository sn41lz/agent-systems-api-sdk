# Agent Platform API and SDKs

A typed agent API with SSE streaming, rate limiting, OpenAPI, and Python and TypeScript clients.

**Status: planning — implementation has not started.** This README describes intended scope and acceptance criteria. It does not establish working behavior, completed tests, security guarantees, or measured performance.

Part of an eight-repository agent-systems portfolio.

## Planned scope

- Versioned run, cancellation, status, and event endpoints with a documented error model.
- SSE event contracts, client disconnect handling, and bounded buffering.
- Authentication, tenant-aware rate limits, an OpenAPI contract, and typed Python/TypeScript SDKs.

## Planned setup and observability

The implementation is intended to provide a `docker-compose.yml` and `Makefile` so a reviewer can use `make demo` for a deterministic offline demonstration and `make test` for offline regression checks. **These commands and their supporting files are not implemented yet.** Live-model runs will be optional and separately documented with configuration and cost assumptions.

Every model call and tool execution is intended to emit an OpenTelemetry span. Planned aggregate reporting includes token usage, estimated USD per 1,000 requests, p50/p95 latency, and p50/p95 time to first token (TTFT) where streaming applies. Offline fixtures can verify instrumentation and behavior; live-provider performance requires separate measurements.

Implementation choices will remain inspectable, with explicit contracts and documented dependencies.

See [PRODUCTION_CHECKLIST.md](PRODUCTION_CHECKLIST.md) for shared acceptance criteria.

## Proposed architecture and tradeoffs

An authenticated gateway validates requests and applies tenant quotas before dispatching runs to a runtime adapter. A versioned event stream feeds SSE clients with explicit event IDs and terminal outcomes. The OpenAPI contract and streaming event schema define the Python and TypeScript client interfaces.

Streaming improves perceived responsiveness but adds disconnect, replay, and backpressure complexity. Generated clients reduce contract drift, while streaming may require carefully maintained extensions. Rate limits need a defined scope and consistency model across gateway instances.

## Roadmap and acceptance criteria

Complete the shared reproducibility and observability checklist, then implement and verify these project-specific criteria:

- [ ] Validate request, response, and error examples against the versioned contract.
- [ ] Exercise SSE event order, terminal events, disconnect behavior, cancellation, and slow-client bounds.
- [ ] Verify authentication, tenant boundaries, and documented rate-limit behavior with deterministic fixtures.
- [ ] Run matching Python and TypeScript examples and compare latency, throughput, and error rate at the same offered load.

## Candidate failure modes

These are design risks to investigate, **not tested or observed findings**:

- A slow or disconnected client causes unbounded buffering or leaves unnecessary work running.
- SSE reconnection duplicates or drops events without a defined recovery contract.
- Contract drift breaks an SDK, or distributed rate limits allow tenant interference.

The implementation will add reproduction cases, actual outcomes, and mitigations as evidence becomes available.

## Baseline versus optimized metrics

No benchmark has been run. The following comparison is planned:

- **Baseline:** A declared initial API/SDK revision under a fixed streaming and request workload.
- **Optimized:** A candidate revision under identical load, retaining authentication and tenant isolation requirements.

| Metric | Baseline | Optimized |
| --- | --- | --- |
| Input/output/total tokens per request | Not yet measured | Not yet measured |
| Estimated USD per 1,000 requests | Not yet measured | Not yet measured |
| End-to-end latency p50 | Not yet measured | Not yet measured |
| End-to-end latency p95 | Not yet measured | Not yet measured |
| Streaming TTFT p50, where applicable | Not yet measured | Not yet measured |
| Streaming TTFT p95, where applicable | Not yet measured | Not yet measured |
| Project-specific quality and failure rate | Not yet measured | Not yet measured |

Future results must identify the workload revision, model/provider configuration, environment, concurrency, sample counts, and quality criteria. Cost estimates must disclose pricing date and assumptions. Report the complete workload, including errors and retries, and retain raw results so baseline and optimized runs can be reproduced. Do not substitute target values for measurements.

## Portfolio

- [Raw Agent Tool Loop](https://github.com/sn41lz/agent-systems-tool-loop)
- [Context Window Engine](https://github.com/sn41lz/agent-systems-context-window)
- [Hierarchical Agent Memory](https://github.com/sn41lz/agent-systems-hierarchical-memory)
- [Multi-Agent Orchestrator](https://github.com/sn41lz/agent-systems-multi-agent-orchestrator)
- [Agent Evaluation Harness](https://github.com/sn41lz/agent-systems-eval-harness)
- [Secure Code Sandbox](https://github.com/sn41lz/agent-systems-secure-sandbox)
- [Durable Agent Runtime](https://github.com/sn41lz/agent-systems-durable-runtime)
- [Agent Platform API and SDKs](https://github.com/sn41lz/agent-systems-api-sdk)
