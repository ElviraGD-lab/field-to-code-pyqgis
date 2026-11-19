---
title: "How to read this book"
---


## What you need

1. **A computer** running Windows, macOS or Linux.
2. **QGIS 4.2 or later.** QGIS 4.2 is the first long-term release (LTR) in the 4 series. You can download it from [qgis.org](https://qgis.org). There's no need to install Python separately: it's already built into QGIS.
3. **An internet connection.** All the data is open, and we download it as we go.

That's all. You don't need to know any Python beforehand either.

## How the book works

**One chapter is one stage of the project.** The chapters go in order, and the project grows step by step:

1. first, the municipal boundary;
2. then the power lines and the homes;
3. then the distance from homes to lines, and the time it takes help to arrive;
4. and finally, the finished tool.

**One lesson is one step.** A lesson takes about an hour: around twenty minutes of reading, and the rest of the time working in QGIS. If a step is a hard one, the chapter gets more lessons.

## How a lesson works

1. **Where we left off.** A short recap, so there's no need to flick back.
2. **What we want to find out.** The question for the lesson, what we expect to see, and how we'll know if we've got it wrong. We decide this before writing any code. We don't name any numbers in advance: only the calculation gives us those.
3. **The code.** In short pieces, each followed by a line-by-line explanation: what the line does and which mistake it protects us from.
4. **What we got.** We compare the result with what we expected. If it doesn't match, that's a result too – it's part of the work on the project. What we get decides the next step.
5. **If something goes wrong.** The mistakes we actually made, and how we found their cause.
6. **Your data.** What to change to run it for your own municipality.
7. **Lesson summary.** New concepts with explanations, the new code with a note on what it does, and the key points.
8. **Questions for an engineer.** Two or three questions to check your understanding. The answer sits right under each question: think it through first, then check.
9. **Exercises.** Three short exercises to help it stick. The answers to all of them are at the end of the book, in 'Answers to the exercises', and there's a link under every exercise.
10. **Open question.** Where the next lesson begins.

## Working with the code

**Type the code out by hand rather than copying it.** That way your hands and your head remember it faster. If the code doesn't work, compare it with the book letter by letter: the culprit is usually a missing bracket or quotation mark.

**Mistakes are normal.** Python tells us which line went wrong and what happened. From the first lesson, we learn to read these messages.

**All the code in the book is open.** You can take it, change it and use it in your work under the Apache-2.0 licence. The text of the book is published under CC BY 4.0.

## If something doesn't work

QGIS gets updated, and data on the internet sometimes moves. If code from the book stops working, or you find a mistake, let us know on the project's GitHub page (under [Issues](https://github.com/ElviraGD-lab/field-to-code-pyqgis/issues)). That way the book gets better for everyone.
