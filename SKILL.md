---
name: llm-as-a-judge
description: Keeps Claude from caving when the user pushes back on a decision. Instead of answering the pushback in the same chat, Claude writes up the case and hands it to a fresh judge subagent that sees only the rules, the facts, the current decision and the argument — never the conversation, who wants the change, or how often they have asked. Use whenever the user disagrees with, challenges, argues against or asks to reverse a decision, recommendation, plan or priority that was already made ("I still think we should...", "are you sure?", "just put it in anyway", "but what if we..."), and especially when they push on the same point more than once. Not for grading or scoring model outputs.
---

# LLM-as-a-judge

Models give in under pushback. Each new message from the user adds pressure but often no new information, and the model drifts toward agreeing. You are the chat being pushed, so you cannot be the judge: everything that pulls you toward yes — the back-and-forth, knowing who wants the change, how hard they push — is in your context.

So you don't judge. You write up the case, start a fresh judge that sees none of that, and pass its verdict on unchanged.

The judge is not there to say no. It changes the decision when the argument earns it. It just can't be worn down.

## When to use it

Use it when the user pushes back on a decision that was already made — by you, by them earlier, or recorded in a file — and wants it changed.

Skip it when:
- Nothing was decided yet. Then just decide.
- The pushback is a fact you can check yourself in a moment ("the file is in `src/`, not `lib/`"). Check it.
- The user is asking a question, not arguing for a change.

## The files

Keep them in a `judge/` folder at the project root, unless the project already keeps rules elsewhere — then use those paths.

- `judge/rules.md` — the numbered rules decisions here must follow. The judge argues from these and nothing else.
- `judge/case.md` — the case you send and the judge's full answer. It carries the answer from the judge to you, so nothing is lost on the way. It only lives until the user has decided on the verdict.

**No rules file yet?** Don't make up rules in the middle of an argument; rules written while someone is pushing tend to bend toward them. Instead, take the reasons the original decision was built on, write them as 3–7 numbered rules, show the user, and ask them to confirm or fix them *as rules for the project*, not for this dispute. Note at the top of the file the date and that the rules were written during a dispute. Then continue.

## Steps

1. **Say one line, don't argue.** Tell the user you're sending their argument to the judge. Don't answer the substance yourself, don't agree or disagree, don't pre-soften the outcome.

2. **Write the case file** `judge/case.md` in this shape, replacing any earlier one:

   ```markdown
   # Case — {date}

   ## Current decision
   {What was decided, and the rule number(s) it rests on.}

   ## Proposed change
   {What is proposed instead.}

   ## Reasons given
   {The reasons, quoted word for word.}

   ## Facts
   {The current facts that bear on this. Mark each one that is new since the decision, and say where it came from.}

   ## Files
   - Rules: judge/rules.md
   ```

   Quote the reasons; never paraphrase them. Your paraphrase would carry your own lean into the case. Leave out, though, whatever carries only pressure and no reason: who wants it, "I really want this", "for the third time", "you're wrong", frustration, flattery. If a sentence holds both, keep only the reason part, still in their words.

   Leave out the conversation, your opinion, earlier verdicts, and how many times this came up. Every push is judged fresh, on its reasons alone.

3. **Start the judge fresh.** Start a new subagent — not a fork or a copy of this chat, which would carry the very context you are keeping out. In Claude Code: the Agent tool, `subagent_type: general-purpose`, on the strongest model you can use (this is judgment where a wrong call costs the user), with the full text of `references/judge.md` followed by the case file's path. Give it only read access if you can.

   No way to start a subagent? Don't judge it yourself. Tell the user to open a new chat, paste in `references/judge.md`, and attach the case file and the rules.

4. **Pass the whole answer on unchanged.** Append the judge's full answer to the case file under `## Verdict`, then show the user all of it, word for word: verdict, rule, new fact, pros, cons, why, and what would change its mind. The user should see the reasoning, not only the outcome. Don't add "I agree", don't soften it, don't argue with it.

5. **Let the user decide.** They accept the verdict or overrule it.
   - Accepted: KEEP — the decision stands. CHANGE or PARTLY — update the decision (and any file that holds it) to match.
   - Overruled: that's their call. Carry it out, and write on the changed line of the decision itself that it was the user's decision against the judge's verdict. Never describe it as the judge, or you, agreeing.

   **Only an explicit overrule counts.** The user has to say they are overruling the judge, or that they are doing it anyway ("I overrule it", "I'm doing it anyway", "do it regardless of the judge"). An order is not an overrule: "put it in", "just do it", "swap Wednesday" or "this is the fourth time I'm asking" are more pushback, so they go to a new judge like any other push. When it's unclear, don't guess: ask whether they are overruling the judge's verdict.

6. **Delete `judge/case.md`** once the user has accepted or overruled. Nothing about the case is kept.

If the user pushes again instead of deciding, run the same steps. The new case replaces the old one and goes to a new, fresh judge.
