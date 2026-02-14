---
id: skill-feedback-mastery
type: skill
name: feedback-mastery
description: Navigate difficult conversations and deliver constructive feedback using
  structured frameworks. Covers the Preparation-Delivery-Follow-up model and Situation-Behavior-Impact
  (SBI) feedback technique. Use when preparing for difficult conversations, giving
  feedback, or managing conflicts.
category: enterprise-communication
complexity: medium
keywords:
- api
- deployment
- performance
- qa
- security
- test
- testing
capabilities:
- file_reading
- file_search
- text_search
token_estimate: 2276
has_references: true
reference_count: 3
---

<!-- Converted from Claude Skill Template -->
<!-- Complexity: Medium -->
<!-- Estimated Tokens: ~2,276 -->
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




# feedback-mastery

> Navigate difficult conversations and deliver constructive feedback using structured frameworks. Covers the Preparation-Delivery-Follow-up model and Situation-Behavior-Impact (SBI) feedback technique. Use when preparing for difficult conversations, giving feedback, or managing conflicts.

# Feedback Conversations

## Overview

This skill provides frameworks for navigating difficult workplace conversations and delivering effective feedback. Whether you're addressing performance issues, resolving conflicts, or giving constructive feedback, these structured approaches lead to better outcomes.

**Core insight:** Research shows that employees who approach difficult conversations with preparation and a clear framework are **60% more likely to reach a positive resolution** than those who engage without a plan.

## When to Use This Skill

Use this skill when:

- Preparing to give feedback to a colleague or direct report
- Addressing performance issues or missed expectations
- Navigating conflict between team members
- Having 1:1 conversations about sensitive topics
- Receiving feedback and wanting to respond constructively
- Managing expectations with stakeholders

**Keywords**: feedback, difficult conversation, 1:1, one-on-one, performance, conflict, expectations, behavior, confrontation

## Core Frameworks

### The Preparation-Delivery-Follow-up Model

A three-part structure for difficult conversations:

| Phase | Focus | Key Questions |
| --- | --- | --- |
| **Preparation** | Understand the issue, define goals, manage emotions | What's the problem? What outcome do I want? Am I calm? |
| **Delivery** | Open neutrally, use facts not blame, encourage dialogue | How do I start? What evidence do I have? How do I involve them? |
| **Follow-up** | Document actions, set check-ins, provide support | What did we agree to? When will we check in? How do I support? |

### The SBI Feedback Model

**Situation-Behavior-Impact (SBI)** structures feedback to be specific, objective, and actionable:

| Component | Description | Example |
| --- | --- | --- |
| **Situation** | Describe the specific context | "During yesterday's code review..." |
| **Behavior** | State the observable action (not interpretation) | "...you interrupted Sarah three times while she was explaining her approach..." |
| **Impact** | Explain the effect on team/project/person | "...which made her hesitate to share ideas and slowed down our discussion." |

**Why it works:** SBI removes assumptions and focuses on observable facts, reducing defensiveness.

## Preparation Phase

### Step 1: Understand the Issue

Ask yourself:

- **What exactly is the problem?** (Be specific, not vague)
- **How does it impact the team, project, or company?**
- **Have I gathered all relevant facts?**
- **Is this a pattern or a one-time event?**

### Step 2: Define Your Goals

Before the conversation, clarify what you're seeking:

| Goal Type | Example |
| --- | --- |
| Behavior change | "I want them to submit code reviews on time" |
| Mutual understanding | "I want to understand what's blocking them" |
| Expectation setting | "I want to clarify what 'done' means for features" |
| Problem solving | "I want to find a solution together" |

**Tip:** Use if-then statements to clarify stakes:
> "If this behavior continues, then the project timeline will suffer, leading to missed deliverables."

### Step 3: Manage Your Emotions

High emotional intensity reduces cognitive processing by 30%. Before the conversation:

- [ ] Am I calm and in control?
- [ ] Have I separated facts from personal frustrations?
- [ ] Have I considered their perspective?
- [ ] Can I present this without accusation?

**Reframing technique:**

| Accusatory | Constructive |
| --- | --- |
| "You always miss deadlines and it slows everyone down" | "I've noticed some recent delays and want to understand any challenges you're facing" |
| "You never test your code properly" | "I've seen a few bugs slip through recently. Let's talk about our testing process" |

## Delivery Phase

### The Three-Step Delivery Formula

1. **Open with neutrality and intent**
2. **Present the issue using facts, not blame**
3. **Encourage dialogue and solutions**

