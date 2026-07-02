# AUT Lessons

## [tags: isolation, HOME, infrastructure] HOME=/tmp lobotomizes the agent, not just the skill (2026-07-03)

Setting HOME=/tmp removes ALL skills, extensions, and MCP tools — not just the one under test. The WITHOUT condition becomes "agent with zero capabilities" vs WITH "fully configured agent." This isn't a controlled comparison.

Fix: create a stripped HOME directory with agent auth config (so CLI runs) but no skills directory and no extension hooks. Verify by asking "what skills do you have?" in each condition.

## [tags: scoring, ground-truth] Stale GTs cause systematic false negatives (2026-07-03)

Ground truth "256" for Google Scholar citations that grew to 257,076. Agent correctly answered 257,076 → scored as FAIL. Flight price GT "$322" was correct same-day but would drift within days.

Fix: verify GTs via the tool itself immediately before running trials. For volatile data (flights, prices), accept ±5% or re-verify within the run window.

## [tags: parallelism, rate-limits] 8 parallel copilot calls → 67% ERROR rate (2026-07-03)

8 concurrent `copilot -p` processes hit API rate limits. 81/120 returned non-zero exit (captured as "ERROR"). Reduced to MAX_PARALLEL=2 with 8s stagger delay → 0% errors.

## [tags: sample-size, noise] N=3 produced +53pp lift that collapsed to +16pp at N=12 (2026-06-28)

Sonnet 4.6 hard questions. N=3: WITH 87%, WITHOUT 27%. N=12: WITH 76%, WITHOUT 60%. The WITHOUT floor was artificially low in the small sample — Sonnet can scrape/fetch answers without the tool, just not every time.

## [tags: difficulty, satisficing] Easy questions show 0% lift regardless of skill quality (2026-06-28)

126 runs on easy questions (phone numbers, stock tickers). Both conditions: 100% correct. Tool discovery rate: 3.7%. Agents satisfice with web_search when it works. Only hard questions (where default tools fail) measure skill value.

## [tags: contamination, training-data] Sonnet names SerpApi engines in 32% of WITHOUT runs (2026-06-28)

With skill completely removed, Sonnet 4.6 says "I need google_flights but SERPAPI_KEY isn't set." It knows from training data (our docs are public). GPT-5.4 and Haiku 4.5: zero awareness without skill. Training-data contamination is model-specific and can't be fixed by better isolation.

## [tags: auth, error-messages, haiku] Missing auth guidance caused -20pp for Haiku (2026-06-29)

Original SKILL.md had no auth-check instructions. Haiku found the CLI, tried to call it, hit 401, burned retries, gave up. Tool actively hurt performance. Added auth-check block → zero auth failures in retest.

## [tags: protocol, pilot] Always pilot 1 WITH + 1 WITHOUT before full matrix (2026-07-03)

First full run (120 trials, 8 parallel, HOME=/tmp) produced garbage: 81 ERRORs, 0% discovery in WITH. Two pilots later: confirmed HOME isolation bug, rate limit threshold, and scoring format issues. Pilot costs 2 runs. Full matrix costs 240. Find infrastructure bugs at N=2, not N=240.

## [tags: behavioral-vs-score] "Used the tool" ≠ "correct answer" (2026-07-03)

Haiku retest: used serpapi CLI successfully in 3/3 manual trials (behavioral pass). But got wrong numbers because flight prices changed (score fail). These are independent metrics. Report both: discovery/usage rate (behavioral) AND correctness (score). Don't conflate.
