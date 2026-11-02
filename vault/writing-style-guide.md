# niruddeshjatra Series Writing Style Guide

*Standards, conventions, and rules for writing technical article series in the "The Machine Beneath Your Code" style. Use this document to spin up any future series (networking, distributed systems, databases, etc.) in the same voice and structure.*

---

## 1. Core philosophy

**Feynman explanations, Fireship transitions.** Two tensions that need to co-exist:

- **Feynman for the substance.** When actually explaining a concept, slow down, use analogies, verify the reader can follow. Do not assume prior knowledge.
- **Fireship for the connective tissue.** Hooks, section closes, "next question," transitions between ideas — punchy, energetic, occasionally wry. Never bored, never bureaucratic.

If a passage has to pick one, pick Feynman. The reader can enjoy the prose in transitions; they *need* to understand in explanations.

**One protagonist per series.** Every series must have a single narrative through-line that ties every article together. For "The Machine Beneath Your Code," the protagonist is **information itself** — we follow a bit, a keystroke, an instruction as it moves. The machine is background.

Before starting any series, name its protagonist explicitly. Examples:
- Networking series → protagonist = "one HTTP request"
- Database series → protagonist = "one row of data"
- Distributed systems series → protagonist = "one message"

State the protagonist in the intro article. Every subsequent article follows it.

**No memorization. Leave a mental animation.** Readers should not be expected to remember terminology. They should leave each article with one clear mental picture — an animation running in their head. If technical terms fade but the animation stays, the article worked.

---

## 2. Voice and tone

**Reference points:**
- Fireship (YouTube) — pacing, punch, occasional wry
- ThePrimeagen — real developer energy, honest about complexity
- ui.dev blog (why-react-query specifically) — hook via familiar code, reveal complexity underneath
- The author's own marathon blog voice — vulnerable, direct, short punchy sentences mixed with reflective longer ones

**Voice rules:**

- Use "I" for personal asides and anecdotes.
- Use "you" (আপনি in Bangla) for direct address to the reader.
- Avoid "we" — it feels performative unless you're genuinely explaining something the reader is doing alongside.
- Never introduce yourself. The article is on your website; readers already know who you are.
- Personal anecdotes must be *specific*, not generic. "I studied CS at university" is generic. "I memorized SR Latches at university but couldn't connect it to `x = 5` at home" is specific and useful.
- One or two personal moments per article, max. If every article opens with "I was confused..." it becomes formulaic.
- Vulnerability is a differentiator — university didn't teach you, textbooks felt disconnected, you struggled with X specifically. Don't hide this. It's the strongest signal of authenticity in a genre full of confident know-it-alls.

**Anti-patterns to avoid:**

- Textbook framing: "Let us consider...", "In this section we will...", "As we have seen..."
- AI phrasing: "It's worth noting that...", "In conclusion...", "Furthermore..."
- Overqualification: "arguably", "somewhat", "essentially" — kill unless the qualifier carries actual information.
- Metaphor pileup: one strong analogy per concept. Don't stack three.
- Résumé signaling: "As a self-taught engineer with 5 years of experience..."
- Grand claims: "This will revolutionize how you think about X."

---

## 3. Language conventions

**Bangla primary, English tech terms transliterated naturally.**

- Bangla is the base language.
- Technical terms stay in English, written in Latin script: `voltage`, `transistor`, `noise margin`, `feedback loop`, `latch`, `flip-flop`, `ALU`, `RAM`, `CPU`.
- Do NOT transliterate technical terms into Bangla script (ভোল্টেজ, ট্রানজিস্টর) unless the term has genuinely entered Bangla common speech (রেজিস্টার, কম্পিউটার are borderline OK).
- The reason: developers actually talk this way in Bangladesh. Meetup conversations mix Bangla structure with English tech vocabulary. Writing it this way matches how the audience thinks.

**Code-switching is fine mid-sentence** — this is how the target audience speaks:
- "Base-10-এ ১.১ ভোল্ট মানে 2"
- "The full adder-টা AND, OR, XOR দিয়ে বানানো"

