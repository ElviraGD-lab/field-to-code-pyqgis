---
title: "Lesson 2.1: Our first lines of Python: numbers, text, variables, lists and dictionaries"
---

## Where we left off

The specification is ready. We know what we're building and how we'll check it. The next step in the plan is to load the municipal boundary with code.

But to write code, we need to know what it's made of. That's why chapter 2 is an introduction. In this lesson, we get to grips with the very first building blocks of Python.

## What we want to know

The question for this lesson: **how does Python store the things we work with – numbers, names, sets of values – and how do we get at them?**

Everything we do from here on rests on five things: numbers, text, variables, lists and dictionaries. Once they're clear, we'll be able to read any code in the book piece by piece.

How will we know if we've got it wrong? If Python gives an answer we didn't expect, or shows an error. Either way, it's a clue to where we've misunderstood something.

## Step 1: Where to write code

We'll write code right inside QGIS. It already has a place where Python runs lines straight away. It's called the **Python Console**.

We open it from the **Plugins → Python Console** menu, or with Ctrl+Alt+P. A panel appears at the bottom of the window. Its bottom line shows the sign `>>>`. This is the prompt: Python is waiting for us to type something.

We type a line after `>>>` and press Enter. Python runs it and shows the answer straight away on the line below.

Let's start with the simplest thing there is:

```python
400 + 20
```

Python answers: `420`.

Nothing special, it seems – just a calculator. But it shows the main idea: **we wrote a line, Python ran it and answered.** That's how all work in the console goes.

## Step 2: Numbers

Let's see how Python does sums.

```python
400 - 20
400 * 2
400 / 20
```

We type each line separately and press Enter. The answers are `380`, `800` and `20.0`.

Let's go through them:

- `-` is subtraction, `*` is multiplication, `/` is division.
- Multiplication is written with an asterisk, not a cross or a dot: that's the convention in almost every programming language.
- The last answer is `20.0`, not `20`. Division in Python always gives a **decimal number**, even when it divides exactly.

This shows us that Python has two kinds of numbers:

1. **Whole numbers**: `400`, `20`, `33033`. In English, they're called integers, and in Python their type is `int`.
2. **Decimal numbers**: `20.0`, `0.6`, `318.4`. In Python, their type is `float`. The name comes from 'floating point'.

**An important detail for us.** In Spanish documents and tables, the decimal part is separated by a comma: 0,6. But in Python, it's separated only by a **point**: `0.6`. What happens if we write a comma?

```python
0,6
```

Python doesn't show an error. It answers `(0, 6)`. It has misunderstood us: it has decided these are two separate numbers, 0 and 6. That's what makes it dangerous: there's no error, but the result is wrong. **So in code, the decimal part always goes after a point.**

## Step 3: Text

As well as numbers, we need names: the name of a municipality, the type of a line, the name of a layer. In Python, text is written in quotation marks:

```python
"Lena"
```

Python answers: `'Lena'`.

Text in quotation marks like this is called a **string**. In Python, its type is `str`. The quotation marks tell Python: 'this isn't a command, it's just text, so don't try to run it'.

We can use double quotation marks, `"Lena"`, or single ones, `'Lena'`: to Python, they're the same. Python itself shows strings in single quotation marks. What matters is opening and closing a string with the same kind.

Let's compare two lines:

```python
"400"
400
```

The first is text made of three characters: 4, 0, 0. The second is a number. They look alike, but to Python they're different things. We can do sums with a number, but not with text. Let's check:

```python
"400" + "20"
```

The answer is `'40020'`. Python didn't add up numbers; it glued two pieces of text together. **So we always look at what we're holding: a number or text.** This will come in handy in chapter 5: in open data, a line's voltage is often stored as text, and we'll have to turn it into a number.

## Step 4: Variables

So far, we've been typing the values afresh every time. But in real work, we need the same value many times. For example, the code of the municipality of Lena is 33033. We'll need it for the boundary, for the buildings and for the report.

So we don't have to type it every time, we give the value a name:

```python
code = 33033
```

Python doesn't answer. That's just as it should be: we didn't ask anything; we only stored a value. Now let's ask:

```python
code
```

The answer is `33033`.

This is a **variable**: a name under which Python remembers a value. Let's take apart the line `code = 33033`:

- on the left, the name: `code`;
- the `=` sign means 'store under this name'. It isn't 'equals', as in maths, but **assignment**: putting a value under a name;
- on the right, the value: `33033`.