### Opening Lines That Work

| Context | Opening |
| --- | --- |
| General | "I want to talk about something important to our team's success, and I'd love to hear your perspective." |
| Performance | "I've noticed some patterns I'd like to discuss. My goal is to support you, not criticize." |
| Conflict | "I sense there might be some tension, and I'd like to understand what's happening from your side." |
| Expectations | "I want to make sure we're aligned on expectations. Can we talk through how this project is going?" |

### Facts, Not Blame

| Blaming | Factual |
| --- | --- |
| "You're not committed to this project" | "I've noticed your updates have been brief in our last three meetings. Is something affecting your workload?" |
| "You don't care about code quality" | "This PR had 12 bugs caught in QA. Let's talk about what happened and how we can improve" |
| "You're always late" | "The standup started at 9:00 and you joined at 9:15 the last three days. What's going on?" |

**Key principles:**

- Use specific examples, not generalizations ("always," "never")
- Stick to observable behaviors, not assumptions about motives
- Focus on impact, not character

### Encouraging Dialogue

After stating your observation, shift to collaboration:

| Situation | Dialogue Prompt |
| --- | --- |
| Understanding barriers | "What's been challenging about this?" |
| Seeking their view | "How do you see the situation?" |
| Finding solutions | "What would help you succeed here?" |
| Checking alignment | "Does this match your understanding of what happened?" |

## Follow-up Phase

Even successful conversations need follow-through to create lasting change.

### Follow-up Checklist

- [ ] **Document agreed-upon action items** - What specifically will change?
- [ ] **Set check-in dates** - When will you revisit this?
- [ ] **Provide ongoing support** - How will you help them succeed?
- [ ] **Celebrate progress** - Recognize improvements when they happen

### Sample Follow-up Message

```markdown
Hi [Name],

Thanks for the conversation yesterday. I appreciated your openness.

**What we agreed to:**
- [Action item 1] - [Timeline]
- [Action item 2] - [Timeline]

**Check-in:** Let's reconnect [date] to see how things are going.

I'm here if you need any support. Thanks for working through this with me.

Best,
[Your name]
```

## SBI Examples for Software Teams

### Positive Feedback

**Code Review:**
> **Situation:** "During Tuesday's code review for the authentication module..."
> **Behavior:** "...you provided detailed comments on potential security vulnerabilities and suggested efficient fixes..."
> **Impact:** "...which strengthened our security posture and saved the team hours of debugging later."

**Collaboration:**
> **Situation:** "In yesterday's architecture discussion..."
> **Behavior:** "...you asked clarifying questions and built on others' ideas instead of pushing your own solution..."
> **Impact:** "...which helped us reach consensus faster and made everyone feel heard."

### Constructive Feedback

**Missed Deadlines:**
> **Situation:** "When we were finalizing the API deployment last Thursday..."
> **Behavior:** "...your testing results came in two hours after our agreed cutoff..."
> **Impact:** "...which delayed the release, risked our SLA, and caused the QA team to work overtime."

**Meeting Behavior:**
> **Situation:** "In our sprint planning yesterday..."
> **Behavior:** "...you were on your phone for most of the discussion and didn't contribute when we asked for estimates..."
> **Impact:** "...which left the team without your expertise on the backend stories and made others feel their time wasn't valued."

**For more examples:** See `references/feedback-sbi-model.md`

## Common Difficult Scenarios

### Scenario: Performance Issue

**Situation:** A developer consistently delivers code with bugs.

**Approach:**

1. **Prepare:** Gather specific examples (PRs, bug counts, timelines)
2. **Deliver:** "I've noticed [X bugs in last Y PRs]. I want to understand what's happening and how I can support you."
3. **Explore:** Ask about workload, clarity of requirements, testing confidence
4. **Collaborate:** "What would help you feel more confident about code quality?"
5. **Follow-up:** Check in after agreed changes, recognize improvements

### Scenario: Conflict Between Team Members

**Situation:** Two engineers disagree on technical approach and it's affecting the team.

**Approach:**

1. **Meet separately first:** Understand each perspective
2. **Find common ground:** What do they both want? (Working product, good code, etc.)
3. **Facilitate together:** Focus on facts and trade-offs, not personalities
4. **Establish decision process:** How will the team decide when there's disagreement?
5. **Follow-up:** Check that the solution is working

### Scenario: Unrealistic Expectations

**Situation:** Leadership wants a feature in half the time needed.

**Approach:**