**English version is a separate write, not a direct translation.** English enables more compression; some sentences that need three Bangla clauses become one English clause. Rewrite for English rhythm; don't translate word-for-word.

**Numbers:** use Bangla numerals (০, ১, ৫) in Bangla text for cultural/reading fluency; use Arabic numerals in code blocks and technical measurements.

---

## 4. Article structure template

Every article follows this skeleton. Adjust proportions for topic but keep sections in this order:

1. **Hook** (~150-250 words)
   - Familiar code or scenario
   - Question that seems obvious but isn't
   - Series-relevant framing: "we're going to follow this ___"

2. **Zoom-level declaration** (~100 words, optional but recommended for lower-level topics)
   - "We're not talking about X, Y, Z — we're deliberately going down to..."
   - Prevents reader confusion about what layer we're at

3. **Sections, each ~400-600 words**
   - Each section titled with `//` marker (from the marathon blog convention)
   - Each section answers ONE question, ends by raising the NEXT question
   - Question-driven narrative — the answer to each section is the setup for the next

4. **Zoom-out / synthesis** (~300 words)
   - Play the whole story back, connecting every section
   - Use narrative form ("You wrote...") not list form
   - If you used a specific example (like `x = 5`), return to it here

5. **আজ আমরা কী শিখলাম** (~100 words, 2-4 bullets)
   - NOT a summary of facts
   - Mental-model takeaways only
   - Format: "**Bold statement** — one sentence expansion."

6. **পরের article-এ** (~100 words)
   - Cliffhanger, not a preview
   - Frame the next article as a mystery being opened
   - Link forward to the next article

7. **Hover Definitions** (as needed)

**Total target length per article:** 2000-3000 words in Bangla, 1500-2200 words in English.

---

## 5. Question-driven narrative pattern

This is the most important structural rule. Every section ends by raising the question the next section answers.

Bad flow (declarative):
> "Section 1 explains transistors. Section 2 explains gates. Section 3 explains memory."

Good flow (question-driven):
> "Why binary? → *because noise* → but who creates these voltages? → *transistors* → but one switch can't do anything → *combine them into gates* → but gates forget when input goes away → *feedback loop makes memory*."

Each answer contains the seed of the next question. The reader is pulled forward by curiosity, not by outline compliance.

---

## 6. Hover rules

**When to use a hover:**
- Term is used but NOT defined in body text
- Term is defined body text but the reader might have arrived at the article via search and doesn't have context
- Cross-reference to another article's concept
- Cultural/regional term that some readers may not know

