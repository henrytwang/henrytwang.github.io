---
layout: post
title: Managing development time for your A/B test
---

You have a feature idea that you’d like to test. When are you going to get it running?

# Today

Build the test in the testing platform. Get one engineer to drop everything and crank it out. Start that baby up, pronto.

Pros:

- The fastest way to determine a feature’s success or failure. Period. If the metrics are positive, you can leave the test running and keep the wins, while another team builds the production-ready version. If it fails, you used the least amount of time and resources.

Cons:

- Customer service alerts the team that several users are reporting a broken experience. You look into it and realize you forgot to test for an edge case that affected many more users than you anticipated. It’s been two weeks since you started the test. Now you have to purge your data and re-run the test. Hopefully this doesn’t happen again.

- For a complex feature, the engineer takes a week to tackle a seemingly impossible task with the limited abilities of the testing platform, and comes to the conclusion that it’s not possible to build. You could have taken the *Week from now* route (see below) and had it ready by then.

- This feature will have to be rebuilt for production by another team.

# Week from now

The growth team builds the feature in their own service, even if the feature is meant to live in another service. Still a hack, but there are processes that make you feel safe and fuzzy: git commits, functional tests, code reviews, QA, and the release.

Pros:

- More process and eyeballs on the work means less risk of failure.

Cons:

- The *Today* test might have already reached significance by now (if it ran correctly).

- If the test succeeds, a relevant team will have to build the feature and the growth team will have to disassemble their own version.

- This feature has to wait for a release.

# Month from now

Have the relevant team build the feature (e.g. the mobile team builds the mobile-only feature).

Pros:

- The feature is production-ready on the first try.

Cons:

- This outside team has its own set of product requests and demands. The A/B test could be de-prioritized multiple times unless it’s a major bullet point on the company to-do list. You lose control of the test launch date.