---
title: "I tested an assumption before building a data analysis tool"
date: 2026-10-29 09:00:00 -0700
categories: experiments
description: "I tested CSVs from 1,000 to 100,000 rows with GPT-5.4 mini. Input tokens barely changed. Here's what that meant for my data analysis tool idea."
excerpt: "I thought processing CSVs outside the model's context could make my tool cheaper. A small experiment showed Code Interpreter was already doing that with Python."
canonical_url: "https://cdtran.github.io/2026/10/29/csv-analysis-input-tokens/"
author:
  name: Chris Tran
  url: "https://cdtran.github.io/about/"
tags: [CSV analysis, AI, input tokens, Code Interpreter, experiments]
seo:
  type: BlogPosting
---

**TL;DR**

- I wanted to build a cheaper data analysis tool by moving calculations outside the AI's context
- I tested CSVs with 1,000, 10,000, and 100,000 rows
- Input tokens barely changed, even with 100 times more rows
- Code Interpreter already processed the full files with Python. All nine answers were correct
- Each CSV cost about $0.04 for three questions, including estimated container charges. This test didn't show a cost advantage for my idea

## The idea

I wanted to build a data analysis tool. The MVP would take in CSVs and let people ask questions about their data. Eventually, I wanted to expand to querying databases.

I thought I could start with CSVs to get traction. But I wasn't sure what my tool would offer that someone couldn't already get by uploading a file to an AI tool.

I thought I could make the analysis cheaper. My assumption was that when an LLM analyzes an uploaded CSV, the entire dataset goes into the model's context. If that were true, more rows would mean more input tokens and a higher cost of analysis.

If this was true, maybe I could build something more efficient. My idea was to turn a user's question into a query, run it outside the LLM, then feed the results back for analysis.

I wanted to test my assumptions before building out the product.

## The experiment

I created three CSVs with the same columns: `sale_id`, `date`, `product_id`, and `revenue`. The files had 1,000, 10,000, and 100,000 rows.

For each file, I asked the same three questions in the same order:

1. How much revenue is generated per day?
2. How many distinct products are there?
3. How many sales are there total?

I used GPT-5.4 mini through the OpenAI API with Code Interpreter and low reasoning. Each file got a fresh conversation and a fresh container for running Python. The three questions shared that conversation and container.

I recorded input tokens, output tokens, estimated costs, latency, and whether the answers were correct. I instructed the model to load the file, calculate the answer, and print the results in one Python execution per question.

## The input tokens told the story

Here's how many input tokens each question used:

| CSV rows | Daily revenue | Distinct products | Total sales |
| ---: | ---: | ---: | ---: |
| 1,000 | 2,157 | 3,242 | 3,037 |
| 10,000 | 2,374 | 3,182 | 3,313 |
| 100,000 | 2,311 | 3,288 | 3,313 |

The largest file had 100 times as many rows as the smallest. The input-token counts barely changed.

For the daily-revenue question, the 1,000-row file used 2,157 input tokens. The 100,000-row file used 2,311. That's about a 7% increase for 100 times the data.

For the total-sales question, the 10,000-row and 100,000-row files used exactly the same number of input tokens: 3,313.

For each question, adding rows didn't cause input tokens to grow proportionally.

## Costs, speed, and accuracy

Here's how the three questions added up for each file:

| CSV rows | Input tokens | Output tokens | Total tokens |
| ---: | ---: | ---: | ---: |
| 1,000 | 8,436 | 1,378 | 9,814 |
| 10,000 | 8,869 | 1,312 | 10,181 |
| 100,000 | 8,912 | 1,482 | 10,394 |

Total tokens means input plus output across all three requests.

| CSV rows | Correct answers | Average response time | Token cost, all 3 questions | Container estimate | Total estimate, all 3 questions |
| ---: | ---: | ---: | ---: | ---: | ---: |
| 1,000 | 3/3 | 4.27 seconds | $0.010800 | $0.030000 | $0.040800 |
| 10,000 | 3/3 | 4.21 seconds | $0.009013 | $0.030000 | $0.039013 |
| 100,000 | 3/3 | 4.15 seconds | $0.009811 | $0.030000 | $0.039811 |

Each cost covers all three questions for that CSV. Response times cover the question requests, excluding uploads, container creation, and cleanup.

## Was it actually reading the whole file?

The traces showed it reading the full CSVs and calculating the answers with code. I also checked the answers against independently calculated results. All nine answers were correct.

The files contained 100, 200, and 300 distinct products. Only analyzing the first thousand rows would've missed the extra product ids in the larger files.

The token counts and traces suggest Python processed the full file without passing every row into the model's context.

It doesn't prove that zero rows reached the model, or that every way of uploading a CSV works the same way. But it didn't support my assumption that the entire CSV had to go into context.

## What this means for the idea

The nine question test cost about $0.12, including estimated container costs.

The input token counts are what affected my decision. Increasing the file size from 1,000 to 100,000 rows didn't create the increase I had expected.

Response times stayed similar in this test, though larger or more complex workloads could take longer.

I want to note that this was one run per file with three simple questions, so more complex analysis could behave differently.

The experiment answered enough to change my next step.

My plan was to build a prototype if larger CSVs significantly increased analysis costs and created an opportunity for a more efficient architecture. This test doesn't support that.

I think there's still opportunity to build a tool that queries databases. Starting with CSVs makes the tool easier to try and build. It doesn't give someone a reason to use it.
