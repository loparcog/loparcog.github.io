---
title: "Regular Expression Power Hour \U0001F524"
summary: "Also known as \"String Comprehension\", \"Code Within Code\", and \"Annoying\""
date: 2023-03-31T16:32:24-05:00
draft: false
tags: ["research", "code"]
showToc: true
TocOpen: false
ShowReadingTime: true
ShowBreadCrumbs: true
UseHugoToc: true
---

*I've decided that recipes have all the fun injecting life stories into their posts and I want to change that.*

## A brief (personal) history

A regular expression, or *regex*, is a pattern defined by a series of characters to search for specific data in text. This definition is extremely basic since the application of regexes is extremely broad. If you ever needed to pull out specific pieces of a string, or certain lines of a file with a certain phrase or pattern, regex will be your best friend. Regex is also a great start for any other pattern-based parsers you might run into (like [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) for example).

Throughout my university career, I was well aware that regex existed and probably used it once or twice, but I didn't really pay it any attention past that. Things only started to get intimate was in my first programming position, where I naively took on a project to localize our entire codebase. Thanks to regex, I was able to parse through our code files programmatically to find and replace any strings with localized references, however this task was a few weeks in comparison to the few days it took to squash low-level bugs.

Now I find myself in a similar position working on a lovely open-source project [Isobar](https://github.com/ideoforms/isobar/), looking to programmatically pull comments out of Python files for documentation. I also thought this would be a good excuse to write an article on regex.

## Basics of regex

*Feel free to open up a free online regex tester like [Regex101](https://regex101.com/) to try any of the following!*

Regex patterns can change from language to language, but they will traditionally follow the pattern `/pattern/flags`. The `pattern` section will hold your query, while the `flags` will dictate settings like case insensitivity and multi-line searches. Here's an example in python:

```python
>>> import re
# Search follows the format (pattern, text to search, flags), and does not require forward slashes
>>> pattern = "[aeiou]\w"
>>> text = "Hello, World!"
# Search and print all matches
>>> re.search(pattern,text)
>>> print(re.groups())
("el", "or")
```

This pattern is looking for a vowel (`[aeiou]`) followed by any "word character", which is any letter, number, or underscore (`\w`, or `[a-zA-Z0-9_]`). So within our phrase, the pattern matches "H**el**lo, W**or**ld!". While we're at it, lets look at some basic regex syntax:

As a basis, **any character** can be put into a regex search to look for that singular character or sequence of characters, however there are some **meta characters** that will be described below. If you just want to search for one of these characters instead of using it as a meta character, you can escape it using a backslash (`\`). For example, searching for a period `.` would look like `\.` in a regex string. The following are all regex meta characters:

`. \ * + ? | ^ $ [ ] ( ) { }`

**Groups** define explicit characters to look for:

| Group Symbol  | Definition    |
|---|---|
| `[ ... ]` | Look for any characters in this group (NOTE: Meta characters don't need to be escaped in these) |
| `[^ ... ]` | Look for any characters **NOT** in this group |
| `( ... )` | Create a subexpression to isolate a specific section of your pattern and/or apply a modifier to a regex pattern (modifiers described below) |
| `( A \| B )` | Match with pattern `A` OR `B` |

**Tokens** are shorthand for capture groups and specific pieces of text:

| Token  | Definition                                                               |
|--------|--------------------------------------------------------------------------|
| `\s`   | Any whitespace including spaces, tabs, and newlines<br>`[ \t\n\r]`       |
| `\d`   | Any number, excluding any operation symbols <br>`[0-9]+`                 |
| `\w`   | Any word built from letters, numbers, and underscores<br>`[a-zA-Z0-9_]`  |
| `.`    | Any character                                                            |
| `^`    | Marks the beginning of the queried text                                  |
| `$`    | Marks the end of the queried text                                        |

**NOTE:** Any of the character tokens above can be capitalized to capture their negative group (eg. anything **but** whitespace would be `\S`)

**Modifiers** go after a token or group to dictate a quantity to look for:

| Modifier  | Definition    |
|-----------|---------------|
| `?`           | 0 or 1        |
| `*`           | 0 or more     |
| `+`           | 1 or more     |
| `{n}`       | Exactly *n*   |
| `{n,}`      | *n* or more   |
| `{n,m}`   | Between *n* and *m*   |



All of this alone will cover a large majority of your regex queries. You can put together any combination of the above to get specific pieces and quotes of any basic text.

But what if we want to push regex further? Like, for example, if we wanted to match multiple possible formats in a piece of code to pull inline text?

## The problem at hand

Each pattern class in isobar *may* have comments below it that define the class. This comment *may* include a short description, longer description, and example output:

```py
class PExample(Pattern):
    """
    PExample short description
    PExample long description which can be
    multiple
    lines

    >>> PExample(example_input)
    >>> example_output
    """
```

The main challenges here are that we need to get everything surrounded by the characters `"""` and divide it into its respective parts, *if those parts exist*, or if the class comment exists to begin with. 

String parsing around different character groups that will exist multiple times in a file and pulling out this specific information is where our basic approach starts to show its flaws. All of the tools above will still be used, but we will need more to make our queries more precise, using a dagger instead of a zweihander.

A simple way to show this is through an example. Say we have the following text to parse:

```py
class PClass1(Pattern):
    """
    Hello,
    """

class PClass2(Pattern):
    """
    World!
    """
```

How can we make sure that we're only getting the first comment? Assuming we're using the flag `s` to allow for `.` to match with newlines, some things that could be worth a try would be...

- `""".*"""`: Matches BOTH comments, from the start of `PClass1`'s to the end of `PClass2`'s
- `"""[^"]*"""`: This works, but would break if there is a quotation mark in the comment

What we're missing with our current tool set is something to see groups **before** and **after** our search, to make sure that we can stop at the first occurrence of a given series of characters. Thankfully, this is possible!

## Advanced regex

When you can't find something with basic regex, chances are there is some advanced syntax that will do exactly what you're hoping for. There are a number of different methods, wonderfully documented across the internet, these are two that I have found the most useful:

### Lazy Quantifier

Dan brought this to my attention while troubleshooting and I'm so surprised I have made it this far without having this committed to memory. A lazy quantifier would do exactly what we're looking for in our first pattern attempt above, matching the LEAST amount of characters possible. This can be done by appending a question mark (`?`) after any quantifier (`?`, `+`, or `*`).

Using this, we can edit our previous query to use a lazy modifier: `""".*?"""`, which will now only match the `PClass1` comment without continuing on! Score!

### Look Ahead/Behind

Another method which I have used constantly is a filter to look for anything before or after a given query without matching it. This is also a very useful tool to put bounds into your pattern. This can be done with the following patterns:

| Group  | Definition    |
|-----------|---------------|
| `(?=xyz)`  | Positive lookahead  |
| `(?!xyz)`  | Negative lookahead  |
| `(?<=xyz)` | Positive lookbehind  |
| `(?<!xyz)` | Negative lookbehind  |

These can help to narrow down your match to only capture the content you want, putting conditions around your searched area. You can also use this with capture groups to create interesting use cases, like a workaround lazy quantifier! 

We can replicate the previous query, replacing the lazy quantifier with a negative lookahead capture group: `"""((?!""").)*"""`. In this case, it is obviously better to use the lazy quantifier, but this leaves interesting possibilities for changing the content the lookahead/behind is looking for to different character sets for complex queries.

## Considerations

I am in no way a regex expert and this is in no way a complete guide. I have just went through a bunch of regex work and wanted a place to document some of this, as well as anything else I come across. My usual regex works are few and far between, but they also usually dive very deep, to the point of learning something new each time.

Will I remember to put it here? Let's see!



