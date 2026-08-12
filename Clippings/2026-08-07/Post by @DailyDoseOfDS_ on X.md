---
title: "Post by @DailyDoseOfDS_ on X"
source: "https://x.com/DailyDoseOfDS_/status/2085659743587365161"
author:
  - "[[@DailyDoseOfDS_]]"
published: 2026-05-08
created: 2026-08-08
description: "RAG vs. Graph RAG vs. Agentic RAG, clearly explained! Standard RAG embeds documents into vectors and retrieves the most similar chunks via"
tags:
  - "clippings"
---
RAG vs. Graph RAG vs. Agentic RAG, clearly explained!

Standard RAG embeds documents into vectors and retrieves the most similar chunks via similarity search. For direct factual lookups, this works well.

But it breaks down when a query needs to connect facts spread across multiple documents. Similarity search retrieves individual chunks, not the relationships between them.

Graph RAG adds a knowledge graph layer on top.

→ During indexing, an LLM extracts entities and relationships from the documents.

→ During retrieval, the system traverses these connections instead of relying on embedding similarity alone.

This is what enables multi-hop queries.

Say a vector DB stores three facts about internal services:

↳ "The checkout service uses payments API."

↳ "The payments API runs on cluster-3."

↳ "Cluster-3 is scheduled for maintenance on Friday."

Someone asks: "Will the checkout service be affected by Friday's maintenance?"

Vector search can likely retrieve facts 1 and 3 because the query mentions "checkout service" and "Friday maintenance."

But it will miss fact 2, which connects the payments API to cluster-3.

That middle fact sits too far from the query in embedding space. It mentions neither "checkout" nor "maintenance," so it never makes it into the retrieved context.

A knowledge graph connects these as linked entities, and graph traversal finds the full path in one query.

Agentic RAG takes a different approach entirely.

Instead of a fixed retrieval pipeline, an LLM agent decides at query time which tools to invoke, which sources to query, and in what order.

Check the visual below to understand the three architectures thoroughly.

One thing to note here is that these three aren't levels of sophistication that you need to graduate through.

Instead, they solve different query types.

↳ Single-hop factual lookups → standard RAG

↳ Multi-hop relationship queries → Graph RAG

↳ Dynamic multi-source tasks with tool use → Agentic RAG

\----

Each of these architectures gets better when the underlying retrieval layer is efficient.

We wrote about a new RAG approach that cuts corpus size by 40x, reduces tokens per query by 3x, and improves vector search relevance by 2.3x.

The article is quoted below.

> **Akshay @akshay\_pachaar** · 2026-05-08
> 
> ![記事のカバー画像](https://pbs.twimg.com/media/HHzOinabYAEF8Wo?format=jpg&name=large)

---

## Comments

> **Damian @ToLeaveNoStone** · [2026-08-07](https://x.com/ToLeaveNoStone/status/2085733103566291323)
> 
> Agents don’t need to remember more; they need to know how to forget. RAG is passive. I built Hypotree, an MCP server that wires memory as a DAG, prunes dead branches automatically, and draws its own conclusions by process of elimination. Deep dive here:
> 
> > **Damian @ToLeaveNoStone** · 2026-08-07
> > 
> > ![記事のカバー画像](https://pbs.twimg.com/media/HPGkVCdWEAI23Fq?format=jpg&name=large)

> **Rishi @RishiUvaach** · [2026-08-07](https://x.com/RishiUvaach/status/2085686495504220361)
> 
> Evaluation should change with the architecture too: retrieval recall for standard RAG, path completeness for Graph RAG, and tool selection plus recovery for agentic RAG. One answer-accuracy metric can hide where each system fails.

> **Argona @Argona0x** · [2026-08-07](https://x.com/Argona0x/status/2085661298654634175)
> 
> graph rag's real cost hits at index time, not query time — entity extraction across a big corpus ate most of my week.