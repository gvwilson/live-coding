# Live coding `for` loops — with an LLM

-   Running time: 2:00
-   Audience: the same scientists, who know `cd`, `ls`, `cat`, `cut`, `<`, `>`, `|`, and `*` globbing.
-   Props: terminal at large font in the left window, a chat LLM in the right, both visible to the room.
-   Data (identical to the no-AI script): `station-a.csv`, `station-b.csv`, `station-c.csv` —
    header `date,temperature_c` plus twelve daily readings each.

Three pedagogically different uses of the LLM, in order:

1. **Drafts, students critique** — the model produces a first attempt; the room judges it against the lesson's goals.
2. **Explains our error, after students predict** — predict, run, then ask; the model interprets a failure the class has just seen.
3. **Writes the exit ticket, we grade it** — the model generates assessment items; the shell checks the answers.

In all three, the instructor prompts in public, reads the answer aloud, and never accepts it without running something.

---

## 0:00 — Same files, same line

**INSTRUCTOR:** Same three weather stations as the other session. Same line from last week pulls three readings out of one file.

```
$ head -n 10 station-a.csv | tail -n 3 | cut -d , -f 2
```

```
10.7
9.8
11.3
```

**INSTRUCTOR:** I want that for all three files. I'm going to ask the model to write it, and you're going to tell me whether to keep the answer.

## 0:15 — Use 1: the model drafts, we critique

**PROMPT (typed live, visible on screen):**

> Three CSV files in this directory, `station-a.csv`, `station-b.csv`, `station-c.csv`, each with header `date,temperature_c`. Write one shell command that prints the 7th–9th temperature from each file, using `head -n 10 | tail -n 3 | cut -d , -f 2`. Keep it to one command.

**LLM (plausible output):**

```
find . -maxdepth 1 -name 'station-*.csv' -exec sh -c '
  for f do
    printf "== %s ==\n" "$f"
    head -n 10 "$f" | tail -n 3 | cut -d , -f 2
  done' sh {} +
```

**INSTRUCTOR:** It works — I ran it before class. Look at what comes out, though: `./station-a.csv`. It added a `./` nobody asked for. And look at what it's made of: `find`, `-exec`, `sh -c`, `{} +`, a loop hidden inside a loop.

**INSTRUCTOR:** Not wrong — but I can't explain `{} +`, and you couldn't change it next week. The model optimized for *correct*; my job is *learnable*. Keep its structure, drop its vocabulary.

```
$ for file in station-*.csv; do
>   echo "== $file =="
>   head -n 10 "$file" | tail -n 3 | cut -d , -f 2
> done
```

```
== station-a.csv ==
10.7
9.8
11.3
== station-b.csv ==
21.6
20.9
19.4
== station-c.csv ==
-2.7
-4.2
-3.9
```

## 0:55 — Use 2: predict first, then let it explain

**INSTRUCTOR:** Now I'll break it on purpose. Everyone write down what you think happens.

```
$ for file in station-*.csv; do head -n 10 file | tail -n 3; done
```

```
head: file: No such file or directory
head: file: No such file or directory
head: file: No such file or directory
```

```
$ echo $?
```

```
0
```

**INSTRUCTOR:** Three errors, and no `$` on `file`. Why three and not one? And the part I want you to carry away — the status says **0**. Success.

**PROMPT:**

> This loop printed `head: file: No such file or directory` three times, but `echo $?` reports 0. Explain both facts for someone who has just learned `for` loops, in under 80 words.

**LLM (plausible output):**

> The loop ran three times, once per file, so `head` failed three times — that's three errors. But the loop's exit status is the status of its last command. Your last command is `tail -n 3`, which received empty input and succeeded, so the status is 0. The missing `$` is the bug: `$file` substitutes a filename, `file` is just the word. Errors tell you; exit status can lie to you.

**INSTRUCTOR:** Good answer, and checkable: put the `$` back and the errors vanish. Notice the shape of this use — I asked *after* you predicted. Prediction first, or an explanation replaces your thinking instead of informing it.

## 1:30 — Use 3: it writes the quiz, we grade it

**PROMPT:**

> Write three one-line exit-ticket questions with answers, for students who just learned `for file in station-*.csv; do ...; done`, using exactly station-a.csv, station-b.csv, station-c.csv above.

**LLM (plausible output):**

> 1. How many lines does the loop print in total? **A:** 9.
> 2. Which printed reading is lowest, and which station is it from? **A:** -4.2 at station-c.
> 3. What changes if the glob is `station-b.csv` instead? **A:** The body runs once, for station-b only.

**INSTRUCTOR:** Question one says nine. Let's not take its word for it.

```
$ for file in station-*.csv; do echo "== $file =="; head -n 10 "$file" | tail -n 3 | cut -d , -f 2; done | wc -l
```

```
      12
```

**INSTRUCTOR:** Twelve — nine readings plus the three headers it forgot it wrote. Question one is wrong; two and three stand. Nobody copies a quiz from a machine that isn't taking its own quiz.

## 1:55 — Close

**INSTRUCTOR:** Three uses, one rule. It drafted, we critiqued. It explained, we checked. It examined, we graded. The model proposes; the shell decides.

---

## Notes for instructors

- **Different cognitive demand each time.** Critiquing a draft, interpreting an observed failure, and auditing generated answers are three separate skills. Don't reuse the same one twice and call it three uses.
- **Prompt where they can see it.** Typed prompts are part of the lesson; pasted ones look like magic and teach nothing about how to ask.
- **Pre-run every answer you plan to show.** The `head: file:` failure and the `12`-line count above are reproducible with this data; if the model drifts, cut the beat rather than accept output you haven't checked.
- **The wrong answer is the lesson.** Use 3 works because the model miscounted. If it hadn't, the shell check would still be the habit you're teaching.
- **Say the rule before they leave.** "It proposes, the shell decides" is the sentence this whole session exists to install.
- **Pacing.** About 260 spoken words is 1:50 at 140 wpm, so the typing and the model's latency have to come out of the pauses. If it runs long, drop the `./`-prefix remark in use 1 — never drop use 3's `wc -l` check.