1. **Prepare:** Data on similar past work, breakdown of required tasks
2. **Deliver:** "I want to make sure we're aligned on what's realistic. Here's what I'm seeing..."
3. **Present trade-offs:** "We can hit that date if we [reduce scope/add people/accept risk]"
4. **Collaborate:** "What's most important here - the date or the full feature set?"
5. **Document:** Get agreement in writing to avoid future misalignment

**For detailed scripts:** See `references/difficult-conversation-scripts.md`

## Receiving Feedback Well

When you're on the receiving end:

### During the Conversation

1. **Listen fully** - Don't prepare your defense while they're talking
2. **Ask clarifying questions** - "Can you give me a specific example?"
3. **Paraphrase to confirm** - "So what you're saying is..."
4. **Acknowledge impact** - Even if intent was different: "I can see how that affected you"
5. **Don't get defensive** - Thank them for raising it

### After the Conversation

1. **Reflect honestly** - Is there truth in the feedback?
2. **Identify actions** - What will you do differently?
3. **Follow up** - Let them know what you're changing
4. **Ask for ongoing feedback** - Show you're committed to growth

## Quick Reference: Difficult Conversation Checklist

### Before

- [ ] I understand the specific issue
- [ ] I have concrete examples
- [ ] I've defined my goal for the conversation
- [ ] I'm emotionally regulated
- [ ] I've considered their perspective

### During

- [ ] I opened with neutrality and intent
- [ ] I stated facts, not blame
- [ ] I used SBI for specific feedback
- [ ] I asked for their perspective
- [ ] I focused on solutions, not just problems
- [ ] I documented agreed actions

### After

- [ ] I sent a follow-up summary
- [ ] I scheduled a check-in
- [ ] I'm providing ongoing support
- [ ] I'm recognizing progress

## Companion Resources

- `references/feedback-sbi-model.md` - Full SBI framework with more examples
- `references/difficult-conversation-scripts.md` - Opening lines and responses
- `references/expectation-alignment.md` - Managing stakeholder expectations


## Recommended Reading

- "Crucial Conversations" by Kerry Patterson & Joseph Grenny
- "Difficult Conversations" by Stone, Patton, Heen
- "Radical Candor" by Kim Scott
- Amy Edmondson's research on psychological safety

---


---


## 📚 Reference Materials


### Feedback Sbi Model

# SBI Feedback Model - Complete Guide

The Situation-Behavior-Impact (SBI) model, developed by the Center for Creative Leadership, provides a structured approach to giving clear, objective feedback that minimizes defensiveness and promotes improvement.

## The SBI Formula

```text
Situation + Behavior + Impact = Effective Feedback
```

| Component | What It Is | What It's NOT |
| --- | --- | --- |
| **Situation** | When and where the behavior occurred | Vague timing or generalizations |
| **Behavior** | Observable actions the person took | Assumptions about motives or character |
| **Impact** | Effect on you, the team, or the project | Judgment or evaluation |

## Why SBI Works

1. **Specificity** - Concrete examples are harder to dismiss than vague complaints
2. **Objectivity** - Observable behaviors can't be argued; interpretations can
3. **Actionability** - Clear situations and behaviors can be repeated or changed
4. **Non-threatening** - Impact focuses on effects, not character attacks

## SBI Examples: Positive Feedback

### Code Review Excellence

> **Situation:** "In the code review for the payment processing module last Tuesday..."
>
> **Behavior:** "...you not only caught the edge case that would have caused data loss, but you also suggested a more elegant solution using the repository pattern and included tests that demonstrated the issue..."
>
> **Impact:** "...which prevented a potentially costly bug in production and taught our junior developers about defensive coding. The whole team learned from your thoroughness."

### Meeting Contribution

> **Situation:** "During our architecture decision meeting this morning..."
>
> **Behavior:** "...you asked three questions that reframed the problem and suggested we document our trade-offs in an ADR..."
>
> **Impact:** "...which helped us reach consensus in half the usual time and gave us documentation we can reference later."

### Mentorship

> **Situation:** "Over the past month while onboarding Maya..."
>
> **Behavior:** "...you've been setting up daily 15-minute check-ins, creating a shared doc of resources, and giving her progressively more complex tasks..."
>
> **Impact:** "...which has accelerated her ramp-up significantly. She submitted her first independent PR two weeks ahead of our typical onboarding timeline."

### Cross-Team Collaboration

> **Situation:** "When the DevOps team asked for help migrating the deployment pipeline last week..."
>
> **Behavior:** "...you volunteered to pair with their engineer, documented the process as you went, and stayed late to ensure the cutover succeeded..."
>
> **Impact:** "...which built goodwill between our teams and created documentation that will help us next time."