It's easiest to read it from right to left: 'we put the number 33033 under the name `code`'.

Now we can work with variables in the same way as with values:

```python
voltage = 400
voltage / 20
```

The answer is `20.0`.

**Why are the names in English?** Code all over the world is written in English, and all the names in QGIS are English too. That way, any engineer in any country can read our code. In the book, we always name a variable after what's in it: `code` for a code, `voltage` for a voltage, `name` for a name.

A few rules for names:

- small Latin letters, no spaces;
- if there are several words, we join them with an underscore: `municipality_name`;
- a name can't start with a digit;
- capital and small letters are different: to Python, `Code` and `code` are two different names.

## Step 5: Strings with values filled in

We often need to build a piece of text out of parts: for example, the label 'Municipality: Lena, code 33033'. Python has a handy way of writing this:

```python
name = "Lena"
f"Municipality: {name}, code {code}"
```

The answer is `'Municipality: Lena, code 33033'`.

Let's go through it:

- the letter `f` before the quotation marks tells Python: there'll be values filled in in this string;
- the curly brackets `{name}` and `{code}` hold the names of variables. Python puts their values in their place.

A string like this is called an **f-string**, from the word 'format'. We'll see it in chapter 3, when we put together the address of the service with the municipal boundaries.

## Step 6: Lists

Now let's imagine we have several values, not just one. For example, the voltages of the lines found in a municipality: 20, 45 and 400 kV. We could set up three variables, but that's awkward. It's better to put them together:

```python
voltages = [20, 45, 400]
```

This is a **list**: several values in order, in square brackets, separated by commas.

Let's note the name: `voltages`, in the plural. One voltage is `voltage`; a list of voltages is `voltages`. This isn't a Python rule, but an agreement for people: the name shows straight away where there's one value and where there's a set.

How do we take a single value from a list? By its number:

```python
voltages[0]
```

The answer is `20`.

Why 0 and not 1? **In Python, counting starts from zero.** The first value is number 0, the second is number 1, the third is number 2. It feels odd at first, so it's worth remembering straight away. A value's number in a list is called its **index**.

```python
voltages[2]
voltages[-1]
```

Both times, the answer is `400`. Index `2` is the third value. And `-1` means 'the last one': we count from the end. That's handy when we don't know how many values there are in the list.

And how many are there? Let's ask:

```python
len(voltages)
```

The answer is `3`. `len` comes from 'length'. It's a **function**: a ready-made Python command. Functions are written like this: the name, then, in round brackets, what to work with. There's more about functions in the next lesson.

## Step 7: Dictionaries

A list is good when the values all mean the same kind of thing – all voltages, for example. But sometimes a single object has several different properties. A municipality has a name, a code and a margin around its boundary in metres. We could put them in a list: `["Lena", 33033, 1000]`. But a week from now, nobody will remember what the third number means.

That's what a **dictionary** is for. Each value in it sits under its own name:

```python
municipality = {"name": "Lena", "code": 33033, "margin_m": 1000}
```

Let's go through it:

- a dictionary is written in **curly** brackets;
- inside, there are pairs separated by commas: a name, a colon, a value;
- the name in a pair is called a **key**. The keys here are strings, so they're in quotation marks.

How do we get a value out? By its key, in square brackets:

```python
municipality["code"]
```

The answer is `33033`.

It's like the attribute table in QGIS. A feature has fields, and each field holds a value. A dictionary is the same thing: the key is like the field name, with the value next to it.

We'll need dictionaries very soon. In chapter 3, when we run Processing tools from code, all their parameters are passed as a dictionary: 'the input layer is this one', 'the buffer distance is so much'.

## What we found

Let's compare this with the question for the lesson. Now we know how Python stores the things we work with:

| What we store | How we write it | Example |
|---|---|---|
| a whole number | just digits | `33033` |
| a decimal number | with a **point** | `0.6` |
| text | in quotation marks | `"Lena"` |
| a value under a name | name `=` value | `code = 33033` |
| several values in order | in square brackets | `[20, 45, 400]` |
| named properties | in curly brackets, key: value | `{"code": 33033}` |

And we know how to get a value out: from a list by its number, starting from zero, and from a dictionary by its key.

## If something goes wrong

