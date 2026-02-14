---
id: skill-professional-communication
type: skill
name: professional-communication
description: Guide technical communication for software developers. Covers email structure,
  team messaging etiquette, meeting agendas, and adapting messages for technical vs
  non-technical audiences. Use when drafting professional messages, preparing meeting
  communications, or improving written communication.
category: enterprise-communication
complexity: medium
keywords:
- api
- ci/cd
- database
- deployment
- go
- qa
- test
- testing
capabilities:
- file_reading
- file_search
- text_search
token_estimate: 1736
has_references: true
reference_count: 4
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~1,736 -->
<!-- Includes Reference Materials -->

> **How to Use This Template**
>
> This template can be used with various AI coding assistants:
> 
> **GitHub Copilot:**
> - Add to `.github/copilot-instructions.md` in your repository
> - Reference in chat: `@workspace` to include in context
> - Add specific sections to your workspace instructions
> 
> **Augment Code:**
> - Load context: `aug context add <path-to-this-file>`
> - Reference in queries naturally
> - Use with specific commands
> 
> **Claude (Desktop/Web):**
> - Add to Project Knowledge in Claude Projects
> - Reference in custom instructions
> - Copy relevant sections as needed
> 
> **ChatGPT:**
> - Add to Custom GPT configuration
> - Include in conversation instructions
> - Use as system prompt
> 
> **Generic Usage:**
> Copy the content below and paste it into your AI assistant's context window
> or system instructions.

---




# professional-communication

> Guide technical communication for software developers. Covers email structure, team messaging etiquette, meeting agendas, and adapting messages for technical vs non-technical audiences. Use when drafting professional messages, preparing meeting communications, or improving written communication.

# Professional Communication

## Overview

This skill provides frameworks and guidance for effective professional communication in software development contexts. Whether you're writing an email to stakeholders, crafting a team chat message, or preparing meeting agendas, these principles help you communicate clearly and build professional credibility.

**Core principle:** Effective communication isn't about proving how much you know - it's about ensuring your message is received and understood.

## When to Use This Skill

Use this skill when:

- Writing emails to teammates, managers, or stakeholders
- Crafting team chat messages or async communications
- Preparing meeting agendas or summaries
- Translating technical concepts for non-technical audiences
- Structuring status updates or reports
- Improving clarity of written communication

**Keywords**: email, chat, teams, slack, discord, message, writing, communication, meeting, agenda, status update, report

## Core Frameworks

### The What-Why-How Structure

Use this universal framework to organize any professional message:

| Component | Purpose | Example |
| --- | --- | --- |
| **What** | State the topic/request clearly | "We need to delay the release by one week" |
| **Why** | Explain the reasoning | "Critical bug found in payment processing" |
| **How** | Outline next steps/action items | "QA will retest by Thursday; I'll update stakeholders Friday" |

**Apply to**: Emails, status updates, meeting talking points, technical explanations

### Three Golden Rules for Written Communication

1. **Start with a clear subject/purpose** - Recipients should immediately grasp what your message is about
2. **Use bullets, headlines, and scannable formatting** - Nobody wants a wall of text
3. **Key messages first** - Busy people appreciate efficiency; state your main point upfront

### Audience Calibration

Before communicating, ask yourself:

1. **Who** are you writing to? (Technical peers, managers, stakeholders, customers)
2. **What level of detail** do they need? (High-level overview vs implementation details)
3. **What's the value** for them? (How does this affect their work/decisions?)

## Email Best Practices

### Subject Line Formula

| Instead of | Try |
| --- | --- |
| "Project updates" | "Project X: Status Update and Next Steps" |
| "Question" | "Quick question: API rate limiting approach" |
| "FYI" | "FYI: Deployment scheduled for Tuesday 3pm" |

### Email Structure Template

```markdown
**Subject:** [Project/Topic]: [Specific Purpose]

Hi [Name],

[1-2 sentences stating the key point or request upfront]

**Context/Background:**
- [Bullet point 1]
- [Bullet point 2]

**What I need from you:**
- [Specific action or decision needed]
- [Timeline if applicable]

[Optional: Brief next steps or follow-up plan]

Best,
[Your name]
```

### Common Email Types

| Type | Key Elements |
| --- | --- |
| **Status Update** | Progress summary, blockers, next steps, timeline |
| **Request** | Clear ask, context, deadline, why it matters |
| **Escalation** | Issue summary, impact, attempted solutions, needed decision |
| **FYI/Announcement** | What changed, who's affected, any required action |

**For templates**: See `references/email-templates.md`

## Team Messaging Etiquette

> **Note:** Examples use Slack terminology, but these principles apply equally to Microsoft Teams, Discord, or any team messaging platform.

### When to Use Chat vs Email

