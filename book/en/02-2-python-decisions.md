---
title: "Lesson 2.2: Python makes decisions and repeats work: conditions, loops, functions and modules"
---

## Where we left off

In lesson 2.1, we learnt to store values: numbers, text, variables, lists and dictionaries. For example, the list of line voltages `voltages = [20, 45, 400]` and the dictionary with the municipality's details.

But so far, Python only remembers what we've given it. It doesn't decide anything or repeat anything by itself. And our project has thousands of buildings and dozens of lines, and for each of them we need to check something.

## What we want to know

The question for this lesson: **how do we tell Python 'if this is so, do that' and 'do the same thing for each one'? And how do we gather that work into a single command, so we don't have to write it out again every time?**

There are four things for this. We'll go through them in order, from the simplest to the most complex:

1. **a condition**: doing something only if a rule is met;
2. **a loop**: repeating the same thing for each value in a list;
3. **a function**: a command of our own, into which we gather the work ourselves;
4. **a module**: a ready-made set of commands that we bring in.

How will we know if we've got it wrong? If Python gives an answer we didn't expect, or shows an error. That's why, at the end, we'll look at how to read error messages.

## Step 1: Comparison: yes or no

Before deciding anything, Python has to be able to ask a question: 'yes or no?'. For example: 'is the voltage above 1 kV?'.

We remember from lesson 1.2 that high voltage is anything above 1 kV. Let's ask Python:

```python
voltage = 400
voltage > 1
```

Python answers: `True`.

And for a 0.4 kV line, a low-voltage one:

```python
voltage = 0.4
voltage > 1
```

The answer is `False`.

`True` means 'yes, that's right', and `False` means 'no, that's not right'. There are no other answers to a question like this. A value like this is called a **Boolean** value, and in Python its type is `bool`.

The comparison signs:

| Sign | What it means |
|---|---|
| `>` | greater than |
| `<` | less than |
| `>=` | greater than or equal to |
| `<=` | less than or equal to |
| `==` | equal to |
| `!=` | not equal to |

**It's easy to slip up here.** Equality in Python is two signs: `==`. We already know the single `=` sign from lesson 2.1: it means 'store under a name'. Let's compare:

- `code = 33033`: **put** the number under the name `code`;
- `code == 33033`: **ask** whether `code` is equal to the number 33033. The answer is `True` or `False`.

## Step 2: A condition: if this is so, do that

Now let's use that answer to make a decision. We'll write: if the voltage is high, print one thing; otherwise, print another.

```python
voltage = 0.4
if voltage > 1:
    print("high voltage")
else:
    print("low voltage")
```

The answer is `low voltage`.

Let's go through each part.

- `if` means 'if'. After it comes a question whose answer is `True` or `False`.
- The **colon** `:` at the end of the line is required. It tells Python: 'what comes next is what to do'.
- The next line is moved **four spaces** to the right. This shift is called an **indent**. It shows which lines belong to the condition. Other languages use brackets for this, but Python uses only the indent. So in Python, indents aren't decoration: they're part of the meaning.
- `print(...)` is a function that shows a value in the console. In lesson 2.1, the console showed the answer by itself. Inside a condition, it doesn't, so we ask for it explicitly: `print`.
- `else` means 'otherwise'. This part runs when the answer to the question is `False`. It has a colon too, and there's an indent under it again.

**How to type this in the console.** After the first line with a colon, the console shows `...` instead of `>>>`. That means: 'I'm waiting for more'. We type the other lines with their indents. At the end, we press Enter once more, on an empty line, and everything runs.

Let's change `voltage = 0.4` to `voltage = 400` and run it again. The answer is `high voltage`. The code is the same, but the decision is different, because the value has changed.

## Step 3: A loop: the same thing for each one

Now we don't have one line, but a list. We want to look at each voltage in turn.

```python
voltages = [20, 0.4, 400]
for voltage in voltages:
    print(voltage)
```

The answer is three lines: `20`, `0.4`, `400`.

Let's go through it:

- `for voltage in voltages:` reads almost like an ordinary sentence: 'for each `voltage` in `voltages`'.
- On each pass, Python puts the next value from the list into `voltage`. On the first pass it's 20, on the second 0.4, on the third 400.
- The indented line runs on each pass.
- Repetition like this is called a **loop**. One pass through it is an **iteration**.

This is where the agreement from lesson 2.1 about singular and plural comes in handy. `voltages` is the whole list, and `voltage` is a single value on the current pass. The line `for voltage in voltages` explains for itself what's happening.

## Step 4: A loop and a condition together

Let's put the two steps together. We'll count how many high-voltage lines there are in the list.

First, let's think about how we'd do it by hand. We take a sheet of paper and write '0'. We go down the list. If a line is high voltage, we cross out the number and write one more. At the end, the answer is on the sheet.

The code does the same thing:

```python
count = 0
for voltage in voltages:
    if voltage > 1:
        count = count + 1
count
```

The answer is `2`.

Let's go through it:

- `count = 0` is our sheet of paper, with a zero on it for now. A variable like this is called a **counter**.
- The loop goes through each voltage.
- The condition sits **inside** the loop, so it has an indent, and the line under it has **two** indents, eight spaces. The indents show what's inside what: the condition is inside the loop, and the adding is inside the condition.
- We read `count = count + 1` from right to left: 'take what's in `count` now, add 1 and put it back under the same name'.
- The last line, `count`, has no indent: it's already outside the loop. We type it after an empty line, once the loop has finished.

Let's check the answer by hand: 20, yes; 0.4, no; 400, yes. Two lines. As expected.

## Step 5: A function: a command of our own

We'll need the rule 'above 1 kV is high voltage' many times: for lines from different sources, for checking the data, in the tool itself. We could write `voltage > 1` every time. But if the rule ever changes, we'll have to hunt for it all over the code.

It's better to write the rule down once and give it a name. That's what a **function** is: a command of our own.

```python
def is_high_voltage(voltage):
    return voltage > 1
```

Python doesn't answer: so far, we've only described the command; we haven't run it. Now let's run it:

```python
is_high_voltage(400)
is_high_voltage(0.4)
```

The answers are `True` and `False`.

Let's go through the first part:

- `def` comes from 'define'. This is how a function description starts.
- `is_high_voltage` is the function's name. It reads like a question: 'is it high voltage?'. Functions that answer 'yes' or 'no' are often given names starting with `is_`.
- `(voltage)`: in the brackets is a **parameter**. It's what the function receives as input. Inside the function, it's called `voltage`.
- The colon and the indent are the same as in a condition and a loop.
- `return` means 'give back'. The function hands back the answer to the question `voltage > 1`.

And the second part:

- `is_high_voltage(400)` is a **call** to the function: its name, and a value in brackets. Python puts 400 into the parameter `voltage`, runs the function's lines and returns the answer.

It's the same kind of command as `len(voltages)` and `print(...)` in the last lesson. It's just that `len` and `print` already exist in Python, while we wrote `is_high_voltage` ourselves.

Now let's use the function in our counter:

```python
count = 0
for voltage in voltages:
    if is_high_voltage(voltage):
        count = count + 1
count
```

The answer is the same: `2`. But now the rule lives in one place. If the threshold changes, we change one line in the function.

## Step 6: A module: ready-made sets of commands

We don't have to write everything ourselves. Python has ready-made sets of commands for different tasks. A set like this is called a **module**. To use one, we have to bring it in with the `import` command.

For example, the `math` module is for mathematics:

```python
import math
math.pi
math.sqrt(16)
```

The answers are `3.141592653589793` and `4.0`.

Let's go through it:

- `import math` means 'bring me the `math` module'. Without this line, Python doesn't know what `math` is.
- `math.pi` is the number π from the `math` module. The dot means 'inside': the `pi` that sits in `math`.
- `math.sqrt(16)` is the function `sqrt` (from 'square root') from the `math` module. The square root of 16 is 4.

QGIS itself is a set of modules too. Everything we'll do with layers is commands from QGIS modules. For example, the Processing tools sit in a module that's simply called `processing`. We'll look at how the QGIS modules work in the next lessons.

## Step 7: How to read an error message

There'll be errors, and that's normal. What matters is being able to read them. Let's make a mistake on purpose: we'll ask for the fourth value from a list that only has three.

```python
voltages[3]
```

Python answers something like this:

```text
Traceback (most recent call last):
  File "<input>", line 1, in <module>
IndexError: list index out of range
```

It looks frightening, but it's easy to read. **We read it from the bottom up.**

1. **The last line is the most important.** `IndexError` is the kind of error: something's wrong with a number. After the colon comes the explanation: 'list index out of range'. In other words, there's no such number.
2. **Above it is where it happened:** `line 1`, the first line of what we ran. When the code gets longer, this number will tell us which line to look at.
3. **The first line**, `Traceback (most recent call last)`, means 'the path to the error, with the last step at the bottom'. We can skip it.

So we asked for a number that doesn't exist. The list has three values, numbered 0, 1 and 2. We correct it to `voltages[2]`.

## What we found

Let's compare this with the question for the lesson. Now we can:

| What we need | How we write it | Example |
|---|---|---|
| ask a 'yes or no' question | a comparison sign | `voltage > 1` → `True` |
| do something only if it's true | `if … :` and an indent | `if voltage > 1:` |
| do something for each one | `for … in … :` and an indent | `for voltage in voltages:` |
| a command of our own | `def name(parameter):` and `return` | `is_high_voltage(400)` |
| bring in ready-made commands | `import` | `import math` |

And we know that we read an error message from the last line.