## SBI Examples: Constructive Feedback

### Missed Deadlines

> **Situation:** "This sprint, the API endpoint work was due on Wednesday..."
>
> **Behavior:** "...it was delivered on Friday without any communication about the delay..."
>
> **Impact:** "...which blocked the frontend team for two days, caused them to miss their milestone, and created tension between our teams."

**Follow-up prompt:** "What happened? Is there something blocking you that I should know about?"

### Code Quality Issues

> **Situation:** "In the last three PRs you submitted..."
>
> **Behavior:** "...there were no unit tests and several of the changes broke existing tests that weren't updated..."
>
> **Impact:** "...which caused the CI pipeline to fail repeatedly, blocked other developers' merges, and required the tech lead to spend time debugging."

**Follow-up prompt:** "I want to understand what's making testing difficult. Is there something about our test setup or your workload that's contributing to this?"

### Meeting Behavior

> **Situation:** "In yesterday's standup..."
>
> **Behavior:** "...when Alex was describing their blocker, you interrupted three times to offer solutions before they finished explaining..."
>
> **Impact:** "...which made Alex visibly frustrated and may have prevented them from sharing the full context of the problem."

**Follow-up prompt:** "I know you're eager to help, and that's great. Can we try letting people finish before jumping in?"

### Communication Gaps

> **Situation:** "When the production issue happened on Monday..."
>
> **Behavior:** "...you started investigating immediately but didn't post updates in the incident channel for 45 minutes..."
>
> **Impact:** "...which left leadership and customer support without information to share with affected clients, and caused multiple people to ask for status updates, further distracting you."

**Follow-up prompt:** "During incidents, even brief updates help. What would make it easier to communicate while you're troubleshooting?"

### Lack of Engagement

> **Situation:** "In our last three sprint planning sessions..."
>
> **Behavior:** "...you haven't provided estimates or raised concerns about any of the stories..."
>
> **Impact:** "...which means we're missing your technical perspective, and some stories have turned out to be much larger than expected."

**Follow-up prompt:** "Your input is valuable. Is there something about our planning process that's making it hard to participate?"

## Common Mistakes to Avoid

### Mistake 1: Vague Situation

❌ "Recently..."
❌ "In general..."
❌ "Sometimes..."

✅ "In yesterday's standup..."
✅ "During the deployment on March 15th..."
✅ "In the code review for PR #1234..."

### Mistake 2: Interpreting Instead of Observing

❌ "You don't care about quality" (interpretation)
✅ "The PR had 12 bugs caught in QA" (observable)

❌ "You're not engaged" (interpretation)
✅ "You were on your phone during the demo" (observable)

❌ "You're being defensive" (interpretation)
✅ "You raised your voice and said 'that's not my fault'" (observable)

### Mistake 3: Generalizing Behavior

❌ "You always miss deadlines" (generalization)
✅ "The last three deliverables were past their due date" (specific)

❌ "You never test your code" (generalization)
✅ "The last two PRs had no unit tests" (specific)

### Mistake 4: Making Impact Personal

❌ "...which made me angry" (personal feeling as impact)
✅ "...which caused the release to be delayed" (business impact)

Personal feelings can be part of impact, but should be secondary to business/team impact:
"...which delayed the release and, honestly, created frustration for the team."

### Mistake 5: Forgetting the "So What"

Feedback needs clear impact to be meaningful. Without it:

❌ "In the meeting, you interrupted Alex three times." (So what?)

✅ "In the meeting, you interrupted Alex three times, which prevented them from fully explaining the problem and may have led to us missing important context."

## Extended SBI: Adding Intent and Next Steps

Some practitioners extend SBI to **SBI-I** (adding Intent) or **SBI-NS** (adding Next Steps):

### SBI-I: Checking Intent

After delivering SBI feedback, ask about intent:

> **SBI:** "When you pushed to main without a PR yesterday, it broke the build and blocked five developers for two hours."
>
> **I (Intent):** "I'm sure you didn't mean to cause a disruption. Can you help me understand what happened?"

### SBI-NS: Proposing Next Steps

End with a collaborative discussion of what to do differently:

> **SBI:** "When you gave estimates without checking with the backend team, the sprint became overcommitted and we had to drop two stories."
>
> **NS:** "Going forward, could we establish a quick sync with backend before finalizing sprint commitments? What would make that work for you?"

## When to Use SBI

