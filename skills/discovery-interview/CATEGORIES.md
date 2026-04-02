# Interview Category Deep Dive

Questions and gap signals for each category. Select categories relevant to the project type.

## Category A: Problem & Goals
**Questions:** What's the current pain point? How do people solve it today? What does success look like — how will you measure it? Who are the stakeholders beyond end users? What happens if this doesn't get built?

**Gap signal:** User describes a solution instead of a problem, or can't articulate the pain clearly.

## Category B: User Experience & Journey
**Questions:** Walk through: a user opens this for the first time — what do they see, what do they do? What's the core action (the one thing users MUST do)? What errors can happen and what should users see? How technical are your users?

**Gap signal:** User describes features instead of journeys, or hasn't thought through the actual flow.

## Category C: Data & State
**Questions:** What information needs to be stored, temporarily or permanently? Where does data come from and where does it go? Who owns the data — are there privacy/compliance concerns? What happens to existing data if requirements change?

**Gap signal:** User says "just a database" without understanding schema implications.

## Category D: Technical Landscape
**Questions:** What existing systems does this need to work with? Are there technology constraints (language, framework, platform)? What's the deployment environment? What's the team's technical expertise?

**Gap signal:** User picks technologies without understanding tradeoffs.

**Research triggers:** "I've heard X is good" → research X vs alternatives. "We use Y but I'm not sure if..." → research Y capabilities. Technology mismatch detected → research correct approaches.

## Category E: Scale & Performance
**Questions:** How many users/requests do you expect now vs. future? What response times are acceptable? What happens during traffic spikes? Is this read-heavy, write-heavy, or balanced?

**Gap signal:** User says "millions of users" without understanding infrastructure implications.

## Category F: Integrations & Dependencies
**Questions:** What external services does this need to talk to? What APIs need to be consumed or created? What's the fallback if third-party dependencies fail? What auth is needed for integrations?

**Gap signal:** User assumes integrations are simple without understanding rate limits, auth, failure modes.

## Category G: Security & Access Control
**Questions:** Who should be able to do what? What data is sensitive (PII, financial, health)? Are there compliance requirements (GDPR, HIPAA, SOC2)? How do users authenticate?

**Gap signal:** User says "just basic login" without understanding security implications.

## Category H: Deployment & Operations
**Questions:** How will this be deployed and by whom? What monitoring/alerting is needed? How do you handle updates and rollbacks? What's the disaster recovery plan?

**Gap signal:** User hasn't thought about ops, or assumes "it just runs."
