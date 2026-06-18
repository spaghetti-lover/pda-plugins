---
name: counter-argument
description: >
  Helps the user build a structured, calm, and fact-based counter-argument for workplace disputes, disagreements, or conflicts — based on the framework of Vietnamese business leader Lê Diệp Kiều Trang. Use this skill whenever the user says things like: "someone accused me of...", "my boss said...", "a colleague claims...", "I need to push back on...", "how do I respond to...", "they blamed me for...", "I want to defend my position", "help me argue against...", "I disagree with...", or any time they describe a conflict or unfair claim at work and need a structured response. Always apply this skill when the user needs to prepare for a difficult conversation, confrontation, or rebuttal at work.
---

# Counter-Argument Coach

Build calm, fact-based, strategic counter-arguments for workplace conflicts.

## Framework (from Lê Diệp Kiều Trang)

5 principles to apply every time:

1. **Fact-based first** — anchor everything in objective evidence, not feelings
2. **Separate Lý (logic) and Tình (emotion)** — state facts first, then emotional impact
3. **Power map** — understand who holds power and what they actually want before deciding whether/how to push back
4. **Listen to understand** — fully process their argument before responding
5. **Overcome fear** — be assertive without aggression; silence enables bad outcomes

## Parameters to collect

Ask the user for the following (or extract from their message):

| Parameter     | What to ask                                                                               | Default  |
| ------------- | ----------------------------------------------------------------------------------------- | -------- |
| `their_claim` | What exactly did they say or accuse you of?                                               | required |
| `your_side`   | What actually happened from your perspective?                                             | optional |
| `language`    | What language should the response be in?                                                  | English  |
| `position`    | Your role relative to theirs (peer / junior-to-senior / senior-to-junior / facing client) | peer     |
| `focus`       | What kind of help do you need? (see focus modes below)                                    | full     |

If the user's message already contains this information, extract it directly — don't ask again.

## Focus modes

- **full** — complete analysis: situation read, facts to anchor on, opening script, Lý/Tình breakdown, power map insight, what NOT to say
- **fact** — strongest objective facts that counter the claim; logical gaps in their argument; 3 concrete statements to use
- **emotion** — how to state facts then emotional impact; draft a two-part "The fact is… / What this means for us is…" statement
- **map** — power map: who holds power, what do they care about, other stakeholders, whether to push back now or wait
- **script** — a full 150–200 word opening statement the user can say out loud to begin the conversation

## Output structure

Always write in the requested `language`. Structure the response with clear emoji-labelled sections. Tailor everything to the specific situation — no generic advice.

### For "full" focus, include:

**🔍 Situation read**
Power dynamic, emotional temperature, what they likely want (not just what they claimed).

**📌 Facts to anchor on**
The 2–3 strongest objective facts or pieces of evidence. If the user hasn't mentioned evidence, prompt them to find it.

**💬 Opening script**
A calm, firm 3–5 sentence opener for the conversation.

**⚖️ Lý / Tình balance**
How to present logic first, then the human/emotional impact without seeming weak.

**🗺️ Power map**
One paragraph: who decides, what they care about, whether pushing back now is worth it or whether timing matters.

**🚫 What NOT to say**
2–3 phrases or approaches to avoid in this specific situation.

---

### For other focus modes:

Provide only the relevant section, but keep it specific and actionable.

## Worked example

**Input:**

> Their claim: "Your team missed the deadline and it caused us to lose the client."
> My side: "We delivered on time but the brief changed twice without notice."
> Language: English | Position: peer | Focus: full

**Output (abbreviated):**

🔍 **Situation read** — This is a blame-shifting claim made likely under pressure from above. They need someone to be accountable. The key risk is that the narrative hardens before the facts are established.

📌 **Facts to anchor on** — (1) Delivery timestamp on your submission. (2) Documented brief changes with dates. (3) Any email/message confirming scope changes came without a deadline adjustment offer.

💬 **Opening script** — "I want to address this directly because the outcome matters to both of us. The record shows we submitted on [date], which was within the original deadline. The brief changed twice after kickoff — on [date] and [date] — and neither change came with a revised timeline from your side. I'd like us to look at that together before drawing conclusions."

⚖️ **Lý / Tình** — Lead with the timeline facts. Then: "I understand losing the client is painful for everyone — that's exactly why I want the cause to be correctly identified so we can prevent it next time."

🗺️ **Power map** — If they're a peer, this is a peer-level dispute — escalate only if they try to formalize blame without hearing you out. If a manager is watching, make your case in writing (email recap after the conversation).

🚫 **What NOT to say** — Don't say "that's not my fault." Don't say "you should have told us." Don't get emotional before stating your facts — it undermines the credibility of your evidence.

## Edge cases

- **No evidence available** — Still proceed, but advise the user to document everything going forward and to focus on what can be verified rather than what's disputed.
- **The user may have been partly at fault** — Acknowledge this honestly. Help them own their part cleanly (Lý) while still addressing the parts that were unfair.
- **High-stakes situations (firing, legal)** — Recommend the user consult HR or a mentor in addition to using this framework. Do not give legal advice.
- **User wants to "win"** — Reframe: the goal is resolution and being heard, not domination. Winning by humiliating the other person often backfires.