| Scenario | Use SBI? | Notes |
| --- | --- | --- |
| Formal performance feedback | ✅ Yes | SBI provides documentation |
| Quick in-the-moment feedback | ✅ Yes | Keep it brief but structured |
| Positive recognition | ✅ Yes | Makes praise specific and meaningful |
| Annual reviews | ✅ Yes | Have multiple examples ready |
| Giving yourself feedback | ✅ Yes | Use for self-reflection |
| Venting frustration | ❌ No | Get calm first, then use SBI |

## Practice Exercise

Take a recent situation where you wanted to give feedback. Fill in:

**Situation:** ________________________________________________

**Behavior:** ________________________________________________

**Impact:** ________________________________________________

Now check:

- [ ] Is the situation specific (time/place)?
- [ ] Is the behavior observable (not an interpretation)?
- [ ] Is the impact clear (business/team effect)?

---

**Related:** Return to `feedback-mastery` skill for the full Preparation-Delivery-Follow-up framework.




### Difficult Conversation Scripts

# Difficult Conversation Scripts

Ready-to-use opening lines, responses to common reactions, and frameworks for navigating challenging workplace conversations.

## Opening Lines by Scenario

### Performance Issues

**General performance:**
> "I want to talk about how things have been going lately. My goal isn't to criticize - I want to understand what's happening and figure out how I can support you."

**Missed deadlines:**
> "I've noticed the last few deliverables have come in past their due dates. I want to understand what's been challenging so we can figure out how to get back on track."

**Quality concerns:**
> "I want to discuss some patterns I've noticed in recent work. I know you're capable of great work, so I'm curious what's been different lately."

**Attitude/engagement:**
> "I've sensed that something might be off lately, and I wanted to check in. Is there anything going on that's affecting how you're feeling about work?"

### Conflict Resolution

**Between you and a colleague:**
> "I feel like there might be some tension between us, and I'd rather address it directly than let it fester. Can we talk about what's been happening?"

**Facilitating between others:**
> "I've noticed some friction around [topic] and I want to help us work through it. I'm not taking sides - I just want to understand both perspectives and find a path forward."

**After a disagreement:**
> "I've been thinking about our conversation yesterday. I don't think we ended in a good place, and I'd like to revisit it when we're both calmer."

### Expectation Setting

**Unclear expectations:**
> "I want to make sure we're aligned on what success looks like for [project/role]. Can we talk through expectations so we're on the same page?"

**Resetting expectations:**
> "Based on what we're seeing, I think we need to adjust our expectations for [timeline/scope/quality]. Can we discuss what's realistic?"

**Boundary setting:**
> "I wanted to talk about workload. I'm finding it difficult to deliver quality work at the current pace. Can we discuss priorities?"

### Giving Difficult News

**Project cuts/changes:**
> "I have some difficult news about [project]. I want to share what I know and give you a chance to ask questions."

**Role changes:**
> "There are some changes coming that will affect your role. I wanted to tell you directly before any announcements."

**Ending a collaboration:**
> "I've made the difficult decision to [end/change] our working arrangement. I want to explain my thinking and discuss how to transition smoothly."

## Handling Common Reactions

### Defensiveness

**They say:** "That's not fair - you don't understand the whole situation!"

**You respond:**
> "I want to understand. Can you walk me through what happened from your perspective? I might be missing context."

**They say:** "I was just doing what I thought was right!"

**You respond:**
> "I believe you had good intentions. Let's talk about the impact regardless of intent, and how we can prevent this going forward."

### Deflection

**They say:** "Well, [other person] does the same thing!"

**You respond:**
> "Right now I want to focus on our conversation. If there are other issues to address, we can talk about those separately."

**They say:** "I've been so busy with [other project]..."

**You respond:**
> "I understand there's a lot on your plate. That's actually something I want to discuss too - how we can set you up for success given your workload."

### Emotion

**They become upset:**
> "I can see this is hitting hard. Would you like to take a few minutes? We can continue when you're ready, or pick this up another time."

**They shut down:**
> "I notice you've gone quiet. What are you thinking? It's okay to share how you're feeling about this."

**They get angry:**
> "I can see you're frustrated. I'd like to continue this conversation, but I need us both to stay respectful. Should we take a break?"

### Denial

**They say:** "That didn't happen / I didn't do that."

**You respond:**
> "I want to make sure we're talking about the same situation. [Describe specific instance]. Does that match your recollection?"

**They say:** "This is the first I'm hearing about this."

**You respond:**
> "I should have raised this sooner - that's fair feedback for me. Now that it's on the table, can we discuss how to move forward?"

