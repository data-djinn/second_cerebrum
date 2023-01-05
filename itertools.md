[[Python]]
# create iterators for efficient looping
==fast, memory-efficient tools that are useful by themselves or in combination==
## infinite iterators:
- `count(start, [step])` -> `start`, `start+step`, `start+2 * step`, ...
- `cycle(p)` -> `p0`, `p1`, ... `p[-1]`, `p[0]`, `p[1]`, ...
- `repeat(elem[, n])` -> `elem`, `elem`, `elem`, endlessly or up to `n` times)
## finite iterators (terminate on the shortest input)
- `accumulate(p[, func])` -> `p[0] + p[1] ... + p[-1]`
- 