| Use Chat | Use Email |
| --- | --- |
| Quick questions with short answers | Detailed documentation needing records |
| Real-time coordination | Formal communications to stakeholders |
| Informal team discussions | Messages requiring careful review |
| Time-sensitive updates | Complex explanations with multiple parts |

### Team Messaging Best Practices

1. **Use threads** - Keep main channels scannable; follow-ups go in threads
2. **@mention thoughtfully** - Don't notify people unnecessarily
3. **Channel organization** - Right channel for right topic
4. **Be direct** - "Can you review my PR?" beats "Hey, are you busy?"
5. **Async-friendly** - Write messages that don't require immediate response

### The "No Hello" Principle

Instead of:

```text
You: Hi
You: Are you there?
You: Can I ask you something?
[waiting...]
```

Try:

```text
You: Hi Sarah - quick question about the deployment script.
     Getting a permission error on line 42. Have you seen this before?
     Here's the error: [paste error]
```

## Technical vs Non-Technical Communication

### When to Be Technical vs Accessible

| Audience | Approach |
| --- | --- |
| **Engineering peers** | Technical details, code examples, architecture specifics |
| **Technical managers** | Balance of detail and high-level impact |
| **Non-technical stakeholders** | Business impact, analogies, outcomes over implementation |
| **Customers** | Plain language, what it means for them, avoid jargon |

### Three Strategies for Simplification

1. **Start with the big picture before details** - People process "why" before "how"
2. **Simplify without losing accuracy** - Use analogies; replace jargon with plain language
3. **Know when to switch** - Read the room; adjust based on questions and engagement

### Jargon Translation Examples

| Technical | Plain Language |
| --- | --- |
| "Microservices architecture" | "Our system is split into smaller, independent pieces that can scale separately" |
| "Asynchronous message processing" | "Tasks are queued and processed in the background" |
| "CI/CD pipeline" | "Automated process that tests and deploys our code" |
| "Database migration" | "Updating how our data is organized and stored" |

**For more examples**: See `references/jargon-simplification.md`

## Writing Clarity Principles

### Active Voice Over Passive Voice

Active voice is clearer, more direct, and conveys authority:

| Passive (avoid) | Active (prefer) |
| --- | --- |
| "A bug was identified by the team" | "The team identified a bug" |
| "The feature will be implemented" | "We will implement the feature" |
| "Errors were found during testing" | "Testing revealed errors" |

### Eliminate Filler Words

| Instead of | Use |
| --- | --- |
| "At this point in time" | "Now" |
| "In the event that" | "If" |
| "Due to the fact that" | "Because" |
| "In order to" | "To" |
| "I just wanted to check if" | "Can you" |

### The "So What?" Test

After writing, ask: "So what? Why does this matter to the reader?"

If you can't answer clearly, restructure your message to lead with the value/impact.

## Meeting Communication

### Before: Agenda Best Practices

Every meeting invite should include:

1. **Clear objective** - What will be accomplished?
2. **Agenda items** - Topics to cover with time estimates
3. **Preparation required** - What should attendees bring/review?
4. **Expected outcome** - Decision needed? Information sharing? Brainstorm?

### During: Facilitation Tips

- **Time-box discussions** - "Let's spend 5 minutes on this, then move on"
- **Capture action items live** - Who does what by when
- **Parking lot** - Note off-topic items for later

### After: Summary Format

```markdown
**Meeting: [Topic] - [Date]**

**Attendees:** [Names]

**Key Decisions:**
- [Decision 1]
- [Decision 2]

**Action Items:**
- [ ] [Person]: [Task] - Due [Date]
- [ ] [Person]: [Task] - Due [Date]

**Next Steps:**
- [Follow-up meeting if needed]
- [Documents to share]
```

**For structures by meeting type**: See `references/meeting-structures.md`

## Quick Reference: Communication Checklist

Before sending any professional communication:

- [ ] **Clear purpose** - Can the recipient understand intent in 5 seconds?
- [ ] **Right audience** - Is this the appropriate person/channel?
- [ ] **Key message first** - Is the main point upfront?
- [ ] **Scannable** - Are there bullets, headers, short paragraphs?
- [ ] **Action clear** - Does the recipient know what (if anything) they need to do?
- [ ] **Jargon check** - Will the audience understand all terminology?
- [ ] **Tone appropriate** - Is it professional but not cold?
- [ ] **Proofread** - Any typos or unclear phrasing?

## Additional Tools

- `references/email-templates.md` - Ready-to-use email templates by type
- `references/meeting-structures.md` - Structures for standups, retros, reviews
- `references/jargon-simplification.md` - Technical-to-plain-language translations

## Companion Skills

- `feedback-mastery` - For difficult conversations and feedback delivery
- `/draft-email` - Generate emails using these frameworks

---

**Last Updated:** 2025-12-22

## Version History

- **v1.0.0** (2025-12-26): Initial release

---


