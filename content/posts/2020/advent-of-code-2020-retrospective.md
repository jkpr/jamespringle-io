+++
title = "Advent of Code 2020 Retrospective"
date = "2020-12-25T06:08:21-06:00"
author = "James K. Pringle"
authorTwitter = "" #do not include @
cover = ""
tags = ["advent-of-code", "numpy", "python"]
keywords = ["", ""]
description = ""
showFullContent = false
+++

In life, the most memorable times are when there are consistency and purpose. Advent of Code 2020 has now come and gone, but while it was here I did have some semblance of consistency and purpose. It was a fun set of 25 challenges starting at 11 PM each night (I'm in the U.S. Central Timezone). These programming challenges made the Christmas season that much more special.

{{< figure src="/static/img/2020/advent-of-code-2020-complete.png" alt="Advent of Code 2020 calendar" position="center" style="border-radius: 8px;" caption="The complete Advent of Code 2020 calendar." >}}

# The source code

This year, as opposed to last year, I maintained a Github repo with my solutions in Python. That repo can be found [here][1]. I kept things tidy by following a common pattern each day:

1. Copy the code template for that day. All solutions are in a path like `advent/dayN/__init__.py`.
2. Solve the two problem parts, then refactor. I made variable names neater, specific to the domain. Less like `x` and `y` and more like [`password`][2a] and [`card_key`][2b]. I extracted common chunks of code into new routines.
3. Run `black` for formatting and `pylint` for catching issues like unused imports and variables.
4. Add notes to the repository's README file of interesting things in the code for that day.

[1]: https://github.com/jkpr/advent-of-code-2020
[2a]: https://adventofcode.com/2020/day/2
[2b]: https://adventofcode.com/2020/day/25

Maintaining this repo was a decent amount of work above and beyond solving the coding challenges. But it paid off. The notes on each day's solution quickly became a useful resource for solving later Advent of Code challenges.

Some days I came up with an alternate solution. While the original solution is what I naturally could come up with in the moment, the alternate solution was my chance to slow down, read the docs, and try something new. I learned a lot about Numpy and array manipulations in my [Day 16: Ticket Translation][3a] and [Day 17: Conway Cubes][3b] alternate solutions. I also took my first foray into the Abtract Syntax Tree with my [Day 18: Operation Order][3c] alternate solution.

[3a]: https://github.com/jkpr/advent-of-code-2020/blob/master/advent/day16/alternate/__init__.py
[3b]: https://github.com/jkpr/advent-of-code-2020/blob/master/advent/day17/alternate/__init__.py
[3c]: https://github.com/jkpr/advent-of-code-2020/blob/master/advent/day18/alternate/__init__.py

# Difficulty

Which were the hardest problems? To answer that question, we can look at the [leaderboard][leaderboard] to see how long it took for the first 100 people to solve both parts each day. The most difficult days are shown in this figure:

[leaderboard]: https://adventofcode.com/2020/leaderboard

{{< image src="/static/img/2020/aoc-2020-times.png" alt="Leaderboard times" position="center" style="border-radius: 8px;" >}}

The most difficult problem for me (aligning with the community) was [Day 20: Jurassic Jigsaw][6a]. The norm is one step per part, but this one had multiple steps in the second part. This problem was the only one I used OOP to solve, and it was by far the most lines of source code.

The second most difficult for me (aligning again with the community) was [Day 23: Crab Cups][6b]. The difficulty was in choosing the correct data structures because we were working on data with 1,000,000 elements and performing a set of operations on those data 10,000,000 times.

[6a]: https://adventofcode.com/2020/day/20
[6b]: https://adventofcode.com/2020/day/23

# Observations

Some general observations:

- Three problems involved variations on Conway's Game of Life. See Day John Conway is a legend in mathematics and puzzles, and he passed away this year from complications due to Covid-19. Rest in peace. See [Day 11: Seating System][4a], [Day 17: Conway Cubes][4b], and [Day 24: Lobby Layout][4c]. 
- Three problems had to do with passwords / encryption / decryption / encoding / decoding. See [Day 2: Password Philosophy][5a], [Day 9: Encoding Error][5b], and [Day 25: Combo Breaker][5c].
- None of the problems built off of each other. Last year, for example, there was the `intcode` compiler, and multiple challenges added new functionality, while other challenges used the compiler to run `intcode` programs.
- There were no maze problems.

[4a]: https://adventofcode.com/2020/day/11
[4b]: https://adventofcode.com/2020/day/17
[4c]: https://adventofcode.com/2020/day/24
[5a]: https://adventofcode.com/2020/day/2
[5b]: https://adventofcode.com/2020/day/9
[5c]: https://adventofcode.com/2020/day/25

# Conclusion

Thank you, Eric Wastl (see his [presentation on creating Advent of Code][wastl]), for continuing to make Advent of Code 🙏! I became an AoC++ member (I donated) to show my appreciation. I learned several new and useful things about Python, little tips and tricks along with trying out new packages like Numpy. I detailed these in repo's README. Anyone who is interested in programming challenges should get started. The old challenges back to 2015 are completely open and available.

[wastl]: https://www.youtube.com/watch?v=bS9882S0ZHs