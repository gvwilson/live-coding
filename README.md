# Live Coding with AI

Re-doing the Carpentries introduction to live coding videos.

## Original Material

-   [A video introduction to live coding part 1](https://www.youtube.com/watch?v=bXxBeNkKmJE):
    how most people teach.
-   [A video introduction to live coding part 2](https://www.youtube.com/watch?v=SkPmwe_WjeY):
    how they ought to teach.

## Scripts

-   [Script without AI](script-without-ai.md)
-   [Script using AI](script-using-ai.md)

## Prompt

-   I am teaching scientists how to use the Unix shell.
-   They have learned basic commands like `cd`, `ls`, `cat`, and `cut`.
-   They understanding redirection with `>` and `<`, and pipes with `|`.
-   They understand simple globbing with `*`.
-   The next step is to teach them how to write a `for` loop in the shell.
-   The motivating example is to run a command once for each file that matches a glob pattern.
-   An example of a command to put into a loop is `head -n 10 FILENAME | tail -n 3 | cut -d , -f 2`.
-   My real aim is to show instructors how to incorporate LLMs into their teaching.
-   Write scripts (in the sense of movie scripts) for two scenarios:
    -   Scenario 1: teaching `for` loops via live coding *without* using LLMs.
    -   Scenario 2: teaching `for` loops via live coding *with* LLMs.
    -   The second script should incorporate LLMs in at least three pedagogically different ways.
-   Each script must describe:
    -   what the instructor says,
    -   what she commands she runs,
    -   how she prompts the LLM (if it is being used), and
    -   what its output might plausibly be.
-   Save the scripts as `script-without-ai.md` and `script-using-ai.md`.
-   Each variation of the lesson should be at most two minutes long.
-   Each lesson must process the same data files.
-   Create three short, simple CSV files for the command(s) to run on.
