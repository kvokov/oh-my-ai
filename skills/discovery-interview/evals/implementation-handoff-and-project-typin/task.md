# Internal CLI Tool for Log Analysis — Discovery to Spec and Handoff

## Background

A platform engineering team at a mid-size SaaS company has asked for your help defining a new internal tool. Their DevOps lead sent the following brief:

> "We need a CLI tool that our on-call engineers can run to analyze production logs during incidents. The tool should be able to filter logs by time window, service name, and severity level, and then output either a summary table or raw lines. Engineers are comfortable with the command line — they'll run this from their local machines or from a bastion host. We generate a lot of logs (maybe a few hundred MB per service per day across about 30 services) so it needs to be fast. Right now engineers manually grep through log files over SSH and it's chaotic during incidents. We haven't decided on a language yet. The logs are currently stored as JSON files on S3."

This is a greenfield internal tool with a well-defined set of users (on-call engineers). The team lead is technical, understands the domain, but hasn't thought through all the edge cases, the exact UX of the CLI, security of S3 access, or what happens when the tool encounters malformed log entries.

## Your Task

Conduct a discovery interview with the DevOps lead to gather everything needed for a complete specification. Since this is a simulated session, you will both ask the questions and answer them based on the brief — making and documenting reasonable assumptions where details aren't covered.

Produce these output files:

1. **`interview_log.md`** — Full record of the interview with questions, categories, and answers or assumptions.

2. The final specification document saved to an appropriate location.

3. **`handoff_notes.md`** — A brief document capturing what happens after the spec is delivered. Based on the conversation, note what the team's immediate priority is once the spec is in hand.
