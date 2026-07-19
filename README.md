# Distributed Systems Study

A public learning laboratory for understanding distributed systems through
theory, failure analysis, backend examples, small experiments, and System
Design interview practice.

This repository is part of a **Learning in Public** journey.

## Purpose

The goal is not to reproduce or summarize a textbook chapter by chapter.

The goal is to learn how to:

- reason about partial failures and uncertainty;
- state system assumptions explicitly;
- distinguish safety, liveness, availability, reliability, and consistency;
- understand time, ordering, coordination, consensus, and replication;
- design robust backend services;
- explain architectural trade-offs during technical interviews.

## Primary reference

The main theoretical reference is:

**Sukumar Ghosh, _Distributed Systems: An Algorithmic Approach_,
Second Edition, CRC Press, 2015.**

The book itself is not included in this repository.

All notes, explanations, diagrams, exercises, examples, and laboratories
published here are original learning artifacts.

## Learning method

Each topic follows a repeatable loop:

1. identify the assumptions and system model;
2. build an intuitive explanation;
3. formalize properties and guarantees;
4. analyze at least one failure scenario;
5. apply the concept to a realistic backend system;
6. answer exercises and interview questions;
7. verify the concept with a focused laboratory when useful;
8. record lessons learned and remaining gaps.

## Repository structure

```text
.
├── docs/
│   ├── glossary/
│   ├── interviews/
│   ├── lessons/
│   ├── reviews/
│   ├── progress.md
│   └── roadmap.md
├── labs/
├── project/
└── sources/
```

## Principles

- No textbook PDFs, copied chapters, or extended quotations.
- Every commit should leave behind a useful learning artifact.
- Examples should expose failures, not only the happy path.
- Technology choices must be justified by assumptions and trade-offs.
- A timeout is evidence of uncertainty, not proof of failure.
- More nodes do not automatically imply greater availability.
- Complex laboratories are split into small, observable experiments.

## Current status

Repository foundation and study roadmap.