---


## 📚 Reference Materials


### Meeting Structures

# Meeting Structures for Developers

Templates and structures for common software development meetings. Use these to run effective meetings and ensure productive outcomes.

## Daily Standup

**Duration:** 15 minutes max
**Frequency:** Daily
**Format:** Each person answers 3 questions

### Structure

Each team member shares (1-2 minutes max per person):

1. **Yesterday:** What did I complete?
2. **Today:** What am I working on?
3. **Blockers:** What's in my way?

### Best Practices

- **Stand up** (if in person) - Keeps it short
- **Focus on work, not activity** - "Completed feature X" beats "Attended meetings"
- **Parking lot** - Note follow-up discussions for after standup
- **Timebox strictly** - 15 minutes, then end
- **Blockers get attention** - If someone's blocked, identify who can help

### Anti-Patterns to Avoid

- Turning into status report to manager (it's team sync, not reporting)
- Problem-solving during standup (take it offline)
- Going into technical details (save for pairing sessions)
- Skipping when "nothing to report" (brief updates still valuable)

---

## Sprint Planning

**Duration:** 1-2 hours
**Frequency:** Start of each sprint
**Purpose:** Agree on sprint goals and work commitment

### Structure

| Phase | Duration | Activity |
| --- | --- | --- |
| Sprint Goal | 10 min | What will this sprint accomplish? |
| Backlog Review | 20 min | Review prioritized items |
| Estimation | 30 min | Size items being considered |
| Commitment | 20 min | Team commits to sprint scope |
| Capacity Check | 10 min | Account for PTO, meetings, etc. |

### Agenda Template

```markdown
1. **Sprint Goal** (10 min)
   - Product Owner presents sprint objective
   - Team asks clarifying questions

2. **Backlog Review** (20 min)
   - Review top items in priority order
   - Clarify acceptance criteria
   - Identify dependencies

3. **Estimation** (30 min)
   - Estimate items using team's method (points, t-shirts, etc.)
   - Break down large items if needed

4. **Sprint Commitment** (20 min)
   - Team selects items that fit capacity
   - Confirm everyone understands the work

5. **Wrap-up** (10 min)
   - Recap sprint goal and committed items
   - Note any risks or dependencies to watch
```

### Best Practices

- **Come prepared** - PO has prioritized backlog, items are refined
- **Focus on "what" not "how"** - Save implementation details for during sprint
- **Protect focus time** - Account for meetings, support, etc. when committing
- **Team decides capacity** - Only team members estimate and commit

---

## Sprint Retrospective

**Duration:** 1-1.5 hours
**Frequency:** End of each sprint
**Purpose:** Continuous improvement

### Structure

| Phase | Duration | Activity |
| --- | --- | --- |
| Set the Stage | 5 min | Check-in, set tone |
| Gather Data | 20 min | Collect feedback |
| Generate Insights | 20 min | Discuss patterns |
| Decide Actions | 15 min | Commit to improvements |
| Close | 5 min | Appreciate, wrap up |

### Common Formats

**Start/Stop/Continue:**

- **Start doing:** What should we begin?
- **Stop doing:** What should we stop?
- **Continue doing:** What's working well?

**Liked/Learned/Lacked/Longed For (4Ls):**

- **Liked:** What went well?
- **Learned:** What did we discover?
- **Lacked:** What was missing?
- **Longed for:** What do we wish we had?

**Mad/Sad/Glad:**

- **Mad:** What frustrated you?
- **Sad:** What disappointed you?
- **Glad:** What made you happy?

### Best Practices

- **Safe space** - No blame, focus on systems not people
- **Limit action items** - 1-3 concrete improvements
- **Follow up** - Review last retro's actions at start
- **Vary the format** - Keep it fresh to avoid ruts
- **Everyone participates** - Make space for quieter voices

---

## Architecture Review / Tech Design Review

**Duration:** 45-60 minutes
**Frequency:** As needed for significant technical decisions
**Purpose:** Get feedback on technical approach before implementation

### Structure

```markdown
1. **Context & Problem** (10 min)
   - What problem are we solving?
   - Why now? What's driving this?

2. **Proposed Solution** (15 min)
   - High-level architecture diagram
   - Key components and their responsibilities
   - Data flow

3. **Trade-offs & Alternatives** (10 min)
   - What alternatives did you consider?
   - Why this approach over others?
   - What are we trading off?

4. **Discussion & Questions** (15 min)
   - Open floor for questions
   - Concerns and risks
   - Edge cases and failure modes

5. **Decision & Next Steps** (5 min)
   - Approved / Approved with changes / Needs revision
   - Action items and timeline
```

### Best Practices

- **Share materials beforehand** - Send design doc 24-48 hours before
- **Timebox discussion** - Don't let it become a working session
- **Focus on architecture, not code** - Implementation details come later
- **Document decisions** - Record the outcome in an ADR or design doc

---

## One-on-One (1:1)

**Duration:** 30-60 minutes
**Frequency:** Weekly or bi-weekly
**Purpose:** Support, feedback, career growth

### Structure (Flexible)

```markdown
1. **Their Topics First** (15-20 min)
   - What's on their mind?
   - Blockers, concerns, wins

2. **Feedback Exchange** (10-15 min)
   - Recognition for recent work
   - Growth areas or coaching

3. **Career/Growth** (10-15 min)
   - Progress on development goals
   - Upcoming opportunities

4. **Admin/Updates** (5 min)
   - Any org updates to share
   - Upcoming schedule impacts
```

### Good Questions to Ask

**For team members:**

- "What's your biggest blocker right now?"
- "What's one thing I could do to better support you?"
- "What are you most proud of recently?"
- "What would you like to be doing more of?"

**For managers:**

- "Is there anything you need from me?"
- "How can I help the team succeed this sprint?"
- "What feedback do you have for me?"

### Best Practices

- **Their meeting, their agenda** - Let them drive topics
- **Don't cancel** - Consistency builds trust
- **Take notes** - Remember what you discussed
- **Follow through** - If you commit to something, do it

---

## Incident Postmortem / Blameless Review

**Duration:** 60-90 minutes
**Frequency:** After significant incidents
**Purpose:** Learn and prevent recurrence

### Structure

```markdown
1. **Timeline Review** (20 min)
   - What happened, when?
   - Build shared understanding of events

2. **Impact Assessment** (10 min)
   - What was affected?
   - Customer impact, business impact

3. **Root Cause Analysis** (20 min)
   - 5 Whys or Fishbone diagram
   - Contributing factors (not just trigger)

4. **What Went Well** (10 min)
   - What worked in our response?
   - What should we keep doing?

5. **Action Items** (15 min)
   - Prevention measures
   - Detection improvements
   - Response improvements

6. **Wrap-up** (5 min)
   - Assign owners and timelines
   - Schedule follow-up if needed
```

### Best Practices

- **Blameless** - Focus on systems, not individuals
- **Assume good intent** - People made reasonable decisions with available info
- **Prioritize actions** - Don't try to fix everything; focus on highest impact
- **Share learnings** - Document and share with broader team/org

---

## Code Review Walkthrough

**Duration:** 30-45 minutes
**Frequency:** As needed for complex changes
**Purpose:** Deep review of significant code changes

### Structure

```markdown
1. **Context Setting** (5 min)
   - What problem does this solve?
   - Why was this approach chosen?

2. **High-Level Walkthrough** (10 min)
   - Architecture/structure overview
   - Key files and their purposes

3. **Detailed Review** (20 min)
   - Walk through critical paths
   - Highlight non-obvious decisions
   - Answer questions in real-time

4. **Wrap-up** (5 min)
   - Note remaining concerns
   - Agree on approval path
```

### Best Practices

- **Share PR link beforehand** - Let reviewers skim first
- **Focus on logic, not style** - Linters catch style issues
- **Author drives the walkthrough** - Explain your thinking
- **Note follow-up items** - Track issues for separate PRs

---

## General Meeting Tips

### Before the Meeting

- [ ] Clear purpose/objective defined
- [ ] Agenda sent in advance
- [ ] Right people invited (and only right people)
- [ ] Materials shared for pre-read
- [ ] Time and duration appropriate for scope

### During the Meeting

- [ ] Start on time
- [ ] State objective at opening
- [ ] Timebox discussions
- [ ] Capture action items live
- [ ] Leave 5 minutes for wrap-up
- [ ] End on time (or early!)

### After the Meeting

- [ ] Send summary within 24 hours
- [ ] Action items have owners and due dates
- [ ] Follow up on commitments
- [ ] Cancel recurring meetings that aren't adding value

---

**Related:** Return to `professional-communication` skill for email and written communication guidance.




### Jargon Simplification

# Technical Jargon Simplification Guide

Translations from technical terminology to accessible language. Use when communicating with non-technical stakeholders, customers, or cross-functional teams.

## When to Simplify

**Simplify for:**

- Non-technical stakeholders (executives, product managers, sales)
- Customers and end users
- Cross-functional teams (HR, finance, legal, marketing)
- New team members still learning the domain
- Anyone who asks "what does that mean?"

**Stay technical for:**

- Engineering peers who share the context
- Technical documentation and specs
- Code reviews and architecture discussions
- When precision is more important than accessibility

## Architecture & Infrastructure

| Technical Term | Plain Language |
| --- | --- |
| Microservices architecture | Our system is split into smaller, independent pieces that can be updated separately |
| Monolithic application | One large application where everything is connected together |
| Load balancer | A traffic cop that distributes work evenly across our servers |
| CDN (Content Delivery Network) | Servers around the world that store copies of our content closer to users |
| Container / Docker | A standardized package that bundles our app with everything it needs to run |
| Kubernetes / K8s | A system that automatically manages and scales our applications |
| Serverless | We run code without managing servers; we pay only when it's actually used |
| API (Application Programming Interface) | A way for different software systems to talk to each other |
| Database migration | Reorganizing how our data is stored and structured |
| Caching | Storing frequently accessed data in a fast temporary location |
| Redundancy | Having backup systems so we can keep running if something fails |
| Failover | Automatically switching to a backup system when the main one fails |

## Development Process

| Technical Term | Plain Language |
| --- | --- |
| CI/CD Pipeline | Automated process that tests and deploys our code |
| Sprint | A 1-2 week work cycle where we build and deliver specific features |
| Backlog | Our prioritized list of features and improvements to build |
| Technical debt | Shortcuts we took that we need to fix later |
| Refactoring | Improving the code's structure without changing what it does |
| Code review | Team members checking each other's work before it goes live |
| Pull request / PR | A request to add your code changes to the main project |
| Deployment | Releasing new code to our live system |
| Rollback | Undoing a release and going back to the previous version |
| Feature flag | A switch that lets us turn features on/off without deploying |
| A/B testing | Showing different versions to different users to see what works better |

## Performance & Reliability

| Technical Term | Plain Language |
| --- | --- |
| Latency | The delay between a request and response; how fast things feel |
| Throughput | How much work the system can handle at once |
| Scalability | The ability to handle more users or work as we grow |
| Uptime / availability | How often the system is working and accessible |
| SLA (Service Level Agreement) | Our promise about how reliable the service will be |
| 99.9% uptime / "three nines" | About 8 hours of downtime per year |
| Memory leak | The app gradually using more memory until it slows down or crashes |
| Race condition | A bug where the outcome depends on timing, causing unpredictable behavior |
| Bottleneck | A part of the system that limits overall performance |

## Security

| Technical Term | Plain Language |
| --- | --- |
| Authentication | Verifying who you are (username/password) |
| Authorization | Checking what you're allowed to do |
| Encryption | Scrambling data so only authorized people can read it |
| SSL/TLS/HTTPS | Secure connection that protects data in transit |
| Two-factor authentication (2FA) | A second verification step beyond your password |
| Vulnerability | A weakness that could be exploited by attackers |
| Penetration testing / pen test | Hiring experts to try to break into our systems |
| Data breach | Unauthorized access to sensitive information |
| Firewall | A security barrier that controls what traffic can enter/exit |

## Data & Databases

| Technical Term | Plain Language |
| --- | --- |
| SQL / NoSQL | Different types of databases; SQL is structured tables, NoSQL is more flexible |
| Query | A request to get specific data from the database |
| Schema | The structure that defines how data is organized |
| Index | A lookup system that makes finding data faster |
| Replication | Copying data to multiple locations for safety and speed |
| Sharding | Splitting data across multiple databases to handle more volume |
| ETL (Extract, Transform, Load) | Moving data from one system to another, cleaning it up along the way |
| Data warehouse | A central repository that combines data from many sources |

## Communication & APIs

| Technical Term | Plain Language |
| --- | --- |
| REST API | A standard way for systems to communicate over the web |
| GraphQL | A more flexible way to request exactly the data you need |
| Webhook | Automatic notifications sent when something happens |
| Asynchronous / async | Tasks that run in the background without blocking other work |
| Message queue | A waiting line for tasks to be processed one at a time |
| Real-time | Updates that happen immediately, without refreshing |
| WebSocket | A persistent connection for instant two-way communication |
| Rate limiting | Restricting how many requests someone can make in a time period |

## Common Phrases Translated

| Technical Phrase | Plain Language |
| --- | --- |
| "We need to optimize the query" | "We need to make this faster" |
| "There's a bug in production" | "There's an error affecting live users" |
| "We're experiencing degraded performance" | "Things are slower than normal" |
| "The service is timing out" | "Requests are failing because they're taking too long" |
| "We need to scale horizontally" | "We need to add more servers to handle the load" |
| "There's a regression in the latest release" | "The new update broke something that used to work" |
| "We're doing a hotfix" | "We're quickly fixing a critical issue" |
| "That's a breaking change" | "Existing integrations will stop working unless updated" |
| "We need to deprecate this endpoint" | "We're phasing out this feature; please switch to the new one" |

## Tips for Effective Translation

### 1. Lead with Impact

Instead of: "We implemented Redis caching to reduce database load"
Say: "We made the page load 3x faster by storing frequently accessed data in memory"

### 2. Use Analogies

| Concept | Analogy |
| --- | --- |
| API | Like a waiter who takes your order to the kitchen and brings back food |
| Load balancer | Like a host at a restaurant directing guests to available tables |
| Database index | Like the index at the back of a book that helps you find topics quickly |
| Caching | Like keeping frequently used items on your desk instead of in a filing cabinet |
| Encryption | Like sending a letter in a locked box that only the recipient can open |

### 3. Focus on "So What?"

For every technical detail, ask: "Why does this matter to my audience?"

| Technical | Business Impact |
| --- | --- |
| "We upgraded to Node 20" | "We can now use modern features and security updates" |
| "We containerized the application" | "Deployments are now faster and more reliable" |
| "We added monitoring and alerting" | "We'll know about problems before customers report them" |

### 4. Know When Precision Matters

Some situations require technical precision:

- Legal or compliance discussions
- Security incident communications
- Technical documentation
- When the audience asks for details

In these cases, provide the technical term with a brief explanation:
"We're implementing TLS 1.3 encryption - this is the latest security standard for protecting data in transit."

---

**Related:** Return to `professional-communication` skill for writing and communication frameworks.




### Email Templates

# Email Templates for Developers

Ready-to-use email templates for common software development scenarios. Adapt the tone and detail level to your audience.

## Status Update Email

```markdown
Subject: [Project Name]: Week [N] Status Update

Hi [Team/Name],

**Summary:** [One sentence on overall progress and key highlight]

**Completed This Week:**
- [Feature/task completed with brief outcome]
- [Feature/task completed with brief outcome]

**In Progress:**
- [Current work item] - Expected completion: [Date]
- [Current work item] - Expected completion: [Date]

**Blockers/Risks:**
- [Blocker description] - Need: [What you need to unblock]

**Next Week:**
- [Planned work items]

Let me know if you have questions or need more detail on anything.

Best,
[Your name]
```

## Sprint Retrospective Summary

```markdown
Subject: Sprint [N] Retrospective Summary

Hi Team,

Thanks for a productive retrospective. Here's a summary of what we discussed:

**What Went Well:**
- [Positive item 1]
- [Positive item 2]

**What Could Improve:**
- [Improvement area 1]
- [Improvement area 2]

**Action Items:**
- [ ] [Person]: [Action] - Due: [Date]
- [ ] [Person]: [Action] - Due: [Date]

We'll revisit these in next sprint's retro. Thanks everyone!

Best,
[Your name]
```

## Request for Review/Feedback

```markdown
Subject: Review Request: [Document/PR/Design Name]

Hi [Name],

I'd appreciate your feedback on [document/PR/design] when you have a chance.

**What it is:** [Brief description]

**Link:** [URL]

**What I'm looking for:**
- [Specific feedback area 1]
- [Specific feedback area 2]

**Timeline:** [When you need feedback by, or "no rush"]

Happy to walk through it together if that would help. Thanks!

Best,
[Your name]
```

## Escalation Email

```markdown
Subject: [URGENT if applicable] Escalation: [Issue Summary]

Hi [Manager/Leadership],

I'm escalating [issue] because [reason it needs escalation].

**Issue Summary:**
[1-2 sentences describing the problem]

**Impact:**
- [Business/technical impact]
- [Timeline/deadline affected]
- [Users/customers affected]

**What We've Tried:**
- [Attempted solution 1]
- [Attempted solution 2]

**Decision/Help Needed:**
[Specific decision or support you need]

**Recommended Next Steps:**
- [Your recommended approach]

I'm available to discuss further. Please let me know how you'd like to proceed.

Best,
[Your name]
```

## FYI/Announcement Email

```markdown
Subject: FYI: [What Changed/Happening]

Hi [Team/Name],

Quick heads up: [One sentence summary of what changed or is happening]

**Details:**
- [Key detail 1]
- [Key detail 2]

**Impact on You:**
- [How this affects the recipient, if at all]

**Action Needed:** [None / Specific action]

Let me know if you have questions.

Best,
[Your name]
```

## Meeting Request Email

```markdown
Subject: Meeting Request: [Topic] - [Proposed Time Range]

Hi [Name],

I'd like to schedule a meeting to discuss [topic].

**Purpose:** [What we need to accomplish]

**Duration:** [Time estimate]

**Proposed Times:**
- [Option 1]
- [Option 2]

**Preparation:** [None needed / What to review beforehand]

Let me know what works for you, or feel free to grab time on my calendar.

Best,
[Your name]
```

## Technical Decision Request

```markdown
Subject: Decision Needed: [Technical Choice]

Hi [Decision Maker],

We need a decision on [technical choice] for [project/feature].

**Context:**
[Brief background on why this decision is needed]

**Options:**

| Option | Pros | Cons | Effort |
| --- | --- | --- | --- |
| Option A: [Name] | [Pro] | [Con] | [Est] |
| Option B: [Name] | [Pro] | [Con] | [Est] |

**My Recommendation:** Option [X] because [brief reasoning]

**Timeline:** Need decision by [date] to stay on track for [milestone]

Happy to discuss further if helpful.

Best,
[Your name]
```

## Introducing Yourself to a New Team

```markdown
Subject: Introduction: [Your Name] - [Your Role]

Hi Team,

I'm [Name], joining as [Role] starting [Date]. Excited to be part of the team!

**Background:**
- [Brief relevant experience]
- [What you'll be working on initially]

**What I'm Looking Forward To:**
- [Something genuine about the team/project]

**How to Reach Me:**
- Slack: [handle]
- Preferred: [communication preference]

Looking forward to meeting everyone. Happy to grab virtual coffee with anyone who wants to chat!

Best,
[Your name]
```

## Asking for Help

```markdown
Subject: Question: [Specific Topic]

Hi [Name],

I'm working on [task/feature] and could use your expertise on [specific area].

**What I'm Trying to Do:**
[Brief description]

**What I've Tried:**
- [Approach 1 and result]
- [Approach 2 and result]

**Where I'm Stuck:**
[Specific question or blocker]

**Relevant Context:**
- [Link to code/doc if helpful]
- [Error message if applicable]

No rush if you're busy - let me know when you have a few minutes. Thanks!

Best,
[Your name]
```

## Following Up

```markdown
Subject: Re: [Original Subject] - Following Up

Hi [Name],

Following up on my earlier message about [topic].

**Quick Recap:** [One sentence reminder of what you need]

**Timeline:** [Any urgency or deadline]

Let me know if you need anything from me or if this is still on your radar.

Thanks,
[Your name]
```

## Tips for Using Templates

1. **Personalize** - Don't send templates verbatim; adapt to context and relationship
2. **Delete irrelevant sections** - If you don't have blockers, don't include a blockers section
3. **Match formality to relationship** - Adjust tone based on how well you know the recipient
4. **Proofread** - Especially names, dates, and any copied content
5. **Consider timing** - Avoid sending at midnight or over weekends unless urgent

---

**Related:** Return to `professional-communication` skill for guidance on writing principles.




### Remote Async Communication

# Remote and Async Communication

Guide to effective communication in remote-first and async-first environments.

## Async-First Principles

### Default to Async

Before scheduling a meeting, ask: "Could this be async instead?"

| Sync (Meeting) | Async (Document/Message) |
| -------------- | ------------------------ |
| Brainstorming with immediate building | Collecting feedback over time |
| Sensitive/emotional conversations | Status updates |
| Real-time collaboration | Decisions with clear options |
| Urgent problem-solving | Non-urgent questions |
| Relationship building | Documentation and knowledge sharing |

### Why Async Matters

- **Time zones:** Not everyone is online at once
- **Deep work:** Meetings fragment focus time
- **Inclusion:** Async gives introverts equal voice
- **Documentation:** Async creates artifacts for later
- **Flexibility:** People work when they're most productive

## Channel Selection Guide

Choose the right tool for the job:

### Instant Messaging (Slack, Teams)

**Best for:**

- Quick questions expecting same-day answers
- Informal team chat
- Time-sensitive coordination
- Celebrations and recognition

**Anti-patterns:**

- Long-form discussions (use docs)
- Decisions needing documentation (use PRs or docs)
- Anything requiring more than 3 back-and-forths

### Documents (Notion, Google Docs)

**Best for:**

- Proposals and RFCs
- Meeting notes and follow-ups
- Complex decisions with multiple stakeholders
- Knowledge that should persist
- Async reviews and feedback

**Format tips:**

- Start with TL;DR
- Use headers for scannability
- Call out action items and decisions clearly
- Include deadline if feedback is needed

### Email

**Best for:**

- External communication
- Formal announcements
- Reaching people outside your company
- When you need a paper trail

**Avoid for:**

- Internal quick questions (use Slack)
- Collaborative editing (use docs)

### Video Calls

**Reserve for:**

- Complex discussions needing real-time back-and-forth
- Sensitive conversations (feedback, conflict)
- Relationship building
- Urgent problem-solving
- Brainstorming and creative work

**Always:**

- Send agenda beforehand
- Record for those who can't attend
- Document decisions and action items after

### PRs and Code Comments

**Best for:**

- Code-specific discussions
- Technical decisions with code context
- Review feedback
- Architectural choices in implementation

## Over-Communication

In remote work, default to over-communication:

### Share Context Liberally

People can't see what you're working on:

| Don't | Do |
| ----- | -- |
| Disappear for hours | Post "heads down on X, will be slow to respond" |
| Assume people know your schedule | Share working hours in your profile |
| Go silent when stuck | Post "blocked on X, exploring options" |
| Complete work silently | Share progress: "Finished the API, moving to tests" |

### Working Out Loud

Share your thinking, not just your conclusions:

**Good examples:**

> "Exploring two approaches for the caching problem: Redis vs. in-memory. Leaning toward Redis for cross-instance consistency. Will share trade-offs in #architecture by EOD."

And:

> "Hit an unexpected issue with the auth flow - user sessions aren't persisting across subdomains. Investigating cookie settings. ETA unclear, will update in 2 hours."

### When Changing Plans

If priorities shift:

- Communicate the change and reason
- Update anyone who was expecting the old thing
- Don't assume people will notice

## Time Zone Awareness

### Know Your Team's Time Zones

Keep a quick reference:

- Who overlaps with you (synchronous possible)
- Who doesn't (async required)
- When each person's day starts/ends

### Async by Default

When in doubt, don't expect immediate response:

- Include context so they can reply without follow-up questions
- Set clear but reasonable deadlines
- Acknowledge receipt even if you can't respond fully yet

### Schedule Sends

Don't send messages at 2am someone else's time:

- Use "schedule send" features
- Or add context: "No response expected until your morning"

### Rotating Meeting Times

For regular cross-timezone meetings:

- Rotate who bears the inconvenient time
- Record meetings for those who can't attend live
- Default to async when sync isn't essential

## Documentation Over Meetings

### Meeting Decision Rule

Before scheduling a meeting:

1. **Write first:** Draft the problem, context, and options
2. **Collect async feedback:** Give 24-48 hours
3. **Then decide:** If still unresolved, schedule a meeting

Many meetings can be avoided if the organizer documents well enough that discussion isn't needed.

### Meeting Follow-Up

Every synchronous meeting should produce async artifacts:

- **Notes:** Key discussion points
- **Decisions:** What was decided and why
- **Action items:** Who does what by when
- **Recording:** For those who couldn't attend

Share within 24 hours while context is fresh.

### Recording and Summarizing

For important sync meetings:

- Record video/audio
- Create written summary with timestamps
- Call out decisions and action items
- Share in relevant channels

## Writing for Async

### Frontload the Point

Start with the conclusion/ask, then provide context:

**Buried lead (bad):**
> "I've been looking at our deployment pipeline and noticed that the test suite takes 45 minutes. After investigating, I found that most of the time is spent on integration tests that could be parallelized. I've put together a proposal to reduce this by 60%..."

**Frontloaded (good):**

> "**Proposal:** Reduce our test suite time from 45 min to 18 min by parallelizing integration tests.
>
> **Context:** Our deployment pipeline is slow because..."

### Make It Scannable

Use structure for long messages:

- **Headers** for sections
- **Bold** for key points
- **Bullets** for lists
- **TL;DR** at the top for long documents

### Include Everything Needed

The reader shouldn't need to ask follow-up questions:

| Missing context | Complete context |
| --------------- | ---------------- |
| "The deploy failed" | "The 3pm deploy to staging failed with timeout error (link to logs). I'm investigating and will update in 1 hour." |
| "Can you review my PR?" | "Can you review my PR (#123)? It's ~200 lines touching the auth flow. Happy to answer questions async or sync." |

### Set Clear Expectations

Make response expectations explicit:

- **Deadline:** "Feedback needed by Friday EOD"
- **Priority:** "Not urgent, whenever you have time"
- **Type of response:** "Looking for approval" vs. "Want to brainstorm"
- **Who should respond:** "@team" vs. specific people

## Anti-Patterns

### The Slack Novel

Long-form content in chat messages:

- Hard to skim
- Gets lost in scroll
- Can't be edited or organized

**Fix:** Put it in a document, share the link.

### The Ping-Pong Conversation

Back-and-forth that takes 3 hours in chat but would take 10 minutes in a call:

**Fix:** After 3 exchanges, offer "Want to hop on a quick call?"

### The Surprise Meeting

Adding meetings without context or asking availability:

**Fix:** Always include agenda; ask before scheduling.

### The Ghost

Disappearing without communication:

**Fix:** Post status when stepping away; respond to acknowledge messages even if you can't fully address them.

### The Always-On Expectation

Expecting immediate responses:

**Fix:** Set team norms about response times; use async by default.

## Team Norms

Consider establishing explicit norms:

```markdown
## Our Communication Norms

### Response Times
- **Slack DM:** Within 4 working hours
- **Slack channel:** Within 24 hours
- **Email:** Within 48 hours
- **Document comments:** Before stated deadline

### Urgent vs. Non-Urgent
- For truly urgent: Use @here or call
- For everything else: Assume async

### Deep Work Protection
- No expectation of response during focus time (blocked on calendar)
- "Do Not Disturb" is respected

### Time Zone Respect
- Core overlap hours: 10am-2pm EST
- Meetings scheduled in overlap hours only
- Async preferred outside overlap
```




---

## 🚀 Usage

**Reference this template:** `@skill-professional-communication.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