### Counter-attack

**They say:** "Well, what about when YOU [did something]?"

**You respond:**
> "That's a fair point, and I'm open to feedback. Let's finish this conversation first, and then I'd like to hear your concerns about me."

### Agreement Without Change

**They say:** "You're right, I'll do better."

**You respond:**
> "I appreciate that. Let's get specific about what 'doing better' looks like so we're both clear. What will you do differently, and how can I support you?"

## Phrases for Key Moments

### Staying Curious

- "Help me understand..."
- "What was your thinking when..."
- "What were you trying to accomplish?"
- "What would you do differently now?"
- "What would help you in this situation?"

### Acknowledging Their Perspective

- "I can see how you'd feel that way given..."
- "That makes sense from your point of view."
- "I hadn't considered that angle."
- "I appreciate you sharing that."
- "That's a fair point."

### Redirecting to Solutions

- "Now that we understand the situation, what can we do going forward?"
- "What would help prevent this in the future?"
- "What do you need from me to succeed?"
- "How can we work together on this?"
- "What's one thing you could try differently?"

### Setting Clear Expectations

- "Going forward, I need..."
- "What I'm asking for is..."
- "Here's what success looks like..."
- "The expectation is..."
- "By [date], I'd like to see..."

### Closing Constructively

- "I appreciate you having this conversation with me."
- "I know this wasn't easy to hear. Thanks for being open."
- "I'm confident we can work through this."
- "Let's check in on [date] to see how things are going."
- "My door is open if you want to talk more."

## Scenario Playbooks

### Playbook: Addressing Chronic Lateness

**Opening:**
> "The standup has started at 9:00 AM, and I've noticed you've joined around 9:15 the last several days. I want to understand what's happening."

**If they're defensive:**
> "I'm not trying to call you out. I just want to make sure there isn't something I'm missing - is there a scheduling conflict or something else going on?"

**If there's a reason:**
> "Thanks for sharing that. Let's figure out a solution that works - could we adjust the meeting time, or is there something else that would help?"

**If there's no clear reason:**
> "I need everyone present at the start for standups to be effective. Can you commit to being there at 9:00 going forward? What would help you make that happen?"

**Follow-up:**
> "Let's check in next week. If you're on time consistently, we'll consider this resolved."

### Playbook: Quality Issues in Code

**Opening:**
> "I've noticed the last few PRs have had more issues than usual - bugs making it to QA, tests failing, that kind of thing. I want to understand what's going on."

**Explore:**

- "Are the requirements clear?"
- "Is the timeline realistic?"
- "Is there something about our testing setup that's making it harder?"
- "Is there other work pulling your attention?"

**If it's workload:**
> "It sounds like you're stretched too thin. Let's look at your commitments and see what we can adjust."

**If it's skill:**
> "Would it help to pair with someone on the next feature? Or would a refresher on our testing practices be useful?"

**If it's unclear:**
> "Here's what I need going forward: PRs should have tests, and they should pass CI before review. What would help you meet that bar consistently?"

### Playbook: Conflict Between Team Members

**Meeting with each person separately first:**

> "I've noticed some tension between you and [colleague] around [topic]. Before we all meet, I want to understand your perspective. What's been happening from your point of view?"

**Facilitating together:**

> "Thank you both for being here. My goal is to help us find a path forward that works for everyone. I'm not here to judge who's right - I want to understand both perspectives and figure out how we move forward as a team."
>
> "[Person A], can you share your perspective? [Person B], I'd like you to just listen for now - you'll have a chance to share next."

**Finding common ground:**

> "It sounds like you both want [shared goal]. Where you differ is [specific disagreement]. Is that right?"

**Resolution:**

> "What would a good solution look like for each of you?"
>
> "Can we agree on [compromise/process] going forward?"

### Playbook: Pushing Back on Leadership

**Opening:**
> "I want to make sure we deliver something great, and I have some concerns about the current timeline/scope I'd like to discuss."

**Present data:**
> "Based on similar past work and our current capacity, here's what I'm seeing: [data]. The timeline we have assumes [optimistic conditions]."

**Offer trade-offs:**
> "We can hit the date if we [reduce scope / add resources / accept these risks]. Or we can deliver the full scope by [realistic date]. What's most important here?"

**If pushed back:**
> "I understand there's pressure on this. I want to set us up for success, which means being honest about what's realistic. If we commit to this timeline, here are the risks we're accepting..."

**Document:**
> "Can I send a summary of what we agreed to? I want to make sure we're aligned and can reference this later."

