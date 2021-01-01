+++
title = "Advent of Code 2019 Intro"
date = "2020-11-01T21:51:25-06:00"
author = "James K. Pringle"
authorTwitter = "" #do not include @
cover = ""
tags = ["competitive-programming", "challenge", "advent-of-code"]
keywords = ["", ""]
description = ""
showFullContent = false
+++

Today I want to write about a fun experience I had in December 2019 called [Advent of Code](https://adventofcode.com/).
This is a programmer's way to celebrate the season of Christmas by completing programming challenges. The creator, [Eric
Wastl](http://was.tl/), first ran Advent of Code in 2015, and he has repeated it every year since. I was fortunate
enough to be able to work on these challenges most nights and find solutions, and in the early morning hours of December
25, I finished the final challenge.

This is what my final Advent of Code looks like: {{< image src="/static/img/2020/advent-of-code-2019-complete.png"
alt="Advent of Code 2019" position="center" >}}

# How I got started

I was clued into it through a Python Meetup group when some members talked about joining a Slack group for it. Solving
puzzles is something I have always liked trying to do, and so I looked forward to the first puzzle being released at
midnight EST on December 1.

Each puzzle has unique input values for each "challenger." That input can be obtained only by authenticating to the
website. So an anonymous user can read the challenge description, but cannot get the challenge input or submit answers.
Each challenge has two parts and the answers to each part are just a simple integers or strings that are calculated. The
second part is only unlocked after the first part is correctly answered.

# The first challenge

The first day was a chance to clean the rust off. Here is a link to the [full description of the
challenge](https://adventofcode.com/2019/day/1), and I will repost the relevant parts here.

## Part one description

Fuel required to launch a given module is based on its mass. Specifically, to find the fuel required for a module, take
its mass, divide by three, round down, and subtract 2.

For example:

- For a mass of 12, divide by 3 and round down to get 4, then subtract 2 to get 2.
- For a mass of 14, dividing by 3 and rounding down still yields 4, so the fuel required is also 2.
- For a mass of 1969, the fuel required is 654.
- For a mass of 100756, the fuel required is 33583.

The Fuel Counter-Upper needs to know the total fuel requirement. To find it, individually calculate the fuel needed for
the mass of each module (your puzzle input), then add together all the fuel values.

## Part one solution

My puzzle input is [here](/static/data/aoc-2019/day1/input.txt), where each line is a positive number representing the
mass of an individual module. Here is a straightforward Python implementation of this challenge.

```python
def calculate_fuel(mass: int) -> int:
    return max(0, mass // 3 - 2)

with open('input.txt') as f:
    fuel_needed = 0
    for line in f:
        mass = int(line)
        fuel = calculate_fuel(mass)
        fuel_needed += fuel
    print(fuel_needed)
```

Running this code with my `input.txt` gives `3331849`. As you can see, not many lines are needed to come to an answer.

## Part two

The second part of these Advent of Code challenges are usually extensions or generalizations of the original part one
problem. For Day 1, the second part was no different.

## Part two description

Fuel itself requires fuel just like a module - take its mass, divide by three, round down, and subtract 2. However, that
fuel also requires fuel, and that fuel requires fuel, and so on. Any mass that would require negative fuel should
instead be treated as if it requires zero fuel; the remaining mass, if any, is instead handled by wishing really hard,
which has no mass and is outside the scope of this calculation.

So, for each module mass, calculate its fuel and add it to the total. Then, treat the fuel amount you just calculated as
the input mass and repeat the process, continuing until a fuel requirement is zero or negative. For example:

- A module of mass 14 requires 2 fuel. This fuel requires no further fuel (2 divided by 3 and rounded down is 0, which
  would call for a negative fuel), so the total fuel required is still just 2.
- At first, a module of mass 1969 requires 654 fuel. Then, this fuel requires 216 more fuel (654 / 3 - 2). 216 then
  requires 70 more fuel, which requires 21 fuel, which requires 5 fuel, which requires no further fuel. So, the total
fuel required for a module of mass 1969 is 654 + 216 + 70 + 21 + 5 = 966.
- The fuel required by a module of mass 100756 and its fuel is: 33583 + 11192 + 3728 + 1240 + 411 + 135 + 43 + 12 + 2 =
  50346.

## Part two solution

This is really just an extension of the original `calculate_fuel` function since we need to keep performing the same
calculation&mdash;and remembering the intermediate results&mdash;until the last result is 0. Let's modify the
`calculate_fuel` function:

```python
def calculate_fuel(mass: int) -> int:
    total_fuel = 0
    current_mass = mass
    while current_mass > 0:
        current_fuel = max(0, current_mass // 3 - 2)
        total_fuel += current_fuel
        current_mass = current_fuel
    return total_fuel
```

My part two answer is based on the same `input.txt`, and it is `4994898`.

# Final thoughts

Day 1 would be considered easy compared to other challenges. They definitely got progressively more difficult as the
days went on, but the told a story, and they built off of each other. I look forward to Advent of Code this year!
