# Crypto sentiment collector api access overview

This repository documents the planned Reddit API usage for a private read-only sentiment collection system.

The production implementation is kept private because it contains deployment configuration, infrastructure details, internal application code, and environment-specific settings. This public repository exists to describe the intended Reddit API access pattern, safety constraints, data handling approach, and rate-limit behavior.

## Purpose

The application is a personal, non-commercial research tool for collecting public crypto-related discussion from Reddit.

The goal is to analyze broad public discussion trends around cryptocurrency markets, assets, ecosystem narratives, regulation, security incidents, project announcements, and community sentiment.

The app is read-only. It does not interact with Reddit users or communities.

## What the app does

The application periodically collects public posts and, optionally, a limited number of public comments from selected crypto-related subreddits.

The collected Reddit data is normalized and stored privately together with data from other public crypto-relevant sources, such as news feeds, project blogs, and market sentiment indexes.

The system is designed as an external backend data pipeline. It runs outside Reddit infrastructure and uses scheduled jobs to collect and normalize data.

## What the app does not do

The application does not:

* Post submissions.
* Post comments.
* Vote on posts or comments.
* Send private messages.
* Moderate communities.
* Modify Reddit content.
* Collect private user data.
* Attempt to identify individual users.
* Scrape Reddit outside approved API access.
* Circumvent Reddit rate limits.
* Train artificial intelligence models on Reddit data.

## Reddit api usage

The application is intended to use Reddit’s official API through OAuth credentials.

The app will use a descriptive user agent similar to:

```text
python:crypto_sentiment_collector:v0.1.0 (by /u/<reddit_username>)
```

The collector will respect Reddit’s published API rate limits and monitor rate-limit response headers when available.

The expected access pattern is modest. The app runs on a schedule and requests recent public submissions from selected subreddits. It may also request a limited number of public comments from selected posts.

## Intended subreddits

The initial subreddit list is expected to include:

```text
r/CryptoCurrency
r/CryptoMarkets
r/BitcoinMarkets
r/altcoin
r/ethereum
r/ethfinance
r/solana
r/defi
r/Arbitrum
r/Sui
r/Chainlink
r/dydxprotocol
r/dogwifhat
r/memecoins
r/VirtualsProtocol
```

This list may be adjusted over time based on relevance, activity level, data quality, and Reddit API access approval.

## Data collected

For public submissions, the app may collect fields such as:

```text
subreddit
submission id
title
selftext
permalink
url
created timestamp
score
upvote ratio
number of comments
author name, if publicly available
listing mode
```

For public comments, the app may collect fields such as:

```text
subreddit
comment id
parent submission id
comment body
permalink
created timestamp
score
author name, if publicly available
```

The application does not need private messages, private subreddit data, voting capabilities, moderation capabilities, or account-level permissions beyond read-only access to public content.

## Data storage and privacy

Collected data is stored in a private database controlled by the application owner.

The system is designed to analyze aggregate discussion patterns and sentiment, not individual users.

Usernames may be stored only when returned as part of public Reddit API responses. The application does not attempt to deanonymize users, enrich user profiles, or combine Reddit identities with external personal data.

The app prioritizes aggregate analysis across topics, assets, subreddits, and time periods.

## Rate limiting and responsible access

The collector is designed to be conservative with API usage.

Planned safeguards include:

* Scheduled collection intervals instead of continuous polling.
* Per-subreddit request limits.
* Per-run maximum item limits.
* Deduplication of previously collected content.
* Retry handling with exponential backoff.
* Respect for API error responses.
* Monitoring of rate-limit headers.
* No scraping outside the official API.

The application is intended to operate well within reasonable read-only API usage limits.

## Why this is not built with Devvit

This project is not a Reddit-native interactive app, subreddit extension, or community bot.

It is an external cloud-based data collection pipeline that needs to:

* Run on external cloud infrastructure.
* Collect data from multiple non-Reddit sources.
* Normalize Reddit data together with news feeds, project blogs, and market sentiment indexes.
* Store data in a private database.
* Archive raw payloads for internal data lineage.
* Run scheduled backend jobs.

Because the project depends on external infrastructure, private storage, scheduled jobs, and cross-source normalization, it does not fit well as a Devvit application.

## Deployment overview

The private production system is expected to run on Google Cloud Run.

A scheduled job periodically triggers the collector, which fetches data from configured sources, normalizes records, deduplicates content, and stores the result in private cloud storage and database tables.

The Reddit part of the system is only one source adapter among several.
