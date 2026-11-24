---
title: "How to read this book"
---

## What we'll need

1. **A computer** running Windows, macOS or Linux.
2. **QGIS 4.2 or later.** We can download it free from qgis.org. Version 4.2 is the first long-term release in the 4 series: it will keep getting fixes and updates for several years. There's no need to install Python separately: it's already inside QGIS.
3. **An internet connection.** All the data in the book is open. We download it as we go.

That's all. We don't need to know any Python beforehand. All we need is what we can already do: work in QGIS with the mouse.

## How the book works

The book follows the same order in which our project grows.

1. **First, the prologue and the specification.** We work out what the problem is and decide exactly what our tool should be able to do. There's no code here yet.
2. **Then chapter 2, the introduction:** 'Introduction to Python' and 'Introduction to PyQGIS'. They cover only the essentials: what code is made of, and what QGIS looks like from the inside. Without them, it won't be clear later on why the code is written the way it is.
3. **Then the project chapters, from chapter 3 on.** One chapter is one stage of the work: the municipal boundary, power lines, homes, distances, travel time and so on. At the end of each chapter, we have a new result.
4. **Finally, the finished tool.** A QGIS plugin that works everything out for any municipality.

**One lesson is one step.** The steps are small. If a step turns out to be hard, we don't squeeze it; we split it into several lessons.

## How a lesson works

Every lesson has the same parts. That makes it easier to find our way: it's always clear where we are and what comes next.

1. **Where we left off.** A short recap, so there's no need to flick back.
2. **What we want to know.** The question for the lesson. Right there, we also write down what we expect to see and how we'll know if we've got it wrong. We decide this before writing any code. We don't name any numbers in advance: only the calculation will give us those.
3. **Steps.** Every step works the same way:
   - first, in words, what we want to do;
   - then a few lines of code;
   - then a walk-through of what each line does;
   - and why it's written exactly that way: where a name comes from, and where we can find it ourselves.
4. **What we found.** We compare the result with what we expected. If it doesn't match, that's a result too, and we work out why it turned out that way.
5. **If something goes wrong.** The mistakes we actually made, and how we found their cause.
6. **Your data.** What to change to run it for our own municipality.
7. **Summary.** New terms, new code and the key points.
8. **Programmer's secrets.** How to find or remember the command we need by ourselves, when the book isn't to hand.
9. **Questions for an engineer.** Two or three questions to check our understanding. The answer sits right under each question. It's best to think it through ourselves first, and then open the answer.
10. **Exercises.** Three short exercises to help it stick. The answers are at the end of the book, in 'Answers to the exercises'. There's a link under every exercise.
11. **The open question.** That's where the next lesson begins.

## Working with the code

**We type the code out by hand rather than copying it.** That way, it sticks faster: our hands remember what our heads haven't quite understood yet.

**Mistakes are normal.** If the code doesn't work, we first compare it with the book letter by letter. The culprit is usually a missing bracket, a missing quotation mark or a space at the start of a line. Python always tells us which line the error is in and what went wrong. We learn to read these messages from the first lesson.

**Keys.** The book gives the keys for Windows and Linux. On macOS, we usually press Cmd instead of Ctrl, and Option instead of Alt.

**All the code in the book is open.** We're free to take it, change it and use it in our own work under the Apache 2.0 licence. The text of the book and the figures are published under CC BY 4.0.

## If something doesn't work

QGIS gets updated, and data on the internet sometimes moves to new addresses. If code from the book stops working, or we spot a mistake, we can report it on the project's GitHub page, under Issues. The page also says what to include so the problem is easy to track down. That way, the book gets better for everyone.
