# Live coding `for` loops — without an LLM

-   Running time: 2:00
-   Audience: scientists who know `cd`, `ls`, `cat`, `cut`, `<`, `>`, `|`, and `*` globbing.
-   Props: terminal at large font, three files already in the working directory. No slides.
-   Data (identical in both scripts): `station-a.csv`, `station-b.csv`, `station-c.csv`.
    Each has a header line `date,temperature_c` and twelve daily readings, so
    `head -n 10 FILE | tail -n 3 | cut -d , -f 2` prints the 7th, 8th, and 9th readings.

---

## 0:00 — Start from what they own

**INSTRUCTOR:** Three weather stations, one file each, same format. Last week you pulled three readings out of one file with this line.

```
$ head -n 10 station-a.csv | tail -n 3 | cut -d , -f 2
```

```
10.7
9.8
11.3
```

## 0:15 — Make the pain visible

**INSTRUCTOR:** Now I want station-b as well. I could just retype it.

```
$ head -n 10 station-b.csv | tail -n 3 | cut -d , -f 2
```

```
21.6
20.9
19.4
```

**INSTRUCTOR:** That's the whole problem: one more line per file. With thirty stations I'd make a typo by station twelve. The fix uses nothing you don't already know.

## 0:35 — The loop with an empty body

```
$ for file in station-*.csv; do echo $file; done
```

```
station-a.csv
station-b.csv
station-c.csv
```

**INSTRUCTOR:** Read it as English: *for each file in the files matching `station-*.csv`, do this.* Two things: the `*` is your own globbing, which the shell turns into a list of three names **before** the loop starts; and `file` is a name I made up, holding one filename per trip around.

## 0:55 — Put the old command inside

**INSTRUCTOR:** Now the trick: the body is the command you already own, with the filename replaced by the variable. Quotes are cheap insurance against a name with a space.

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

**INSTRUCTOR:** Three files, four lines of code, and the pipeline in the middle is untouched. That's the whole idea: a loop is not a new language, it's a way to reuse a line you already trust.

## 1:35 — One prediction before we stop

**INSTRUCTOR:** Don't run anything. Station-c is in the mountains, so its readings are negative. Why doesn't the minus sign break `cut`?

**STUDENT:** Because `cut` splits on commas — it never looks at whether the field is a number. `-2.7` is just text to it.

**INSTRUCTOR:** Right. `cut` doesn't know what a number is, which is why it also works on station names and dates.

## 1:52 — Close

**INSTRUCTOR:** Next time: the same loop over `*.csv`, or over `*.dat`. The loop doesn't change. The pattern does.

---

## Notes for instructors

- **Order matters.** Empty body first, then fill it. Students who see the finished loop first read it as one opaque line and can't say which part does what.
- **Say "before the loop starts" out loud.** The glob expanding once, up front, is the concept most often missing afterwards; it explains why creating a file mid-loop won't add it.
- **The prediction at 1:35 is the assessment.** It costs fifteen seconds and tells you whether they think of `cut` as a text tool or a number tool.
- **Deliberately unmagical.** Every token in the final loop (`for`, `in`, `*`, `$`, `|`) has been seen before this lesson.
- **Pacing.** About 250 spoken words is 1:45 at 140 wpm, leaving ~15 seconds for typing. If you speak slower, cut the 1:35 prediction beat — never cut the loop-building beats.