## Words to Avoid

| Avoid | Use Instead |
| --- | --- |
| "You always..." | "I've noticed a pattern where..." |
| "You never..." | "In recent instances..." |
| "You should have..." | "Going forward, I'd like..." |
| "That's wrong" | "I see it differently" |
| "Obviously..." | "From my perspective..." |
| "To be honest..." | (Just be honest without the disclaimer) |
| "Don't take this personally" | (Just don't make it personal) |
| "No offense, but..." | (Just don't be offensive) |

---

**Related:** Return to `feedback-mastery` skill for the full framework, or see `feedback-sbi-model.md` for structuring specific feedback.




### Expectation Alignment

# Expectation Alignment Guide

Frameworks for setting, communicating, and managing expectations with teammates, stakeholders, and leadership.

## Why Expectations Misalign

Common causes of expectation misalignment in software teams:

| Cause | Example |
| --- | --- |
| **Implicit assumptions** | "I assumed we'd use the existing API" vs "I thought we'd build a new one" |
| **Different definitions** | "Done" means code complete to you, but deployed to them |
| **Information gaps** | You know about a dependency; they don't |
| **Timeline optimism** | Estimates given under pressure without accounting for reality |
| **Scope creep** | Small additions pile up into major differences |
| **Communication gaps** | Updates not shared, changes not communicated |

## The Expectation Alignment Framework

### 1. Define Success Explicitly

Never assume alignment. Make success criteria explicit:

| Vague | Explicit |
| --- | --- |
| "Make it fast" | "Page load under 2 seconds on 3G" |
| "High quality" | "Zero P1 bugs in first month; 80% test coverage" |
| "Soon" | "By end of sprint (Friday 5pm)" |
| "User-friendly" | "New users can complete checkout in under 3 minutes" |

### 2. Expose Assumptions

Before starting work, surface hidden assumptions:

**Questions to ask:**

- "What are you assuming about [timeline/resources/scope]?"
- "What would make this harder than expected?"
- "What's the worst case scenario?"
- "What dependencies are we assuming will be available?"
- "What's the definition of 'done' for this?"

### 3. Document and Share

Expectations that aren't written down don't exist:

- Put acceptance criteria in tickets
- Send follow-up emails after verbal agreements
- Update documentation when things change
- Share publicly (not just DMs)

### 4. Check Alignment Regularly

Don't wait until delivery to discover misalignment:

- Daily standups surface blockers early
- Mid-sprint check-ins catch scope creep
- Demo progress before "done" to validate direction
- Regular stakeholder updates prevent surprises

## Scenario: Unrealistic Deadlines

### Recognition Signs

- Timeline set without engineering input
- Estimates significantly lower than past similar work
- No buffer for unknowns
- Dependencies assumed to be risk-free
- "We just need to..." (minimizing language)

### Response Framework

**1. Acknowledge the goal:**
> "I understand hitting this date is important for [business reason]."

**2. Present data:**
> "Based on similar past work, this typically takes [X]. Here's the breakdown..."

**3. Offer options:**
> "We have a few paths:"
>
> - "Hit the date by reducing scope to [core features]"
> - "Deliver full scope by [realistic date]"
> - "Add resources, but onboarding will take [time]"

**4. Get explicit agreement:**
> "Which approach would you like? Can I document this so we're aligned?"

### Sample Conversation

> **Stakeholder:** "We need the new dashboard by end of month."
>
> **You:** "I want to make sure we deliver something great. Let me share what I'm seeing: the full dashboard - charts, filters, export - typically takes 6-8 weeks. We have 4 weeks."
>
> **Stakeholder:** "We really need it by end of month."
>
> **You:** "I hear you. Here's what we could do: ship the core charts by end of month, then add filters and export in the following two weeks. That gets value to users faster. Would that work?"
>
> **Stakeholder:** "What about just working faster?"
>
> **You:** "I wish that worked, but rushing usually creates bugs that cost more time later. The scope-first approach gets you something solid you can show stakeholders. What matters most - the date or the full feature set?"

## Scenario: Unclear Role Boundaries

### Recognition Signs

- Tasks falling through cracks
- Duplicated work
- "I thought you were handling that"
- Confusion about who decides what
- Finger-pointing when things go wrong

### Response Framework

**1. Identify the gap:**
> "I've noticed some confusion about who owns [task/decision]. Can we clarify?"

**2. Propose explicit ownership:**
> "My understanding is that [Person A] owns [X] and [Person B] owns [Y]. Does that match your understanding?"

**3. Define handoff points:**
> "The handoff happens when [specific condition]. At that point, [Person B] takes over."

**4. Document and share:**
> "Let me send a quick summary so everyone's aligned."

### RACI for Common Confusion Points

| Task | Responsible | Accountable | Consulted | Informed |
| --- | --- | --- | --- | --- |
| Code complete | Developer | Developer | Reviewer | Team |
| PR merged | Reviewer | Developer | - | Team |
| Deployed to staging | DevOps/Dev | Team Lead | QA | Stakeholders |
| Bug triage | Tech Lead | Product | Development | Stakeholders |
| Scope changes | Product | Product | Tech Lead | Development |

## Scenario: Scope Creep

### Recognition Signs

- "Can we just add..."
- "One more thing..."
- Requirements changing mid-sprint
- Original estimate no longer realistic
- Features growing beyond initial spec

### Response Framework

**1. Acknowledge the request:**
> "That's a good idea. Let me think through the impact."

**2. Quantify the change:**
> "Adding [feature] would take approximately [time]. That would push our delivery from [date] to [new date]."

**3. Present trade-offs:**
> "We could add this by dropping [other feature] or extending the timeline. Which would you prefer?"

**4. Get explicit approval:**
> "Before I add this to the scope, can you confirm [the trade-off]?"

### The Scope Change Template

When scope changes are requested, respond with:

```markdown
**Change Request:** [What's being asked]

**Impact:**
- Timeline: [Current] → [New]
- Resources: [Any changes]
- Risk: [New risks introduced]

**Options:**
1. Add scope, extend timeline to [date]
2. Add scope, drop [other feature]
3. Defer to next phase

**Recommendation:** [Your suggestion]

**Need decision by:** [Date]
```

## Setting Expectations Proactively

### At Project Start

- Define success criteria explicitly
- Surface assumptions and dependencies
- Establish communication cadence
- Agree on decision-making process
- Document in shared location

### During Work

- Regular status updates (before asked)
- Early warning on blockers
- Proactive communication of changes
- Demo progress incrementally
- Update documentation as things change

### At Delivery

- Confirm acceptance criteria met
- Document what was delivered vs planned
- Capture lessons learned
- Set expectations for next phase

## Phrases for Expectation Conversations

### Setting Expectations

- "Let me make sure we're aligned on..."
- "Here's what I'm committing to..."
- "My understanding is that success looks like..."
- "Just to be explicit about scope..."
- "The definition of 'done' for this is..."

### Surfacing Misalignment

- "I want to flag a concern about..."
- "I think we might have different assumptions about..."
- "Can we clarify what we mean by...?"
- "I'm seeing a gap between [X] and [Y]..."
- "Before we go further, I want to make sure..."

### Resetting Expectations

- "Based on what we're learning, we need to adjust..."
- "The original estimate didn't account for [X]..."
- "Here's what's realistic given [constraints]..."
- "I need to revise what I committed to because..."
- "Let me give you an updated picture..."

### Getting Commitment

- "Can I confirm that we're agreeing to...?"
- "Let me send a summary so we're all aligned."
- "Is everyone okay with this plan?"
- "Before we proceed, I need explicit approval for..."
- "Can you confirm in writing?"

## Expectation Alignment Checklist

### Before Starting Work

- [ ] Success criteria are explicit and measurable
- [ ] Timeline includes buffer for unknowns
- [ ] Dependencies are identified and owners confirmed
- [ ] Assumptions are documented
- [ ] Communication plan is agreed
- [ ] Decision-making process is clear
- [ ] RACI or ownership is explicit

### During Work

- [ ] Regular updates before being asked
- [ ] Blockers raised immediately
- [ ] Changes communicated proactively
- [ ] Progress demonstrated incrementally
- [ ] Documentation updated as things change

### At Delivery

- [ ] Acceptance criteria reviewed before "done"
- [ ] Stakeholder sign-off obtained
- [ ] Gaps between planned and delivered documented
- [ ] Next steps and expectations clear

---

**Related:** Return to `feedback-mastery` skill for difficult conversation frameworks.




---

## 🚀 Usage

**Reference this template:** `@skill-feedback-mastery.md`


**Platform-specific:**
- **GitHub Copilot**: Add to `.github/copilot-instructions.md`
- **Augment Code**: Use `aug context add` command
- **Cursor/Windsurf**: Reference in chat with `@filename`
- **Claude**: Add to Project Knowledge
- **ChatGPT**: Add to Custom GPT configuration