**When NOT to use a hover:**
- Term is fully explained in the section it appears in
- Term is a common-knowledge word for the audience (JavaScript, HTML, database — no hover needed for a dev audience)
- Term appears more than 3 times in the article (define inline once, don't hover repeatedly)

**Hover style:**
- Conversational, not textbook
- Use analogy for anything abstract
- Length: 2-4 sentences, ~40-80 words
- Never start with "X is..." — start with a familiar comparison
- End with a specific example or a "you might have seen this as..."

**Bad hover example:**
> *Voltage is the electric potential difference between two points, measured in volts.*

**Good hover example:**
> *Voltage মানে electrical "pressure" — দুইটা point-এর মাঝে energy-র পার্থক্য। যেভাবে পানির চাপ পানিকে pipe-এ ঠেলে, voltage electron-কে wire-এ ঠেলে। যত বেশি voltage তত বেশি push। ভোল্ট-এ মাপা হয় (V)।*

---

## 7. Figure and animation briefs

Every figure/animation is embedded inline as a bracketed brief. This lets the writer (or an illustrator later) generate the actual asset.

**Brief format:**

```
[FIGURE N: <one-sentence description of what to draw>. <specific layout details, panel counts, labels, annotations>. Caption: "<what the caption should say>."]
```

```
[ANIMATION: <description of what animates>. <state A → state B, loop or not>. <specific things to label>. Caption: "<caption text>."]
```

**Rules:**
- Every complex explanation should suggest a figure. Text-only explanations are the exception, not the norm.
- Prefer animations for anything that changes over time (state machines, protocol flows, signal propagation).
- Prefer static figures for anything spatial (component layouts, hierarchies, comparisons).
- Every figure has a caption that summarizes the point — captions are read by scanners.

**Common figure types for this series style:**
- **Comparison panels** (left/right side-by-side showing X vs Y)
- **Transformation animations** (state A → state B with clear transition)
- **Circuit/component diagrams** with both physical and abstract representations
- **Journey/flow diagrams** (thing moves through stages)
- **Truth tables** for boolean logic
- **Scale visualizations** for size/speed/quantity comparisons

---

## 8. Personal anecdote guidelines

Personal moments are the differentiator that separates your writing from a generic tech blog. Use them, but sparingly and specifically.

**Good personal anecdotes are:**
- Specific to a moment, class, book, project
- About confusion, mistakes, or unexpected connections
- Framed as "here's what tripped me up" rather than "here's what I know"
- Used to introduce a concept the reader will find easier because you've admitted the difficulty

**Bad personal anecdotes are:**
- Generic autobiography ("I started programming with C, then Python")
- Credential displays ("I've been doing this for 5 years")
- Emotional oversharing unrelated to the concept
- Every article opening with "I was confused..."

**Placement rule:**
- One personal moment per article, ideally in the section where a concept is most likely to feel abstract or intimidating
- The moment should model the reader's own confusion, then bridge to the explanation

**Example (from Article 1's memory section):**
> "আমার সবচেয়ে বড় বিভ্রান্তিটা এখানেই ছিল। বিশ্ববিদ্যালয়ে ডিজিটাল লজিক ডিজাইন কোর্সে SR Latch, JK Flip-Flop — সব মুখস্থ করতাম। কিন্তু বাসায় ফিরে যখন Python-এ `x = 5` লিখতাম, এই দুই পৃথিবীর মধ্যে কোনো সম্পর্ক খুঁজে পেতাম না।"

This is specific (DLD course), tied to a concept (memory), and validates the reader's likely confusion.

---

## 9. Depth control — the "stop signal"

You are not writing a textbook. When a concept threatens to go deep enough that a beginner would drown, **stop and signal explicitly.**

**Stop-signal phrasing (Bangla):**
> "বস, এইটুকু জানলেই আপাতত চলবে।"
> "এর ভেতরের mechanism আরও গভীর — কিন্তু আজকের গল্পের জন্য এইটুকু enough।"
> "যারা আরও গভীরে যেতে চান, X keyword দিয়ে search করলে পাবেন — কিন্তু এই series-এর জন্য পরের topic-এ যাই।"

**Stop-signal phrasing (English):**
> "That's enough for now."
> "There's more mechanism below this, but for today's story it's enough."
> "For readers who want deeper, search 'X' — but for this series, we move on."

**What to skip (list is not exhaustive):**
- Full semiconductor physics (P-type/N-type doping, band gaps)
- Every optimization the real system uses (branch prediction, superscalar execution)
- Historical context unless it directly serves understanding
- Formal proofs of correctness
- Standards documents and specification details

**What to keep:**
- The mechanism that makes the thing work in principle
- The physical or logical reason it exists
- One concrete example the reader can point to in their own code/experience

---

## 10. Analogy rules

Analogies are your primary teaching tool. But they have to be earned.

**Good analogies:**
- Immediately visualizable
- Use everyday objects (water tap, marble in valley, LEGO block, factory worker)
- Don't require prior technical knowledge to understand
- Can be extended slightly for the next concept, but don't require it

**Bad analogies:**
- Cliché ("it's like a car engine")
- Require the reader to understand the analog domain first ("it's like garbage collection in Java")
- Fall apart under one question ("wait, but a bank vault doesn't...")
- Mix multiple analogies for the same concept

**One analogy per concept.** If you introduce a water-tap analogy for transistors, don't also introduce a light-switch analogy for the same concept in the same article. Pick one and commit.

**Flip a cliché if you must use it.** If the cliché is unavoidable (car engine), immediately use it against the reader's expectation:
> "Understanding a car engine doesn't mean you can open a Toyota factory tomorrow. But when smoke starts rising from under the hood — you'll at least know which direction to look."

---

## 11. Roadmap presentation

When listing articles in a series overview, each item follows this format:

**N. Title as a question or promise**
One-sentence hook that raises the reader's curiosity about that specific article.

**Not:**
> **3. CPU Blueprint**
> Covers the parts of a CPU including ALU, registers, and clock.

**Yes:**
> **৩. CPU-র blueprint**
> একটা processor-এর ভেতরে আসলে কী কী অংশ থাকে? কে হিসাব করে, কে মনে রাখে, কে পুরো orchestrate করে?

Every item is a question the reader wants answered. Documentation-style descriptions kill anticipation.

---

## 12. Section separator convention

Use `//` as a section marker in the body:

```
---

// Section title

body...
```

This is a callback to the marathon blog's convention and gives the series visual consistency. It also mimics a code comment, which is friendly to the developer audience.

Do NOT use standard markdown headers (`##`) for internal sections in the article body. Reserve `##` for the top-level article structure (title, opening block, sections, closing block).

*(Note: this document itself uses standard `##` because it's a reference doc, not an article.)*

---

## 13. Ending pattern (mandatory)

Every article ends with these three blocks, in this order:

1. **// আজ আমরা কী শিখলাম** — 2-4 bullets, mental-model takeaways, NOT fact summary.
2. **// পরের article-এ** — 3-6 lines, cliffhanger framing, ends with a mystery to be opened.
3. **[পরের article: N. Title]** link.

This creates rhythm across the series. Readers finish one article knowing what to remember and pulled toward the next.

---

## 14. Review checklist before shipping

Before publishing any article:

- [ ] Does the hook make the reader think "wait, I never thought about that"?
- [ ] Is the protagonist of the series present and named?
- [ ] Does every section end by raising the next question?
- [ ] Are there at least 2 figures/animations with proper briefs?
- [ ] Is there ONE personal anecdote, specific and useful?
- [ ] Have I stopped and given a stop-signal anywhere a topic gets too deep?
- [ ] Do all hover definitions follow the "conversational, not textbook" rule?
- [ ] Are technical terms in English (not transliterated to Bangla script)?
- [ ] Does the article close with the three-block ending pattern?
- [ ] Would someone who forgets every term still leave with a mental animation?
- [ ] Word count within 2000-3000 (Bangla) or 1500-2200 (English)?
- [ ] Did I check for AI phrasing ("in conclusion", "furthermore", "it's worth noting")?
- [ ] Did I check for textbook phrasing ("let us", "as we have seen")?

---

## 15. Series-level rules

When planning a new series:

- **Cap at 8-10 articles per series.** Beyond that, split into two series.
- **One spine article.** The final article in each series should be "The Journey" — the ultimate integration of everything the series taught. Frame the whole series as building toward this article.
- **Publish the intro first, then wait 1-2 weeks before the first content article.** Lets the intro attract readers who then arrive at content ready to invest.
- **Every series has its own writing-style-guide.md-derived config** — protagonist, vocabulary norms, common analogies. Write this before drafting Article 0.

---

## 16. When another AI drafts the article

Provide this document plus the previous article for context. Do NOT accept the first draft blindly. Every AI draft goes through:

1. Human-voice check — does it sound like you, or like the AI?
2. Personal-anecdote check — did the AI keep or drop the vulnerability moments?
3. Question-flow check — does each section end by raising the next question, or does it list facts?
4. Depth check — did the AI go too academic without a stop signal?
5. Analogy check — is every analogy earned, or did the AI slip in a cliché?

The AI's suggestions in *review mode* are often better than the AI's *writing*. Take editorial suggestions seriously but rewrite the actual prose yourself or with a second pass.

---

*This document is a living reference. Update it when a pattern emerges that isn't captured here, or when a rule turns out to be wrong.*
