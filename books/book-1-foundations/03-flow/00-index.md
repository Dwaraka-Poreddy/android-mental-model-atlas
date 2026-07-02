# DOC 3 — Reactive Streams & Flow

**Purpose:** How Data Flows

This document teaches how data moves through asynchronous systems using Flows. It explains the mental model of streams, producers, consumers, and operators.

## Concepts Included

- [Why Flow Exists](./01-why-flow-exists.md)
- [Flow](./02-flow.md)
- [Flow vs Suspend Function](./03-flow-vs-suspend-function.md)
- [Cold Flow](./04-cold-flow.md)
- [Producing And Consuming Flow](./05-producing-and-consuming-flow.md)
- [Emit](./06-emit.md)
- [Collect](./07-collect.md)
- [Flow Pipeline](./08-flow-pipeline.md)
- [Producer](./09-producer.md)
- [Upstream](./10-upstream.md)
- [Downstream](./11-downstream.md)
- [Collector](./12-collector.md)
- [Flow Operators](./13-flow-operators.md)
- [Transforming Values](./14-transforming-values.md)
- [Map](./15-map.md)
- [Filtering Values](./16-filtering-values.md)
- [Filter](./17-filter.md)
- [Reducing Noise](./18-reducing-noise.md)
- [Debounce](./19-debounce.md)
- [Combining Flows](./20-combining-flows.md)
- [Combine](./21-combine.md)
- [Zip](./22-zip.md)
- [From Values To Flows](./23-from-values-to-flows.md)
- [Keeping Only The Latest Result](./24-keeping-only-the-latest-result.md)
- [FlatMapLatest](./25-flat-map-latest.md)
- [Executing Flow Pipelines](./26-executing-flow-pipelines.md)
- [Intermediate Operators](./27-intermediate-operators.md)
- [Terminal Operators](./28-terminal-operators.md)
- [Flow In Android Architecture](./29-flow-in-android-architecture.md)

## Reading Order

Concepts should be read in the order listed because:

1. **What is Flow?** (Why Flow Exists, Flow, Cold Flow)
2. **How do you use it?** (Producing, Consuming, Emit, Collect)
3. **How does data move?** (Flow Pipeline, Producer, Collector)
4. **How do you transform data?** (Operators, Map, Filter, Debounce)
5. **How do you combine streams?** (Combine, Zip, FlatMapLatest)
6. **How do you execute pipelines?** (Terminal Operators)

## How This Fits Into The Atlas

This document answers: **How does data flow through asynchronous systems?**

Flow is the foundation for state management in Android (learned in DOC 4) and the UI layer (learned in later books).

The next document, **DOC 4 — State Management**, teaches how to use Flow for representing application state.

---

**Start with:** [Why Flow Exists](./01-why-flow-exists.md)