Let's look at the counter from step 5 again. It's already a real, if small, calculation. Instead of three numbers, there could be a thousand lines, and the code wouldn't change. That's how we'll count lines, buildings and travel time in the project chapters.

## If something goes wrong

- **`SyntaxError: expected ':'`.** We forgot the colon at the end of a line with `if`, `else`, `for` or `def`.
- **`IndentationError: expected an indented block`.** There's no indent after a line with a colon. We add four spaces.
- **`IndentationError: unexpected indent`.** There's an indent where there shouldn't be one. For example, an extra space at the start of an ordinary line.
- **`SyntaxError` in a line with `if`.** Often, the assignment `=` has been written instead of the comparison `==`.
- **The function doesn't return anything.** If we call it and get nothing back, we check whether it has a `return`. Without one, the function returns `None`: 'nothing'.
- **`NameError: name 'math' is not defined`.** We forgot `import math`.

## Your data

If another country sets the high-voltage threshold differently, one line changes: the one inside the `is_high_voltage` function. Everything else stays as it is. That's exactly why we put the rule into a function.

## Summary

**New terms**

- **Boolean value:** `True` (yes) or `False` (no). *Example:* `400 > 1` → `True`.
- **Condition:** lines that run only if the answer is `True`. *Example:* `if voltage > 1:`.
- **Indent:** four spaces at the start of a line; it shows that the line is inside a condition, a loop or a function. *Example:* `    print("high voltage")`.
- **Loop:** repeating the same lines for each value. *Example:* `for voltage in voltages:`.
- **Counter:** a variable that goes up by 1 in a loop. *Example:* `count = count + 1`.
- **Function:** a command of our own, with a name, parameters and an answer. *Example:* `def is_high_voltage(voltage):`.
- **Parameter:** what a function receives as input. *Example:* `voltage` in `is_high_voltage(voltage)`.
- **Module:** a ready-made set of commands, brought in with `import`. *Example:* `import math`.

**New code**

```python
def is_high_voltage(voltage):
    return voltage > 1

voltages = [20, 0.4, 400]
count = 0
for voltage in voltages:
    if is_high_voltage(voltage):
        count = count + 1
count

import math
math.pi
```

**Key points**

- `=` puts a value under a name; `==` asks 'is it equal?'.
- After `if`, `else`, `for` and `def` comes a colon, and under them an indent.
- The indents show what's inside what.
- A rule we need many times, we write down once: in a function.
- We read an error from the bottom up: the last line says what happened.

## Programmer's secrets

1. **Forgotten what a function does?** `help(len)` or `help(math.sqrt)` shows its description right in the console, without the internet.
2. **What's inside a module?** `dir(math)` shows a list of everything in the `math` module. That way, we can find the command we need even if we don't remember its exact name.
3. **We check a loop by hand.** Before we trust a loop's answer, we go through two or three values on paper, as in step 4. If the paper gives the same as Python, the logic is right.

## Questions for an engineer

1. **Why are indents so important in Python?**

:::{dropdown} Answer
An indent is the only way to tell Python which lines belong to a condition, a loop or a function. An extra or missing indent changes the meaning: a line may run once instead of on every pass, or not run at all.
:::

2. **Why write an `is_high_voltage` function, if we can simply write `voltage > 1`?**

:::{dropdown} Answer
The rule lives in one place and has a clear name. If the threshold changes, we change one line, instead of hunting for `> 1` all over the code. And the code is easier to read: `if is_high_voltage(voltage)` tells us straight away what we're checking.
:::

3. **In the counter from step 4, the last line, `count`, has no indent. What happens if we indent it inside the loop?**

:::{dropdown} Answer
It will run on every pass, and we'll see the counter's intermediate values, not just the total. So a line that should run after the loop is written without an indent.
:::

## Exercises

1. **Recall.** What's the difference between `=` and `==`? [Answer](#en-sol-2-2-1)

2. **Fix it.** There are two mistakes in this loop. Let's find them: [Answer](#en-sol-2-2-2)

   ```python
   for voltage in voltages
   print(voltage)
   ```

3. **Back to lesson 1.1.** Who are the three users of our tool? [Answer](#en-sol-2-2-3)

## The open question

Now we can write simple calculations in Python. But our data isn't lists of numbers; it's QGIS layers: boundaries, lines, buildings. What does QGIS look like from the inside, to Python? What are a layer, a feature and its fields in code? That's where lesson 2.3 begins.

## Notes and sources

1. Python Software Foundation, *The Python Tutorial*, section 4, 'More Control Flow Tools': https://docs.python.org/3/tutorial/controlflow.html.
2. Python Software Foundation, *The Python Tutorial*, section 6, 'Modules': https://docs.python.org/3/tutorial/modules.html.
3. Python Software Foundation, *The Python Tutorial*, section 8, 'Errors and Exceptions': https://docs.python.org/3/tutorial/errors.html.
