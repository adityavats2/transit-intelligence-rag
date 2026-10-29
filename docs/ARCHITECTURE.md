# Transit Intelligence: Architecture Guide

## Purpose & Authority

This is the internal guide to the product's intent, scope, architectural constraints, and design decisions. It is the starting context for contributors and coding agents working on individual features.

Use this document to understand **what the product should do and why**. Use the current source code, tests, and configuration to establish **what is implemented today**. If implementation and this guide differ, do not assume the guide describes current behavior: identify the discrepancy and update the guide when an intentional product or architecture decision changes. Do not silently turn an unapproved proposal below into an implementation commitment.

Keep this guide focused on durable product and architecture context. It is not a user-facing README, setup tutorial, API reference, or file-by-file implementation manual.

## Product Intent

Transit Intelligence aims to provide specific, evidence-backed answers about the Ontario Line and its impacts on Toronto transit, especially transfers to Line 1 and Line 2 before and after the Ontario Line is completed.

The product will use publicly available documents from the Toronto Transit Commission (TTC), Metrolinx, the City of Toronto, and the Government of Ontario. It should help a user find and understand relevant information across those sources rather than present unsupported claims as fact.

The intended core is a focused research assistant for Ontario Line questions, not a general-purpose chatbot or a replacement for the source institutions' official information.

## Scope and answer expectations

### In scope

- Answer questions grounded in the project's approved public source documents.
- Retrieve relevant evidence and make it possible to trace material claims back to their sources.
- Address Ontario Line impacts on Line 1 and Line 2 transfer interchanges, including before/after-completion comparisons when the sources support them.
- Identify when evidence is incomplete, ambiguous, or conflicting.

### Out of scope unless explicitly decided

- Treating generated answers as official TTC, Metrolinx, City, or provincial guidance.
- Answering arbitrary questions unrelated to the project's source collection and transit focus.
- Presenting forecasts, interpretations, or calculations as source-stated facts.

### Required answer behavior

- Ground factual claims in retrieved source material and provide usable citations. Preserve source title and publisher, plus page, section, heading, or URL when available.
- Distinguish source-stated facts from calculations or interpretation; explain the basis for derived results.
- Say when the available evidence does not support a reliable answer. Do not fill evidence gaps with plausible-sounding detail.
- Surface source dates and conflicting or superseded information when relevant to the question.

These are product requirements, not claims that the current implementation already satisfies them.

## Architecture Overview

The intended system has two flows:

1. **Document preparation:** collect approved public documents; retain provenance; extract text and structure; create semantically coherent chunks; attach metadata; embed and index chunks for retrieval.
2. **Question answering:** interpret a user's question; retrieve relevant evidence; optionally use approved deterministic operations; generate an answer from the evidence; return the answer with citations and appropriate uncertainty.

The exact components, interfaces, and deployment topology are not yet selected. Treat this flow as the target architecture, not a description of working code.

## Data and ingestion principles

- Preserve the original source and enough provenance to locate each extracted passage again.
- Prefer document structure—such as headings, sections, chapters, and meaningful table boundaries—over arbitrary fixed-length splits when creating chunks.
- A chunk should preserve enough context to represent a coherent policy statement, budget item, construction milestone, or other source unit.
- Retain useful structural metadata with each chunk, such as document identity, publisher, publication date, section path, page or location, and source URL where available.
- Handle tables and extraction failures deliberately; do not imply that all PDF content is parsed correctly.
- Keep source text, derived chunks, embeddings, and generated answers distinguishable so results can be traced to source material.

The target starting strategy is structural chunking. The parsing approach, chunk-size limits, embedding model, update/deletion policy, and handling of scanned documents remain open decisions.

## Retrieval and answer generation principles

- Retrieval should find source passages relevant to the user's question; semantic similarity is a means of finding evidence, not proof that a passage answers the question.
- The answer-generation model must be constrained to the retrieved evidence and must not invent citations or unsupported details.
- If deterministic calculations or other tools are needed, expose only explicitly approved operations with defined inputs and outputs. Do not treat model-generated arbitrary code as a trusted execution plan.
- Make retrieval quality and answer quality independently testable.

The project intends to use Retrieval-Augmented Generation (RAG). The model provider, retrieval strategy, reranking approach, deterministic operations, and orchestration are not yet settled.

## Quality, evaluation, and failure behavior

Before calling the product reliable, evaluate at least:

- Whether relevant source evidence is retrieved for representative questions.
- Whether important answer claims are supported by the cited passages.
- Whether citations identify the correct source locations.
- Whether the system handles missing, conflicting, stale, or poorly extracted evidence without overstating certainty.
- Whether before/after comparisons distinguish source facts from derived reasoning.

Build a representative question-and-evidence set from the intended use cases as implementation progresses. Evaluation methods, acceptance thresholds, and monitoring are open decisions and should be recorded here when chosen.

Expected failure behavior is to report insufficient or uncertain evidence, not to fabricate an answer or imply the search was complete when it was not.

## Constraints and operational considerations

- Initial source scope is public material from TTC, Metrolinx, the City of Toronto, and the Government of Ontario.
- Respect source terms and preserve attribution and provenance.
- Keep credentials and other secrets out of source documents, logs, and version control.
- The system should make model, retrieval, and ingestion failures observable rather than silently presenting partial results as complete.

Hosting, privacy/data-retention requirements, update cadence, cost and latency targets, and production operations still need explicit decisions.

## Decision register

Record a decision here when it materially affects product behavior, data contracts, system boundaries, or operating costs. For each decision, capture its status, rationale, and consequences. Do not present a candidate as selected until it is explicitly marked **Decided**.

| Topic | Status | Current position |
|---|---|---|
| Product approach | Decided | Build a focused RAG product for evidence-backed Ontario Line answers, especially Line 1 and Line 2 transfer impacts. |
| Initial source institutions | Decided | TTC, Metrolinx, City of Toronto, and Government of Ontario public documents. |
| Initial chunking direction | Decided | Prefer structural boundaries over naive arbitrary chunking. |
| LLM provider | Open | Claude is a candidate; provider and model are not selected. |
| Vector database | Open | ChromaDB and LanceDB were considered; neither is selected. |
| Containerization/deployment | Open | Docker was proposed; deployment architecture is not selected. |
| Retrieval, embedding, and reranking | Open | Select and evaluate against representative source-grounded questions. |
| Evaluation thresholds and operations | Open | Define measurable quality criteria and operational requirements before making reliability claims. |

## Guidance for coding agents

1. Read this guide before designing a feature that affects product behavior, source data, retrieval, or answer generation.
2. Inspect the repository to learn the current implementation; this guide intentionally does not duplicate file-level details.
3. Preserve the answer, provenance, and evidence principles above. Call out conflicts or missing decisions rather than making consequential assumptions silently.
4. Update this document in the same change when an approved product or architecture decision changes. Mark unresolved choices **Open** and record decided choices with a brief rationale.
5. Keep unrelated implementation details in code, tests, or focused supporting documentation; avoid duplicating architecture decisions elsewhere.
