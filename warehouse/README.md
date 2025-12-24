# Warehouse Layering Strategy

This project uses a simple, explicit warehouse layering model to support
clarity, testability, and evolution over time. The structure reflects
analytics engineering best practices rather than application-specific
constraints.

The warehouse is organized into three datasets:

- `raw`
- `stage`
- `mart`

Each layer has a clear purpose and a narrow set of responsibilities.

---

## `raw` — Source-Aligned Data

The `raw` dataset contains data ingested directly from source systems with
minimal transformation.

**Principles:**
- Preserve source fidelity
- Avoid business logic
- Prefer append-only or immutable patterns
- Capture ingestion metadata (load time, source, version where applicable)

**What belongs here:**
- USGS earthquake event records
- Wikimedia pageview statistics
- JSON or flattened representations of source payloads

**What does *not* belong here:**
- Aggregations
- Joins across sources
- Derived metrics
- Interpretive logic

The goal of `raw` is traceability and debuggability, not usability.

---

## `stage` — Normalized and Conformed Data

The `stage` dataset contains cleaned, normalized, and conformed representations
of raw data.

**Principles:**
- One model per source entity
- Stable column naming and data types
- Time normalization and alignment
- Basic data quality enforcement

**Typical transformations:**
- Timestamp normalization (UTC alignment, truncation)
- Field renaming and typing
- Deduplication
- Null and range handling

Models in `stage` are designed to be reusable building blocks for downstream
analytics. They are expected to be well-tested and documented.

---

## `mart` — Analytics-Ready Models

The `mart` dataset contains analytics-ready models intended for analysis,
reporting, and interpretation.

**Principles:**
- Business-meaningful grain
- Explicit assumptions
- Documented metrics
- Designed for consumption, not reuse

**What belongs here:**
- Time-aligned datasets combining seismic events and pageview activity
- Aggregated views at hourly or daily grain
- Correlation and lag-analysis inputs

Models in `mart` may encode analytical intent and project-specific logic. They
are expected to be understandable by readers without deep knowledge of the raw
sources.

---

## Testing and Documentation

Across all layers:
- Schema tests enforce expected structure
- Data tests validate assumptions (ranges, uniqueness, completeness)
- Documentation explains *why* models exist, not just *what* they contain

Testing intensity increases from `raw` → `stage` → `mart`.

---

## Design Intent

This layering strategy is deliberately conservative. It favors:
- Transparency over cleverness
- Explicitness over abstraction
- Evolution over premature optimization

The structure supports incremental development, clear reasoning, and future
extension without requiring redesign.

---

## Non-Goals

This warehouse design explicitly avoids:
- Complex star-schema enforcement
- Metric abstraction frameworks
- Real-time serving requirements
- Application-specific optimizations

Those concerns can be layered on later if warranted.

---

This structure is intended as a reference pattern for small, event-driven
analytics projects on Google Cloud Platform.