- **The answer is `(0, 6)` instead of a number.** There's a comma in the decimal number. We change it to a point: `0.6`.
- **`SyntaxError: unterminated string literal`.** A string has been opened with a quotation mark but not closed: `"Lena`. We add the second quotation mark. The English words in the error mean 'the string isn't finished'.
- **`NameError: name 'code' is not defined`.** Python doesn't know that name. Either we haven't run the line `code = 33033` yet, or we've got a letter wrong, or the console has been restarted and everything has been forgotten. We run the line with `=` again.
- **`IndexError: list index out of range`.** We asked for a number that doesn't exist: for example, `voltages[3]` when there are three values (numbers 0, 1, 2).
- **`KeyError: 'Code'`.** There's no such key in the dictionary. Often, it's a capital letter: the key is `"code"`, but we wrote `"Code"`.

We always read an error message **from the last line**: that's where it says what the error is and what it's about.

## Your data

For our own municipality, we replace the name and the code in the dictionary: the municipality's code is in the open data from national statistics. For Spanish municipalities, it's the INE code.

## Summary

**New terms**

- **Python Console:** the place in QGIS where Python runs a line straight away. *Example:* `400 + 20` → `420`.
- **Whole and decimal numbers:** `int` and `float`; the decimal part goes after a point. *Example:* `33033`, `0.6`.
- **String:** text in quotation marks. *Example:* `"Lena"`.
- **Variable:** a name under which Python remembers a value. *Example:* `code = 33033`.
- **f-string:** a string with values filled in. *Example:* `f"code {code}"`.
- **List:** values in order in square brackets; numbering starts from 0. *Example:* `voltages[0]`.
- **Dictionary:** 'key: value' pairs in curly brackets. *Example:* `municipality["code"]`.
- **Function:** a ready-made command; a name and round brackets. *Example:* `len(voltages)`.

**New code**

```python
code = 33033
name = "Lena"
f"Municipality: {name}, code {code}"
voltages = [20, 45, 400]
voltages[0]
len(voltages)
municipality = {"name": "Lena", "code": 33033, "margin_m": 1000}
municipality["code"]
```

**Key points**

- The `=` sign means 'store under this name'.
- A number and text are different things: `400` and `"400"`.
- The decimal part goes only after a point.
- In a list, counting starts from zero.
- A variable's name says what's in it; a list's name is in the plural.

## Programmer's secrets

1. **Not sure what we're holding?** Let's ask Python: `type(voltage)`. It will answer `int`, `float`, `str`, `list` or `dict`. It's the quickest way to check whether we have a number or text.
2. **No need to type a line again.** In the console, the ↑ arrow key brings back earlier lines. Spotted a mistake? We press ↑, fix the one letter and press Enter.
3. **Which brackets?** A simple tip helps: round `()` run a function, square `[]` make a list or 'get by number or key', and curly `{}` make a dictionary or fill in a value in an f-string.

## Questions for an engineer

1. **Why does Python answer `(0, 6)` to `0,6`, and why is that worse than an error?**

:::{dropdown} Answer
To Python, a comma separates values, so it sees two numbers, 0 and 6. It doesn't show an error, and the code carries on with the wrong value. We'd notice an error straight away, but a wrong value can go unnoticed all the way to the result.
:::

2. **Why use a dictionary, if we can put the same three values in a list, `["Lena", 33033, 1000]`?**

:::{dropdown} Answer
In a list, the values differ only by their number, and a week from now nobody will remember what the third number means. In a dictionary, every value has a name: `"margin_m"` tells us straight away that it's the margin in metres.
:::

3. **Why do we call the list `voltages` and not `voltage`?**

:::{dropdown} Answer
The name should show what's inside. The singular is one value; the plural is a set of values. Python doesn't care, but it makes the code easier for us to read.
:::

## Exercises

1. **Recall.** Which brackets create a list, and which create a dictionary? [Answer](#en-sol-2-1-1)

2. **Fix it.** There's a mistake in this code. Let's find it: [Answer](#en-sol-2-1-2)

   ```python
   margin_km = 1,5
   ```

3. **Back to lesson 1.2.** Which three numbers do we store for each building so we can compare homes near lines of different voltages? [Answer](#en-sol-2-1-3)

## The open question

We can store values. But code isn't only about storing. We need Python to make decisions by itself ('if the voltage is above 1 kV, we include the line') and to repeat the work for each building by itself. How do we write that? That's where lesson 2.2 begins.

## Notes and sources

1. Python Software Foundation, *The Python Tutorial*, section 3, 'An Informal Introduction to Python': https://docs.python.org/3/tutorial/introduction.html.
2. QGIS Documentation, 'Python Console': https://docs.qgis.org/latest/en/docs/user_manual/plugins/python_console.html.
