---
title: "From Field to Code: PyQGIS from Scratch"
---

**Building a real tool and learning to think like an engineer**

This book is for engineers who have spent years in the field with survey instruments. We know cartography and coordinate reference systems, and we can work in QGIS with the mouse: open a layer, make a buffer, put a map together.

But we've never written a line of code. Python feels like something far removed from us, as if it were meant for programmers, not for people like us.

The book is written by an engineer just like us, who is learning from scratch too. We don't have ready answers to everything. We go together, step by step, and work things out as we go.

## What we learn

**Python and PyQGIS, right from the beginning.**

- **Python** is a programming language. QGIS understands it without our having to install anything separately.
- **PyQGIS** is a way of controlling QGIS through Python. Everything we do with the mouse, we can do with code: load a layer, find a feature, build a buffer. The difference is that we can run the code again, on another municipality, and it does everything by itself.

We don't learn everything there is in Python and PyQGIS. Only the basics our project needs – but slowly, clearly and all the way through.

The other books in the series take their Python and PyQGIS from this one. That's why here we explain them from scratch, and in the books that follow, we simply use them.

## The problem we're solving

In the mountains in winter, storms bring down power lines. Trees and snow block the roads. The power goes off, and help can't get there quickly.

That gives us our question: **which homes stand close to overhead power lines, and how many minutes does it take fire crews to reach them, by day and by night?**

We answer it for one municipality in Asturias, and then we build a tool that works out the same thing for any municipality. By the end, we'll have a QGIS plugin.

## How we work

1. **One real problem, from start to finish.** Each new piece of Python comes in at the moment the problem needs it.
2. **Think first, then calculate.** Before the code, we decide what we want to get. After the code, we check whether we can trust the result.
3. **Code in short pieces.** We go through every line: what it does and why it's there.
4. **We don't hide mistakes.** If something doesn't work, we show how we found the cause. That's part of the work too.

We can simply read the book, and even then it'll be clear what each line does. But it's better to have QGIS open alongside and repeat every step.

We start with the lesson ['How to read this book'](how-to-read.md).

*This book is being written now. Lessons are published as they're ready.*
