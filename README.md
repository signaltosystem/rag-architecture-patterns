# Why Most RAG Systems Fail in Production

RAG works in demos. It often breaks in production.

Most failures come from system design, not model quality. Weak retrieval, poor ranking, unbounded context, and limited observability create brittle pipelines that degrade under real traffic and changing data.

This repository focuses on architecture patterns, failure modes, and design tradeoffs for production RAG systems. It does not cover tutorials or starter implementations.

YouTube: https://www.youtube.com/@signal-to-system

## The Problem

Production RAG systems fail when retrieval is treated as a single step instead of a staged system.

Teams often rely on one retriever, a fixed top-k policy, and direct prompt assembly. That design is easy to ship and hard to operate. Errors compound quickly, and the model receives context that is noisy, incomplete, or irrelevant.

## Where It Breaks

- Ingestion quality degrades retrieval before the query starts
- Metadata drift makes filtering unreliable
- Vector search alone often returns related but unhelpful documents
- Top-k retrieval pushes low-value context into the prompt
- Duplicate passages waste context budget
- Weak evidence still produces confident answers
- Failures are hard to trace across pipeline stages

## Naive Architecture

```text
[Retriever] -> [Top-K Docs] -> [LLM]
```

This pattern has no ranking stage and no context control. Prompt quality depends almost entirely on the initial retrieval set.

## Better Design

```text
[Retriever] -> [Ranker] -> [Context Builder] -> [LLM]
```

This pattern separates retrieval from ranking and prompt assembly. It creates a cleaner boundary between search quality and generation quality.

## Design Checklist

- Treat ingestion as a pipeline, not a script
- Version content and metadata
- Use hybrid retrieval where possible
- Rerank before prompt assembly
- Deduplicate and bound context size
- Define abstain and fallback behavior
- Trace failures by stage

## Key Takeaways

- Production RAG is a systems problem
- Retrieval quality determines answer quality more often than prompt wording
- Ranking and context assembly should be explicit stages
- Observability is required to improve failure modes over time

See diagrams/ for visual architecture.
