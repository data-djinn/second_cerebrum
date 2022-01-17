[[Azure (WIP)]] [[Data Engineering]]
# Session
 - group events that arrive at similar times, & filter out periods of time where there is no data
 - windows are constantly being evaluated based on when they enter/exit the window
 - Maximum window size is 7 days
 - ![[Pasted image 20211013183134.png]]
# Tumbling
- repeating
- **non-overlapping**
- events cannot belong to more than one tumbling window
- ![[Pasted image 20211013183155.png]]
# Hopping
- Hop forward in time by a fixed period
- windows can overlap
- max window is 7 days as well
- `GROUP BY X, HoppingWindow(Duration(minute , 10), Hop(minute,10), Offset(millisecond, -1))`
- ![[Pasted image 20211013183309.png]]
# Sliding
- output produced *only* when an even occurs
- every window has at least one event
- windows continuously move forward by an epsilon
GROUP BY X, SlidingWindow(minute , 10)
- ![[Pasted image 20211013183316.png]]