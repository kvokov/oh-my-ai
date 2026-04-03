# E-Commerce Recommendation Engine — Full Discovery Interview

## Background

A growth-stage e-commerce company wants to add a product recommendation engine to their platform. The engineering manager sent this brief ahead of the session:

> "We want to add a recommendation engine that shows customers 'You might also like' suggestions on product pages and in post-purchase emails. We've heard GraphQL is great for this kind of thing and we're thinking of using it for the API. Our data team said something about collaborative filtering being the right approach. We already have a user database and a product catalog in PostgreSQL. We have about 50,000 active users and expect to grow. Security is handled — we have auth already. Deployment will be on our existing Kubernetes cluster."

The brief is promising but incomplete in ways that matter. The team has picked technologies based on what they've heard rather than on requirements fit: GraphQL may or may not be the right approach for a recommendations API, and collaborative filtering has significant cold-start and scale implications that haven't been considered. The security claim is vague and the scale expectations are underspecified.

Additionally, this project sits at the intersection of several concerns that may conflict: recommendation freshness (more personalized = higher compute cost), response time on product pages (recommendations slow page load), and privacy constraints (personalization requires tracking purchase history).

## Your Task

Conduct a complete discovery interview with the engineering manager to gather all the information needed for a thorough specification. Since this is a simulated session, you will both ask the questions and answer them yourself based on the brief — making and documenting reasonable assumptions for any gaps.

Produce these output files:

1. **`interview_log.md`** — Full record of the discovery interview: each question, the category it addresses, and the answer or assumption.

2. **`research_notes.md`** — Notes from any research you conduct into the technical approaches, comparing options and their tradeoffs for this specific use case.

3. The final specification document saved to an appropriate location.

Make sure all major areas of concern are addressed before writing the spec. If you realize partway through that an important area hasn't been covered, go back and ask about it before finalizing.
