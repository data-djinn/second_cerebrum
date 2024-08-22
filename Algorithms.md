#interview #algorithms #data-structures
# Arrays & Strings
- strings are 1-dimensional arrays
![[Pasted image 20240728223146.png]]

## Two pointers
- two integer variables that both move along an iterable
- " Start the pointers at the edges of the input. Move them towards each other until they meet."
- will never have more than *O(n)* iterations
- examples:
```python
def check_if_palindrome(s):
    left = 0
    right = len(s) - 1

    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    
    return True


def check_for_target(nums, target):
    left = 0
    right = len(nums) - 1

    while left < right:
        # curr is the current sum
        curr = nums[left] + nums[right]
        if curr == target:
            return True
        if curr > target:
            right -= 1
        else:
            left += 1
    
    return False
```

- " Move along both inputs simultaneously until all elements have been checked."
```python
def combine(arr1, arr2):
    # ans is the answer
    ans = []
    i = j = 0
    while i < len(arr1) and j < len(arr2):
        if arr1[i] < arr2[j]:
            ans.append(arr1[i])
            i += 1
        else:
            ans.append(arr2[j])
            j += 1
    
    while i < len(arr1):
        ans.append(arr1[i])
        i += 1
    
    while j < len(arr2):
        ans.append(arr2[j])
        j += 1
    
    return ans
```
- this has *O(n)* time complexity & *O(1)* space complexity
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        i = j = 0
        while i < len(s) and j < len(t):
            if s[i] == t[j]:
                i += 1
            j += 1

        return i == len(s)
```

## sliding window
##### subarray
- given an array, a **subarray** is a contiguous section of the array
- all the elements must be adjacent to eachother in the original array & in their original order
- **can be defined by 2 indeces, start & end**
	- For example, with `[1, 2, 3, 4]`, the subarray `[2, 3]` has a starting index of `1` and an ending index of `2`. Let's call the starting index the **left bound** and the ending index the **right bound**. Another name for subarray in this context is "window"
##### when to use sliding window?
- **First**, the problem will either explicitly or implicitly define criteria that make a subarray "valid". There are 2 components regarding what makes a subarray valid:
1. A **constraint metric**. This is some attribute of a subarray. It could be the sum, the number of unique elements, the frequency of a specific element, or any other attribute.
2. A **numeric restriction** on the constraint metric. This is what the constraint metric should be for a subarray to be considered valid.
- e.g. a subarray is valid if it has a sum less than or equal to `10`. 
	- The constraint metric here is the sum of the subarray
	- the numeric restriction is `<= 10`

**Second**, the problem will ask you to find valid subarrays in some way.
1. The most common task you will see is finding the **best** valid subarray. The problem will define what makes a subarray **better** than another
	- For example, a problem might ask you to find the **longest** valid subarray
2. Another common task is finding the number of valid subarrays

# # Sliding window algorithm
- only consider valid subarrays
- maintain 2 variables, `left=0` & `right=0`
- expand the size of our window by incrementing `right`
- if, after expansion, our window becomes invalid (fails constraint metric/numeric restriction), we remove elements from the left of the subarray by incrementing `left`
##### Implementation
- example constraint: sum
	- keep track of current sum with variable `curr`
	- when adding a new element from the right, `curr += nums[right]`
	- when removing an element from the left, `curr -= nums[left]`
	- **All these operations are O(1)**
- use `for` loop to iterate `right` over the input
- use while loop to check `curr > 10`
	- if true, shrink window by incrementing `left`
```
function fn(nums, k):
    left = 0
    curr = 0
    answer = 0
    for (int right = 0; right < nums.length; right++):
        curr += nums[right]
        while (curr > k):
            curr -= nums[left]
            left++

        answer = max(answer, right - left + 1)

    return answ
```
##### Efficient!
- if the logic done for each window is *O(1)*, sliding window algorithms run in *O(n)*, which is **much** faster.
```python
def find_length(nums, k):
    # curr is the current sum of the window
    left = curr = ans = 0
    for right in range(len(nums)):
        curr += nums[right]
        while curr > k:
            curr -= nums[left]
            left += 1
        ans = max(ans, right - left + 1)  # compare len(window) with previous answer, for each iteration
    
    return ans
```

Another example:
> Example 2: You are given a binary string `s` (a string containing only `"0"` and `"1"`). You may choose up to one `"0"` and flip it to a `"1"`. What is the length of the longest substring achievable that contains only `"1"`?
> 
> For example, given `s = "1101100111"`, the answer is `5`. If you perform the flip at index `2`, the string becomes `1111100111`.

- Because the string can only contain `"1"` and `"0"`, another way to look at this problem is "what is the longest substring that contains **at most one** `"0"`?".
- This makes it easy for us to solve with a sliding window where our condition is `window.count("0") <= 1`
- We can use an integer `curr` that keeps track of how many `"0"` we currently have in our window.
```python
def find_length(s):
    # curr is the current number of zeros in the window
    left = curr = ans = 0 
    for right in range(len(s)):
        if s[right] == "0":
            curr += 1
        while curr > 1:
            if s[left] == "0":
                curr -= 1
            left += 1
        ans = max(ans, right - left + 1)
    
    return ans
```
#### Number of subarrays (that fit some constraint)
- "how many valid windows **end** at index `right`?"
- `(left, right)`, then `(left + 1, right)`, `(left + 2, right)`, and so on until `(right, right)` (only the element at `right`)
- `right - left +1`
> Example 1: Given an array of positive integers `nums` and an integer `k`, find the length of the longest subarray whose sum is less than or equal to `k`
```python
def find_length(nums, k):
    # curr is the current sum of the window
    left = curr = ans = 0
    for right in range(len(nums)):
        curr += nums[right]
        while curr > k:
            curr -= nums[left]
            left += 1
        ans = max(ans, right - left + 1)
    
    return ans
```

>Example 2: You are given a binary string `s` (a string containing only `"0"` and `"1"`). You may choose up to one `"0"` and flip it to a `"1"`. What is the length of the longest substring achievable that contains only `"1"`?
```python
def find_length(s):
    # curr is the current number of zeros in the window
    left = curr = ans = 0 
    for right in range(len(s)):
        if s[right] == "0":
            curr += 1
        while curr > 1:
            if s[left] == "0":
                curr -= 1
            left += 1
        ans = max(ans, right - left + 1)
    
    return ans
```

### Fixed Window Size
- easy because the difference between any two adjacent windows is only 2 elements
	- we add one element on the right & remove one element on the left to maintain the length
- build the first window: `i = 0, j = contraint - 1)
- now we have an array of length `constraint`
- to add value to the end of array, we must remove `j - k = 0`
> Example 4: Given an integer array `nums` and an integer `k`, find the sum of the subarray with the largest sum whose length is `k`.

```
function fn(arr, k):
    curr = some data to track the window

    // build the first window
    for (int i = 0; i < k; i++)
        Do something with curr or other variables to build first window

    ans = answer variable, probably equal to curr here depending on the problem
    for (int i = k; i < arr.length; i++)
        Add arr[i] to window
        Remove arr[i - k] from window
        Update ans

    return ans
``` 
```python
def find_best_subarray(nums, k):
	curr = 0
	for i in range(k):
		curr += nums[i]
	
	ans = curr
	for i in range(k, len(nums)):
		curr += nums[i] - nums[i - k]
		ans = max(ans, curr)
	
	return ans
```
## Prefix sum
- use for sums of subarray in arrays of numbers
- create an array `prefix` where `prefix[i]` is the sum of all elements up to index `i` (*inclusive*)
	- e.g. given `nums = [5, 2, 1, 6, 3, 8]`, we would have `prefix = [5, 7, 8, 14, 17, 25]`
- allows us to find the sum of any subarray in O(1) time
- If we want the sum of the subarray from `i` to `j` (inclusive), then the answer is `prefix[j] - prefix[i - 1]`, 
	- or `prefix[j] - prefix[i] + nums[i]` if you don't want to deal with the out of bounds case when `i = 0`.
- `prefix[i - 1]` is the sum of all elements **before** index `i`
	- When you subtract this from the sum of all elements up to index `j`, you are left with the sum of all elements starting at index `i` and ending at index `j`
```
Given an array nums,

prefix = [nums[0]]
for (int i = 1; i < nums.length; i++)
    prefix.append(nums[i] + prefix[prefix.length - 1])
```

> > Example 1: Given an integer array `nums`, an array `queries` where `queries[i] = [x, y]` and an integer `limit`, return a boolean array that represents the answer to each query. A query is `true` if the sum of the subarray from `x` to `y` is less than `limit`, or `false` otherwise.
> 
> For example, given `nums = [1, 6, 3, 2, 7, 2]`, `queries = [[0, 3], [2, 5], [2, 4]]`, and `limit = 13`, the answer is `[true, false, true]`. For each query, the subarray sums are `[12, 14, 12]`.

```python
def answer_queries(nums, queries, limit):
	# building preprocessing array
    prefix = [nums[0]]
    for i in range(1, len(nums)):
		# below would error out if i = 0, hence above
        prefix.append(nums[i] + prefix[-1])
    
    ans = []
    for x, y in queries:
        curr = prefix[y] - prefix[x] + nums[x]
        ans.append(curr < limit)

    return ans
```

> > Example 2: [2270. Number of Ways to Split Array](https://leetcode.com/problems/number-of-ways-to-split-array/)
> 
> Given an integer array `nums`, find the number of ways to split the array into two parts so that the first section has a sum greater than or equal to the sum of the second section. The second section should have at least one number.
```python
class Solution:
    def waysToSplitArray(self, nums: List[int]) -> int:
        n = len(nums)
        
        prefix = [nums[0]]
        for i in range(1, n):
            prefix.append(nums[i] + prefix[-1])

        ans = 0
        for i in range(n - 1):
            left_section = prefix[i]
            right_section = prefix[-1] - prefix[i]
            if left_section >= right_section:
                ans += 1

        return ans
```

## String building
- in most languages, concatenating a single character to a string is O(n)
	- this is because strings are immutable
- The operations needed at each step would be `1 + 2 + 3 + ... + n`. This is the partial sum of [this series](https://en.wikipedia.org/wiki/1_%2B_2_%2B_3_%2B_4_%2B_%E2%8B%AF#Partial_sums), which leads to O(n^2) operations.
1. declare a list
2. when building the string, add characters to the list 
	1. O(1)) per operation, O(n) total
3. once finished, convert the list to a string using `"".join(list)`
	1. O(n)

## subarrays/substrings, subsequences, and subsets
##### subarrays/substrings
- constraints:
	- sum greater than / less that `k`
	- limits on what is contained, such as the maximum of `k` unique elements or no dupes allowed
- metric:
	- minimum/maximum length
	- number of subarrays/substrings
	- max/minimum sum
**USE A SLIDING WINDOW**

- If a problem's input is an integer array & you need to calculate multiple subarray sums, use a prefix sum
	- size of a subarray between `i` & `j` is `j - i + 1`
		- this is also the number of subarrays that end at j, starting at `i` or later

##### Subsequences
> A subsequence is a set of elements of an array/string that keeps the same relative order but doesn't need to be contiguous.
> 
> For example, subsequences of `[1, 2, 3, 4]` include: `[1, 3]`, `[4]`, `[]`, `[2, 3]`, but not `[3, 2]`, `[5]`, `[4, 1]`.

- two pointers may be applicable
##### Subsets
- compared to subsequence, the order matters

# Hashing
- Hash maps & sets are implemented using [hashing](https://en.wikipedia.org/wiki/Hash_function).
- a ==hash function takes an input & deterministically conerts it to an integer that is less that a fixed size==
	- inputs are callled **keys**
	- the same input will always be converted to the same integer
- arrays have O(n) random access - access & update values in constant time
	- main constraint is that their size is fixed and the indices must be integers
	- Because hash functions can convert any input to an integer, we can effectively remove the constraint of indices needing to be integers
- when a hash function is combined with an array, it creates a **hash map / hash table / dictionary**
- with arrays, we map indices to values
- with hashmaps, we map **keys** to values (key: value pair)
	- a key can be almost anything
- the only constraint on a hash map's key is that it has to be **immutable**
#### advantages
- add & remove, check existence in O(1) time (vs O(n) time via arrays)
- same time complexity (O(1)) as arrays for:
	- finding length/number of elements
	- updating values
	- iterate over elements
##### disadvantages
- can be slower with smaller input sizes due to overhead
- take up more space
	- resizing a dynamic hash table is much more expensive because every existing key needs to be rehashed
	- a hash table may also use an array that is significantly larger than the number of elements stored, resulting in a huge waste of space

### Collisions
- when different keys convert to the same integer, it is called a collision
- without handling collisions, older keys will get overwritten & their data lost
- **chaining** is a common way to handle collisions
	- use link lists inside the hash map's array instead of the elements themselves
	- the linked list nodes store both the key & the value
	- if there are collisions, the collided key: value pairs are linked together in a linked list
		- then, when trying to access one of these key: value pairs, we traverse through the linked list until the key matches

## Sets
- similar to hash maps, without mapped values corresponding to the keys
- useful for checking if an element exists
- add, remove, and check existence in O(1)
- does not track frequency - writing same value 100 times to a set essentially discards 99 writes

- to use array as key, convert mutable array into immutable tuple as key
	- another way is to convert array into a delimited string

## Checking for existence
- using a hash map / set can improve this operation from O(n^2) to O(n)
> > Example 1: [1. Two Sum](https://leetcode.com/problems/two-sum/)
> 
> Given an array of integers `nums` and an integer `target`, return indices of two numbers such that they add up to `target`. You cannot use the same index twice.

- brute force approach would be using a nested loop to iterate over every pair of indices and check if the sum is equal to `target`
	- time complexity on O(n^2)
	- first for loop focuses on a number `num` and a second, nested loop looks for `target - num`
	- with an array, looking for `target - num` is O(n)
- with a hashmap it's O(1)
- first build a hash map as we iterate along the array, mapping it's value to it's index
	- at each index `i`, where `num = nums[i]`, we can check our hash map for `target - num`
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        dic = {}
        for i in range(len(nums)):
            num = nums[i]
            complement = target - num
            if complement in dic: # This operation is O(1)!
                return [i, dic[complement]]
            
            dic[num] = i
        
        return [-1, -1]
```
-  If the question wanted us to return a boolean indicating if a pair exists or to return the numbers themselves, then we could just use a set
	- however, since it wants the indices of the numbers, we need to use a hash map to "remember" what indices the numbers are at.
## Counting
- tracking the frequency of things
- mapping keys to integers
> Example 1: You are given a string `s` and an integer `k`. Find the length of the longest substring that contains **at most** `k` distinct characters.
> 
> For example, given `s = "eceba"` and `k = 2`, return `3`. The longest substring with at most `2` distinct characters is `"ece"`.

- This problem deals with substrings and has a constraint on the substrings (at most `k` distinct characters)
	- These characteristics let us know that we should consider sliding window.
- use hash map `counts` to keep count of the characters in the window
	- map letters to their frequency
	- length (number of keys) in `counts` at any time is the number of distinct characters
	- when we move from the left, decrement the frequency of the elements being removed
	- when frequency becomes 0, character is no longer part of the window, & we delete the key
```python
from collections import defaultdict

def find_longest_substring(s, k):
    counts = defaultdict(int)
    left = ans = 0
    for right in range(len(s)):
        counts[s[right]] += 1
        while len(counts) > k:
            counts[s[left]] -= 1
            if counts[s[left]] == 0:
                del counts[s[left]]
            left += 1
        
        ans = max(ans, right - left + 1)
    
    return ans
```
# Linked List
- a node is an element with more information than just one piece of data like an integer or string
	- e.g. an array stores it's value & it's index
- a linked list is similar to an array
- it stores data in an ordered manner, but is implemented using node objects
	- need a custom class that defines the node object
- each node has a "next" pointer, which points to the node representing the next element in the sequence
```python
class ListNode:
    def __init__(self, val):
        self.val = val
        self.next = None
    
one = ListNode(1)
two = ListNode(2)
three = ListNode(3)
one.next = two
two.next = three
head = one

print(head.val)
print(head.next.val)
print(head.next.next.val)
```
- node `one` is the **head**
##### advantages
- you can add & remove elements at any position in O(1)
	- you need to have a reference to a node at the position in which you want to perform the addition/removal
		- otherwise the operation is O(n) because you need to iterate starting from the `head` until you get to the desired position
- doesn't have a fixed size (no resizing needed)
	- while dynamic arrays can be resized, under the hood they are still allocated a fixed size
		- when this size is exceeded, the array is resized (expensive)

##### Disadvantages
- no random access
	- if you have a large linked list and want to access the 150,000th element, there usually isn't a better way than to start at the head & iterate 150,000 times
	- so while array has O(1) indexing, a linked list could require O(n) to access an element
- every element needs to have extra storage for the pointers
	- if you're only storing small items like booleans or characters, you may be more than doubling the space needed

### Mechanics
- when you assign a pointer to an existing linked list node, the pointer refers to the object in memory
- let's say you have a node `head`
```python
ptr = head
head = head.next
head = None
```
- after all this, `ptr` still refers to the original `head` node, even though the `head` variable changed
	- variables remain at nodes unless they are modified directly
	- `ptr = something` is the only way to modify `ptr`
#### Chaining `.next`
- if you have multiple `.next`, for example `head.next.next`, everything before the final `.next` refers to one node
- for example, given a linked list `1 -> 2 -> 3`, if you have `head` pointing at the first node, and you do `head.next.next`, you are actually referring to `2.next`, because `head.next` is the `2`
#### Traversal
- iterating forward through a linked list can be done with a simple loop
```python
def get_sum(head):
    ans = 0
    while head:
        ans += head.val
        head = head.next
    
    return ans
```
- the final node's `next` pointer is `None`, so `head` becomes `None` and the `while` loop ends
- equivalent of iterating to the next element in an array
- can be done recursively:
```python
def get_sum(head):
    if not head:
        return 0
    
    return head.val + get_sum(head.next)
```
## Types of linked lists
### Singly linked list
- only points to the **next node**
- this means you can *only move forward* when iterating
- need a pointer at `i - 1` if you want to add or remove `i`:
```python
class ListNode:
    def __init__(self, val):
        self.val = val
        self.next = None

# Let prev_node be the node at position i - 1
def add_node(prev_node, node_to_add):
	# prev_node was pointing to the tail node - insert into index i-1
    node_to_add.next = prev_node.next
    prev_node.next = node_to_add

# Let prev_node be the node at position i - 1
def delete_node(prev_node):
	# prev_node.next is deleted
    prev_node.next = prev_node.next.next # node at i is garbage-collected
```
 
As mentioned before, when you have a reference to the node at `i - 1`, then insertion and deletion is O(1)O(1). However, without that reference, you need to obtain the reference by iterating from the head, which for an **arbitrary** position is O(n).

## Doubly linked list
- node contains pointers to previous & next node
- allows iteration in both directions
- only need reference to node at `i` to insert/delete
	- this is because we can simply reference the `prev` pointer of that node to get the node at `i - 1`
	- then do the exact same operations as above
```python
class ListNode:
    def __init__(self, val):
        self.val = val
        self.next = None
        self.prev = None

# Let node be the node at position i
def add_node(node, node_to_add):
    prev_node = node.prev
    node_to_add.next = node
    node_to_add.prev = prev_node
    prev_node.next = node_to_add
    node.prev = node_to_add

# Let node be the node at position i
def delete_node(node):
    prev_node = node.prev
    next_node = node.next
    prev_node.next = next_node
    next_node.prev = prev_node
```

## linked list with sentinel nodes
- sentinel nodes sit at the start & end of linked lists
	- used to make operations & the code needed to execute those operations cleaner
- even when there are no noes in a linked list, you still keep pointers to a `head` and `tail`
- the real head of the linked list is `head.next` and the real tail is `tail.prev`
- i.e. the sentinel nodes themselves are not part of our linked list
>The previous code we looked at is prone to errors. For example, if we are trying to delete the last node in the list, then `nextNode` will be `null`, and trying to access `nextNode.next` would result in an error. With sentinel nodes, we don't need to worry about this scenario because the last node's `next` points to the sentinel tail.

- sentinel nodes also allow us to easily add and remove from the front or back of the linked list
- addition & removal is only O(1) if we have a reference to the node at the position we are performing the operation on
- with sentinel tail node, we can perform operations at the end of the list in O(1)
```python
class ListNode:
    def __init__(self, val):
        self.val = val
        self.next = None
        self.prev = None

def add_to_end(node_to_add):
    node_to_add.next = tail
    node_to_add.prev = tail.prev
    tail.prev.next = node_to_add
    tail.prev = node_to_add

def remove_from_end():
    if head.next == tail:
        return

    node_to_remove = tail.prev
    node_to_remove.prev.next = tail
    tail.prev = node_to_remove.prev

def add_to_start(node_to_add):
    node_to_add.prev = head
    node_to_add.next = head.next
    head.next.prev = node_to_add
    head.next = node_to_add

def remove_from_start():
    if head.next == tail:
        return
    
    node_to_remove = head.next
    node_to_remove.next.prev = head
    head.next = node_to_remove.next

head = ListNode(None)
tail = ListNode(None)
head.next = tail
tail.prev = head
```

#### Dummy pointers
- we usually want to keep a reference to the `head` to ensure we can always access any element
- sometimes it's better to traverse using a `dummy` variable & keep `head` at the head
```python
def get_sum(head):
    ans = 0
    dummy = head
    while dummy:
        ans += dummy.val
        dummy = dummy.next
    
    # same as before, but we still have a pointer at the head
    return ans
```

## Fast & slow pointers
- implementation of the 2-pointers technique
- move 2 pointers at different "speeds"
	- usually the "fast" pointer moves 2 nodes per iteration, whereas the "slow" pointer moves one node per iteration
```python
// head is the head node of a linked list
function fn(head):
    slow = head
    fast = head

    while fast and fast.next:
        Do something here
        slow = slow.next
        fast = fast.next.next
```

- The reason we need the while condition to also check for `fast.next` is because if `fast` is at the final node, then `fast.next` is null, and trying to access `fast.next.next` would result in an error (you would be doing `null.next`)

> Example 1: Given the head of a linked list with an **odd** number of nodes `head`, return the value of the node in the middle.
> 
> For example, given a linked list that represents `1 -> 2 -> 3 -> 4 -> 5`, return `3`

- converting to an "array" would be basically cheating & not acceptable answer
- difficulty is that we don't know how long the linked list is
- we can use a fast & slow pointer solution
	- one pointer move's twice as fast as the other
	- by the time the fast pointer reaches the end, the slow pointer will be halfway through since it is moving at half the speed
```python
def get_middle(head):
    slow = head
    fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    
    return slow.val
```

- the pointers use O(1) space
- if there are *n* nodes in the linked list, the time complexity is *O(n)* for the traversals

> Example 2: [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
> 
> Given the `head` of a linked list, determine if the linked list has a cycle.
> 
> There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer.

- a cyce is a group of nodes forming a circle
	- traversal never ends as it moves around that circle infinitely
- best approach is to use a fast & slow pointer
	- imagine a straight racetrack
		- if 2 runners of significantly different speeds are racing, the slow runner will never catch up the faster one.
		- the fast runner finishing the race is like the fast pointer reaching the end of the linked list
	- if the racetrack was instead circular, the fast racer would eventually pass the slow runner
	- likewise, if a fast & slow pointer ever meet, we know there must be a cycle
> Why will the pointers always meet, and the fast pointer won't just "skip" over the slow pointer in the cycle? After looping around the cycle for the first time, if the fast pointer is one position behind, then the pointers will meet on the next iteration. If the fast pointer is two positions behind, then it will be one position behind on the next iteration. This pattern continues - after looping around once, the fast pointer moves exactly one step closer to the slow pointer at each iteration, so it's impossible for it to "skip" over.

```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        slow = head
        fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            if slow == fast:
                return True

        return False
```
- time complexity of *O(n)* and space complexity of *O(1)*
- you could also use hashing, but that would take *O(n)* space:
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        seen = set()
        while head:
            if head in seen:
                return True
            seen.add(head)
            head = head.next
        return False
```

> Example 3: Given the head of a linked list and an integer `k`, return the kthkth node from the end.
> 
> For example, given the linked list that represents `1 -> 2 -> 3 -> 4 -> 5` and `k = 2`, return the node with value `4`, as it is the 2nd node from the end.

- separate the two pointers by a gap of `k`, and then move them at the same speed
	- they will always be `k` apart
	- when the fast pointer (the one further ahead) reaches the end, then the pointer must be at the desired node, since it's `k` behind
```python
def find_node(head, k):
    slow = head
    fast = head
    for _ in range(k):
        fast = fast.next
    
    while fast:
        slow = slow.next
        fast = fast.next
    
    return slow
```
- *O(n)* time complexity, *O(1)* space complexity

## Reversing a linked list
 >Imagine that we have a linked list `1 -> 2 -> 3 -> 4`, and we want to return `4 -> 3 -> 2 -> 1`. Let's say we keep a pointer `curr` that represents the current node we are at. Starting with `curr` at the `1`, we need to get the `2` to point to `curr`. The problem is, once we iterate (`curr = curr.next`) to get to the `2`, we no longer have a pointer to the `1` because it is a singly linked list.
- we must use another pointer `prev`
- at any given node, we set `curr.next = prev` to switch the direction of the arrow
- then, we update `prev` to be `curr`
- however, if we change `curr.next`, we lose that next node
- to fix this, we use a temp variable `next_node` to point to the next node before changing any of the other pointers
```python
def reverse_list(head):
    prev = None
    curr = head
    while curr:
        next_node = curr.next # first, make sure we don't lose the next node
        curr.next = prev      # reverse the direction of the pointer
        prev = curr           # set the current node to prev for the next node
        curr = next_node      # move on
        
    return prev
```
- *O(n)* time complexit, *O(1)* space complexity

> Given the `head` of a linked list, swap every pair of nodes. For example, given a linked list `1 -> 2 -> 3 -> 4 -> 5 -> 6`, return a linked list `2 -> 1 -> 4 -> 3 -> 6 -> 5`.

1. Starting with `head` at node `A`, we need node `B` to point here.
    
    - We can accomplish this by doing `head.next.next = head`
2. However, if we change `B.next`, we will lose access to the rest of the list.
    
    - Before applying the change in step 1, save a pointer `nextNode = head.next.next`.

> head.next.next is used differently in steps 1 and 2. When it is before the assignment operator (=), it is **changing** head.next's next node. When it is after the assignment, it is **referring** to head.next's next node.

1. We now have `B` pointing at `A`. We need to move on to the next pair `C, D`. However, `A` is still pointing at `B`, which isn't what we want. If we move on to the next pair immediately, we will lose a reference to `A`, and won't be able to change `A.next`.
    
    - Save `A` in another pointer with `prev = head` (we haven't changed `head` yet so it's still pointing at `A`).
    - To move to the next pair, do `head = nextNode`.
2. Once we move on to the next pair `C -> D`, we need `A` to point to `D`.
    
    - Now that `head` is at `C`, and `prev` is at `A`, we can do `prev.next = head.next`.
3. The first pair `A, B` is fully completed. `B` points to `A` and `A` points to `D`. When we started, we had `head` pointing to `A`. After going through steps 1 - 4, we completed `A, B`. Right now, we have `head` pointing to `C`. If we go through the steps again, we will have complete `C, D`, and be ready for the next pair. We can just repeat steps 1 - 4 until all pairs are swapped. But what do we return at the end?
    
    - Once all the pairs are finished, we need to return `B`. Unfortunately, we lost the reference to `B` a long time ago.
    - We can fix this by saving `B` in a `dummy` node before starting the algorithm.
4. What if there is an odd number of nodes? In step 4, we set `A.next` to `C.next`. What if there were only 3 nodes, so `C.next` was null?
    
    - Before moving on to the next pair, set `head.next = nextNode`. This is setting `A.next` to `C`.
    - Note that this effect will be overridden by step 4 in the next swap if there is still a pair of nodes remaining.
    - Since in step 2 we do `head.next.next`, we need our while loop condition to check for both `head` and `head.next`. That means if there is only one node left in the list, the while loop will end after the current iteration. As such, this effect wouldn't be overridden.
    - For example, consider the list `A -> B -> C -> D`. At some point, we have `B <-> A C -> D`. Here, we perform step 6, and we get `B -> A -> C -> D`. When we start swapping the pair `C, D`, step 4 will set `A.next` to `D`, which overrides what we just did with step 6. But if `D` didn't exist, then the iteration would have just ended. In that scenario, we would have `B -> A -> C`, which is what we want.

```python
class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        # Check edge case: linked list has 0 or 1 nodes, just return
        if not head or not head.next:
            return head

        dummy = head.next               # Step 5
        prev = None                     # Initialize for step 3
        while head and head.next:
            if prev:
                prev.next = head.next   # Step 4
            prev = head                 # Step 3

            next_node = head.next.next  # Step 2
            head.next.next = head       # Step 1

            head.next = next_node       # Step 6
            head = next_node            # Move to next pair (Step 3)

        return dummy
```

> [2130. Maximum Twin Sum of a Linked List](https://leetcode.com/problems/maximum-twin-sum-of-a-linked-list/) asks for the maximum pair sum. The pairs are the first and last node, second and second last node, third and third last node, etc.
- 1. Find the middle of the linked list using the fast and slow pointer technique from the previous article.
2. Once at the middle of the linked list, perform a reversal. Basically, reverse only the second half of the list.
3. After reversing the second half, every node is spaced `n / 2` apart from its pair node, where `n` is the number of nodes in the list which we can find from step 1.
4. With that in mind, create another fast pointer `n / 2` ahead of `slow`. Now, just iterate `n / 2` times from `head` to find every pair sum `slow.val + fast.val`.

# Stacks & Queues
- a stack is an ordered collection of elements where elements are only added & removed from the same end
	- in the physical world, an example of a stack would be a stack of plates in a kitchen - you can only add or remove plates from the top of the pile
	- the history of your current browser's tab is a stack
- LIFO queue (last in, first out)
- any dynamic array can implement a stack
- inserting into a stack is called **pushing**
- removing from a stack is called **popping**
- stacks usually have a **peek** method which looks at the top of the stack without popping
- time complexity of stack operations is dependent on the implementation
	- dynamic array stacks have the same time complexity of regular dynamic arrays
		- **O(1) push, pop, & random access**
		- **O(n) search**
==a stack is a good option whenever you can recognize a LIFO pattern==
- typically, elements in the input interact together
	- e.g. matching elements together, querying some property such as "how far is the next largest element", evaluating a mathematical equation given as a string

## String problems
- Normally, string questions that can utilize a stack will involve iterating over the string and putting characters into the stack, and comparing the top of the stack with the current character at each iteration.
- Stacks are useful for string matching because it saves a "history" of the previous characters
> Example 1: [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)
> 
> Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid. The string is valid if all open brackets are closed by the same type of closing bracket in the correct order, and each closing bracket closes exactly one open bracket.
> 
> For example, `s = "({})"` and `s = "(){}[]"` are valid, but `s = "(]"` and `s = "({)}"` are not valid.
```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        matching = {"(": ")", "[": "]", "{": "}"}
        
        for c in s:
            if c in matching: # if c is an opening bracket
                stack.append(c)  # amortized O(1)
            else:
                if not stack:
                    return False
                
                previous_opening = stack.pop()
                if matching[previous_opening] != c:
                    return False
 
        return not stack
```
- using a hash map to store the frequency of any key we want allows us to solve sliding window problems that put constraints on multiple elements
- *O(n)* as long as the work done inside each loop is amortized constant
> Example 2: [2248. Intersection of Multiple Arrays](https://leetcode.com/problems/intersection-of-multiple-arrays/)
> 
> Given a 2D array `nums` that contains `n` arrays of distinct integers, return a sorted array containing all the numbers that appear in all `n` arrays.
> 
> For example, given `nums = [[3,1,2,4,5],[1,2,3,4],[3,4,5,6]]`, return `[3, 4]`. `3` and `4` are the only numbers that are in all arrays.
```python
from collections import defaultdict

class Solution:
    def intersection(self, nums: List[List[int]]) -> List[int]:
        counts = defaultdict(int)
        for arr in nums:
            for x in arr:
                counts[x] += 1

        n = len(nums)
        ans = []
        for key in counts:
            if counts[key] == n:
                ans.append(key)
        
        return sorted(ans)
```
- if we used an array, it would need to be at least as large as the maximum element
- We need to initialize an array of size `1000`, even though only a few of the indices will actually be used
	- Therefore, using an array could end up being a huge waste of space. Sure, sometimes it would be more efficient because of the overhead of a hash map, but overall, a hash map is much safer.
	- Even if `99999999999` is in the input, it doesn't matter - the hash map handles it like any other element.
- Let's say that there are nn lists and each list has an average of mm elements. To populate our hash map, it costs O(n⋅m) to iterate over all the elements. The next loop iterates over all unique elements that we encountered. If all elements are unique, this can cost up to O(n⋅m), although this won't affect our time complexity since the previous loop also cost O(n⋅m). Finally, there can be at most mm elements inside `ans` when we perform the sort, which means in the worst case, the sort will cost O(m⋅logm). This gives us a time complexity of O(n⋅m+m⋅log⁡m)=O(m⋅(n+log⁡m)). If every element in the input is unique, then the hash map will grow to a size of n⋅m, which means the algorithm has a space complexity of O(n⋅m).

> Example 3: [1941. Check if All Characters Have Equal Number of Occurrences](https://leetcode.com/problems/check-if-all-characters-have-equal-number-of-occurrences/)
>
Given a string `s`, determine if all characters have the same frequency.
>
For example, given `s = "abacbc"`, return true. All characters appear twice. Given `s = "aaabb"`, return false. `"a"` appears 3 times, `"b"` appears 2 times. `3 != 2`

- use hash map `counts` to count all character frequencies
- iterate through `s` & get the frequency of every character
- check if all frequencies are the same
```from collections import defaultdict

class Solution:
    def areOccurrencesEqual(self, s: str) -> bool:
        counts = defaultdict(int)
        for c in s:
            counts[c] += 1
        
        frequencies = counts.values()
        return len(set(frequencies)) == 1
```
- *O(n)* to populate the hash map
- *O(n)* to convert hash map's values to a set
- time complexity of *O(n)*
### Count the number of subarrays with an "exact" constraint
> For example, "Find the number of subarrays that have a sum less than k" with an input that **only has positive numbers** would be solved with sliding window. In this section, we would be talking about questions like "Find the number of subarrays that have a sum **exactly equal** to `k`"
- use prefix sum
	- find the sum of subarrays by taking the difference between 2 prefix sums
	- any difference in the prefix sum array equal to `k` represents a subarray with a sum equal to `k` (same would be true of any operation - `/`, `*`, `-`)
- first, declare hash map `counts` that maps prefix sums to how often they occur (a number could appear multiple times in a prefix sum if the input has negative numbers
	- for example, given `nums = [1, -1, 1]` the prefix sum is `[1, 0, 1]`, and `1` appears twice
	- need to initialize `counts[0] = 1`, because empty prefix `[]` has a sum of `0`
- next declare `ans` & `curr` variables
- now, we iterate over the input - at each element, we update `curr` & maintain `counts` by incrementing the frequency of `curr` by 1
	- before updating `counts`, we must update `ans`
	- find how many valid subarrays end at current index
	- Recall that the sum of a subarray was found by taking the difference between two prefixes. If `curr - k` existed as a prefix before this point and our current prefix is `curr`, then the difference between these two prefixes is `curr - (curr - k) = k`, which is exactly what we are looking for
	- Therefore, we can increment our answer by `counts[curr - k]`. If the prefix `curr - k` occurred multiple times before (due to negative numbers), then each of those prefixes could be used as a starting point to form a subarray ending at the current index with a sum of `k`. That's why we need to track the frequency.
```python
from collections import defaultdict

class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        counts = defaultdict(int)
        counts[0] = 1
        ans = curr = 0

        for num in nums:
            curr += num
            ans += counts[curr - k]
            counts[curr] += 1
    
        return ans
```
## Queues
- Follows **First in, First Out** patttern
	- elements are added & removed from opposite sides
	- example, line at a fast food restaurant
- harder to implement than stacks while maintaining good performance
	- you could just use a dynamic array, but operations on the front of the array are *O(n)*
- adding to a queue is called **enqueue** and deletions are called **dequeue** - they should be *O(1)*
	- could just use doubly-linked list
		- if you have the pointer to a node, you can add or delete at that location in *O(1)*
		- implement by maintaining sentinel nodes 
- there is also a data structure called a **deque**: double ended queue (pronounced "deck")
- queues are primary used to implement breadth-first search "BFS"
> Example: [933. Number of Recent Calls](https://leetcode.com/problems/number-of-recent-calls/)
> 
> Implement the `RecentCounter` class. It should support `ping(int t)`, which records a call at time `t`, and then returns an integer representing the number of calls that have happened in the range `[t - 3000, t]`. Calls to `ping` will have increasing `t`.

- stream of increasing integers
- every time we add an integer to the stream, we need to find out how many numbers are in stream within `3000`
	- using an array & counting the length would be very inefficient
```python
from collections import deque

class RecentCounter:
    def __init__(self):
        self.queue = deque()

    def ping(self, t: int) -> int:
        while self.queue and self.queue[0] < t - 3000:
            self.queue.popleft()
        
        self.queue.append(t)
        return len(self.queue)


# Your RecentCounter object will be instantiated and called as such:
# obj = RecentCounter()
# param_1 = obj.ping(t)
```
## Monotonic
- ==(of a function or quantity) varying in such a way that it either never decreases or never increases==
- elements are always sorted (ascending or descending)
- maintain their sorted property by removing elements that would violate the property before adding new elements
	- `stack = [1, 5, 8, 15, 23]`. You want to push `14` onto the stack
		- To maintain the sorted property, we need to first pop the `15` and `23` before pushing the `14` - after the push operation, we have `stack = [1, 5, 8, 14]`
		- this happens in *O(n)*
- useful for finding the "next" element based on some criteria
	- e.g. find the next greater element
- also good when you have a dynamic window of elements and you want to maintain knowledge of the maximum or minimum element as the window changes
> Example 1: [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)
> 
> Given an array of integers `temperatures` that represents the daily temperatures, return an array `answer` such that `answer[i]` is the number of days you have to wait after the "ith" day to get a warmer temperature. If there is no future day that is warmer, have `answer[i] = 0` instead.
- could brute force it, *O(n^2)*
- first 5 days all share same "answer day", the 6th day
	- use this observation to improve efficiency
	- push the temperatures onto a stack, then pop them off once we find a warmer temperature
- 

# Trees & graphs
- node is a abstract data type that:
1. stores data
2. points to other nodes

### Graph: *collection of nodes & their pointers*
- linked list & trees are both types of graphs
- nodes of a graph are called *vertices*, their connecting pointers are called *edges*

### Trees
- tree is a type of graph
#### Binary trees
- start of the binary tree is called the *root*
- node points to its *children*
- root is the only node with no parent
- tree nodes cannot have more than one parent
- nodes with no children are called a **leaf** node
	- all end nodes are the "leaves" of the tree
- **depth** of a node is how far it is from the root node
	- the root has a depth of `0`
	- every child has a depth of `parents_depth + 1`
- a **subtree** of a tree is a node & all of it's children
- real life examples:
	- file systems (root dir < subfolders)
	- Reddit comments
```python
class TreeNode:
    def __init__(self, val, left, right):
        self.val = val
        self.left = left
        self.right = right
```

## Depth-first search (DFS)
- depth by traversing as far down the tree as possible in one direction, until encountering a leaf node
	- then go the other direction
	- move exclusively with `node.left` until left subtree has been fully explored

```python
def dfs(node):
    if node == None:
        return

    dfs(node.left)
    dfs(node.right)
    return
```

The good news is that the structure for performing a DFS is very similar across all problems. It goes as follows:

1. Handle the base case(s). Usually, an empty tree (`node = null`) is a base case.
2. Do some logic for the current node
3. Recursively call on the current node's children
4. Return the answer
**each function call solves and returns the answer to the original problem as if the subtree rooted at the current node was the input**

##### 3 types:
1. **preorder traversal**: logic is done on the current node before moving to the children
```python
def preorder_dfs(node):
    if not node:
        return

    print(node.val)
    preorder_dfs(node.left)
    preorder_dfs(node.right)
    return
```
2. **inorder traversal**: recursively call the left child, then perform logic on the current node, then recursively call the right child
	- this means no logic will be done untill we reach a node without a left child since calling on the left child takes priority over performing logic
	- calling on the left child takes priority over performing logic
```python
def inorder_dfs(node):
    if not node:
        return

    inorder_dfs(node.left)
    print(node.val)
    inorder_dfs(node.right)
    return
```
3. **postorder traversal**
	- recursively call on the children first and then perform logic on the current node
	- no logic will be done until we reach a leaf node since calling on the children takes priority over performing logic
	- root is the last node where logic is done
```python
def postorder_dfs(node):
    if not node:
        return

    postorder_dfs(node.left)
    postorder_dfs(node.right)
    print(node.val)
    return
```
## Breadth-first search (BFS)
- prioritize breadth over depth
- recall that a node's depth is its distance from the root
- in DFS, we tried to go down as far as we could, increasing the depth of the current node until we reached a leaf
- **In BFS, we traverse all nodes at a given depth before moving on to the next depth**
![[Pasted image 20240805102115.png]]
```python
from collections import deque

def print_all_nodes(root):
    queue = deque([root])
    while queue:
        nodes_in_current_level = len(queue)
        # do some logic here for the current level

        for _ in range(nodes_in_current_level):
            node = queue.popleft()
            
            # do some logic here on the current node
            print(node.val)

            # put the next level onto the queue
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
```
- at the start of each iteration inside the while loop, the queue contains exactly all the nodes at the current level
- then use a for loop to iterate over the current level
	- store the number of nodes in the current level `nodes_in_current_level`
	- then iterate to make sure the for loop doesn't iterate over any other nodes
- for loop visits each node in the current level and puts all the children (the next level's nodes) in the queue
- because we are removing from the left & adding on the right, after the loop finishes, the queue will hold all the nodes in the next level
- move to the next while loop & repeat
- dequeue & enqueue operations are *O(1)*
- total time complexity is *O(n * k)*, where:
	- n = total number of nodes
	- k = amount of work we do at each node (hopefully *O(1)*!)
> Example 1: [199. Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/)
> 
> Given the `root` of a binary tree, imagine yourself standing on the right side of it. Return the values of the nodes you can see ordered from top to bottom.
- essentially asking for the rightmost node at each level
- if we prioritize the left children before right children, each final value at each iteration will be the rightmost node
```python
class Solution:
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        
        ans = []
        queue = deque([root])
        
        while queue:
            current_length = len(queue)
            ans.append(queue[-1].val) # this is the rightmost node for the current level
            
            for _ in range(current_length):
                node = queue.popleft()
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
        
        return ans
```
> Example 2: [515. Find Largest Value in Each Tree Row](https://leetcode.com/problems/find-largest-value-in-each-tree-row/)
> 
> Given the `root` of a binary tree, return an array of the largest value in each row of the tree.
```python
class Solution:
    def largestValues(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        
        ans = []
        queue = deque([root])
        
        while queue:
            current_length = len(queue)
            curr_max = float("-inf") # this will store the largest value for the current level
            
            for _ in range(current_length):
                node = queue.popleft()
                curr_max = max(curr_max, node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            
            ans.append(curr_max)
        
        return ans
```
### when to use BFS over DFS?
- in DFS and BFS, the important thing is you visit all nodes
- **DFS uses less code & thus is quicker & easier to implement if using recursion**
	- you could waste a lot of time searching for a value
	- if the node was stored on the right side of the tree, at a depth of 1, DFS would search entire left subtree before finding the value on the right
	- DFS uses space linear with the height of the tree (max depth) 
- **When the problem requires us to handle nodes according to their level, we use BFS**
	- downside: if the node you're searching for is near the bottom, you will waste a lot of time searching through all the levels before reaching the bottom 
	- uses space linear with the level that has the nodes
## Binary search trees
- for each node, all values in its left subtree are less than the value in the node
- all values in its right subtree are greater than the value in the node
- all values in a BST must be unique
![[Pasted image 20240805161003.png]]
- operations like searching, adding, and removing can be done in *O(log n)* time on average, where n is the number of nodes in the tree (using **binary search**)

> Example 1: [938. Range Sum of BST](https://leetcode.com/problems/range-sum-of-bst/)
> 
> Given the `root` node of a **binary search tree** and two integers `low` and `high`, return the sum of values of all nodes with a value in the inclusive range [low, high].

- brute force approach would be to do a normal BFS or DFS, visit every node, and only return nodes whose values are between `low` and `high` to the sum
- make use of BST property to develop a more efficient algorithm
	- every node has a greater value than all nodes in the left subtree, and less than all nodes in the right subtree
	- thus if the current node's value is less that `low`, we know it is pointless to check the left subtree becaus all nodes in the left subtree will be out of the range
	- likewise, if the current node's value is greater than `high`, we don't need to check the right subtree
```python
class Solution:
    def rangeSumBST(self, root: Optional[TreeNode], low: int, high: int) -> int:
        if not root:
            return 0

        ans = 0
        if low <= root.val <= high:
            ans += root.val
        if low < root.val:
            ans += self.rangeSumBST(root.left, low, high)
        if root.val < high:
            ans += self.rangeSumBST(root.right, low, high)

        return ans
```

>> Example 3: [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/)
> 
> Given the `root` of a binary tree, determine if it is a valid BST.
- construct recursive function that takes a `node` and returns `True` if the tree rooted at `node` is a valid BST
- root node can be any value, but node to the left must be less than current node  & node to the right must be greater
	- need 2 arguments, `small` & `large`, & make sure `small < node.val < large`
	- at each node, `large` & `small` = `node.val`
	- initialize `small = float('-inf')` & `large = float('inf')`
	- all subtrees are also BSTs
		- thus given input node, we must ensure 
- base case = empty tree is technically a BST, so we return `True`
```python
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def dfs(node, small, large):
            if not node:
                return True
            
            if not (small < node.val < large):
                return False

            left = dfs(node.left, small, node.val)
            right = dfs(node.right, node.val, large)

            # tree is a BST if left and right subtrees are also BSTs
            return left and right

        return dfs(root, float("-inf"), float("inf"))
```
# Graphs
- ==any collection of nodes & connections between those nodes==
	- another term for nodes is **vertices**, and connections between the nodes are called **edges**
![[Pasted image 20240805161629.png]]
![[Pasted image 20240805161653.png]]![[Pasted image 20240805161712.png]]
- edges of a node can either be **directed or undirected**
	- directed edges mean that you can only traverse in one direction
		- i.e. can't move backwards
		- represented as arrows between nodes
	- undirected edges can traverse in both directions
		- represented as straight lines
	- in binary trees, the edges were directed
		- binary trees are *directed graphs*
- **connected compentent** of a graph is a group of nodes that are connected by edges
	- in binary trees, there must be only one connected component
- a node can have any number of edges to it
	- if we have a directed graph, it can have any number of edges leaving it and any number of edges entering it
- number of edges that can be used to reach the node is the node's **indegree**
- number of edges that can be used to leave the node is the nodes **outdegree**
- nodes that are connected by an edge are called **neighbors**
	- In binary trees, all nodes except the root had an indegree of 1 (due to their parent)
	- All nodes have an outdegree of 0, 1, or 2
	- An outdegree of 0 means that it is a leaf
	- Specific to trees, we used the parent/child terms instead of "neighbors"
- a graph can be either **cyclic**:
	- graph has a cycle
- or **acyclic**:
	- graph does not have a cycle
### How are graphs represented in algorithm problems?
> An important thing to understand is that with linked lists and binary trees, you are literally given objects in memory that contain data and pointers. With graphs, the graph doesn't literally exist in memory.
> 
> In fact, only the "idea" of the graph exists. The input will give you some information about it, and it's up to you to figure out how to represent and traverse the graph with code.
> 
> Many times, the nodes of a graph will be labeled from `0` to `n - 1`. The problem statement may or may not explicitly state the input is a graph. Sometimes there might be a story, and you need to determine that the input is a graph. For example, "there are `n` cities labeled from `0` to `n - 1`". You can treat each city as a node and each city has a unique label.

- with graphs, any node can have any number of neighbors
	- we usually need to do some work to make sure that for any given `node`, we can immediately access all the neighbors of said `node`
#### **First input format: array of edges**
- In this input format, the input will be a 2D array. Each element of the array will be in the form `[x, y]`, which indicates that there is an edge between `x` and `y`. The problem may have a story for these edges - using the cities example, the story would be something like "`[x, y]` means there is a highway connecting city `x` and city `y`".
- starting a DFS from node `0`
	- we can't start traversal immediately because we would need to iterate over the entire input to find all edges that include `0`
	- when we move to a neighbor node, we would need to iterate over the entire input again to find all neighbors of that node
	- this is very slow
	- **instead, preprocess the input so that we can easily find all neighbors of any given node**
		- you want a data structure where you can give `node` as an argument & be returned a list of neighbors
	- **USE A HASH MAP**
		- hash map `graph` that maps integers to lists of integers
		- iterate over the input
		- for each `[x, y]` pair, we can put `y` in the list associated with `graph[x]`
		- if the edges are undirected, we will also need to put `x` in the list associated with `graph[y]`
		- after building the hash map, we can do `graph[0]` and have all neighbors of node `0` returned
`edges = [[0, 1], [1, 2], [2, 0], [2, 3]]`
![[Pasted image 20240806123703.png]]
```python
from collections import defaultdict

def build_graph(edges):
    graph = defaultdict(list)
    for x, y in edges:
        graph[x].append(y)
        # graph[y].append(x)
        # uncomment the above line if the graph is undirected
    
    return graph
```

#### Second input format: adjacency list
- nodes are numbered from `0` to `n - 1`
- input is a 2d integer array, `graph`
	- `graph[i]` is a list of all the outgoing edges from the `i`th node
- e.g. `graph = [[1], [2], [0, 3], []]`
	- we can already access all the neighbors of any given `node`
	- don't need any pre-processing
	- if we want all neighbors of node `6`, just check `graph[6]`

#### Third input format: adjacency matrix
- nodes are also number `0` through `n - 1`
- given a 2d matrix of size `graph = n * n`, 
	- if `graph[i][j] == 1`, that means there is an outgoing edge from node `i` to node `j`
![[Pasted image 20240806145604.png]]
2 options:
1. any given `node` you can iterate over `graph[node]`
	- if `graph[node][i] == 1`, then you know node `i` is a neighbor
2. pre-process the graph as we did with the array of edges
	- build a hash map
	- then iterate over entire `graph`
	- if `graph[i][j] == 1`, then put `j` in the list associated with `graph[i]`
	- thus when performing the traversal, you will not need to iterate `n` times at every node to find the neighbors
		- especially useful when nodes have only a few neighbors and `n` is large
	- both approaches have time complexity of *O(n^2)*

#### Final input format: matrix
- 2d matrix, problem describes a story
- square represents somethhing, and squares are connected some way
	- e.g. "each square of the matrix is a village. villages trade with their neighboring villages, which are the villages above, left, right, or below them
- each square `(row, col)` of the matrix is a node, and the neighbors are  `(row - 1, col), (row, col - 1), (row + 1, col), (row, col + 1)` (if in bounds)
	- need a function to check if in bounds
- nodes are not provided in these problems, they must be inferred by the story
##### Differences between graphs & trees
- binary tree has a `root` node to start from
	- a graph does not always have an obvious "start" point
- when traversing a tree, we refer to `node.left` and `node.right` at each node
	- when traversing a graph, we need to use a for loop to iterate over the neighbors of the current node, since a node could have any number of neighbors
- DFS for graphs is similar to its implementation for trees (recursive)
	- check for base case
	- recursively call on all neighbors
	- do some logic to calculate the answer
	- return the answer
	- can also be done iteratively using a stack
- in undirected graph or a directed graph with cycles, implementing DFS the same way we did with binary trees will result in an infinite cycle
	- like with trees, in most graph questions, we only need to visit each node once
	- to prevent cycles and unnecessarily visiting a node more than once, we can use a set `seen`
		- before we visit a node, we first check if the node is in `seen`
		- if it isn't, we add it to `seen` before visiting it
		- this allows us to only visit each node once in *O(1)* time, because adding and checking for existence in a set takes constant time

## DFS

There are `n` cities. A province is a group of directly or indirectly connected cities and no other cities outside of the group. You are given an `n x n` matrix `isConnected` where `isConnected[i][j] = isConnected[j][i] = 1` if the ith city and the jth city are directly connected, and `isConnected[i][j] = 0` otherwise. Return the total number of provinces.
- number of connected components
- each city is a node
- a DFS from any node will visit every node in the connected component
- to avoid cycles with undirected graphs, need to use a set `seen` to track which nodes we have already visited
- after performing a DFS on a connected component, all nodes in that component will be inside `seen`
	- therefore we can iterate from `0` to `n`, and each time we find a node that hasn't been visited yet, we know we also have a component that hasn't been visited
	- so we perform a DFS to "mark" the component as visited and increment our answer
```python
from collections import defaultdict

class Solution:
    def findCircleNum(self, isConnected: List[List[int]]) -> int:
        def dfs(node):
            for neighbor in graph[node]:
                # the next 2 lines are needed to prevent cycles
                if neighbor not in seen:
                    seen.add(neighbor)
                    dfs(neighbor)
        
        # build the graph
        n = len(isConnected)
        graph = defaultdict(list)
        for i in range(n):
            for j in range(i + 1, n):
                if isConnected[i][j]:
                    graph[i].append(j)
                    graph[j].append(i)
        
        seen = set()
        ans = 0
        
        for i in range(n):
            if i not in seen:
                # add all nodes of a connected component to the set
                ans += 1
                seen.add(i)
                dfs(i)
        
        return ans
        
```
alternative dfs using a stack:
```python
def dfs(start):
    stack = [start]
    while stack:
        node = stack.pop()
        for neighbor in graph[node]:
            if neighbor not in seen:
                seen.add(neighbor)
                stack.append(neighbor)
```

Example 2: [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)

Given an `m x n` 2D binary `grid` which represents a map of `1` (land) and `0` (water), return the number of islands. An island is surrounded by water and is formed by connecting adjacent land cells horizontally or vertically.

- this is the same problem as example #1, but with a different input format
```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        def valid(row, col):
            return 0 <= row < m and 0 <= col < n and grid[row][col] == "1"
        
        def dfs(row, col):
            for dx, dy in directions:
                next_row, next_col = row + dy, col + dx
                if valid(next_row, next_col) and (next_row, next_col) not in seen:
                    seen.add((next_row, next_col))
                    dfs(next_row, next_col)
        
        directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
        seen = set()
        ans = 0
        m = len(grid)
        n = len(grid[0])
        for row in range(m):
            for col in range(n):
                if grid[row][col] == "1" and (row, col) not in seen:
                    ans += 1
                    seen.add((row, col))
                    dfs(row, col)
        
        return ans
```

Example 3: [1466. Reorder Routes to Make All Paths Lead to the City Zero](https://leetcode.com/problems/reorder-routes-to-make-all-paths-lead-to-the-city-zero/)

There are `n` cities numbered from `0` to `n - 1` and `n - 1` roads such that there is only one way to travel between two different cities. Roads are represented by `connections` where `connections[i] = [x, y]` represents a road from city `x` to city `y`. The edges are directed. You need to swap the direction of some edges so that every city can reach city `0`. Return the minimum number of swaps needed.
- because there is only one road between cities, **all roads** must be directed towards 0
- this means we can traverse away from `0`, and any time we see that an edge is pointing away from `0`, we know we need to swap it
- we must convert it to undirected graph so we can reach all nodes from `0`
- because our traversal direction is away from `0`, at every `node`, every traversal to a `neighbor` that hasn't been visited will be away from `0`
- therefore, if `(node, neighbor)` is in `connections`, we know we need to swap that road
- to have fast O(1) checking for if a given edge is in `connections`, we can put the original directed edges in a set `roads`
```python
class Solution:
    def minReorder(self, n: int, connections: List[List[int]]) -> int:
        roads = set()
        graph = defaultdict(list)
        for x, y in connections:
            graph[x].append(y)
            graph[y].append(x)
            roads.add((x, y))

        def dfs(node):
            ans = 0
            for neighbor in graph[node]:
                if neighbor not in seen:
                    if (node, neighbor) in roads:
                        ans += 1
                    seen.add(neighbor)
                    ans += dfs(neighbor)
            
            return ans

        seen = {0}
        return dfs(0)
```


Example 4: [841. Keys and Rooms](https://leetcode.com/problems/keys-and-rooms/)

There are `n` rooms labeled from `0` to `n - 1` and all the rooms are locked except for room `0`. Your goal is to visit all the rooms. When you visit a room, you may find a set of distinct keys in it. Each key has a number on it, denoting which room it unlocks, and you can take all of them with you to unlock the other rooms. Given an array `rooms` where `rooms[i]` is the set of keys that you can obtain if you visited room `i`, return true if you can visit all the rooms, or false otherwise

- input is an **adjacency matrix**
	- `rooms[i]` is an array of other rooms we can visit from the current room
- start at room `0` and need to visit every room
- at every node `i`, the neighbors are `rooms[i]`
- if we can start a DFS at `0` and visit every node, then the answer is `True`
```python
class Solution:
    def canVisitAllRooms(self, rooms: List[List[int]]) -> bool:
        def dfs(node):
            for neighbor in rooms[node]:
                if neighbor not in seen:
                    seen.add(neighbor)
                    dfs(neighbor)
            
        seen = {0}
        dfs(0)
        return len(seen) == len(rooms)
```


Example 5: [1557. Minimum Number of Vertices to Reach All Nodes](https://leetcode.com/problems/minimum-number-of-vertices-to-reach-all-nodes/)

Given a directed acyclic graph, with `n` vertices numbered from `0` to `n-1`, and an array `edges` where `edges[i] = [x, y]` represents a directed edge from node `x` to node `y`. Find the smallest set of vertices from which all nodes in the graph are reachable.
- rephrase: smallest set of nodes that are not reachable from other nodes
	- because if a node can be reached from another node, then we would rather include the parent node rather than the child in our set
	- **nodes with an indegree of 0**

## BFS
- **USE TO FIND SHORTEST PATH**
- BFS on a graph always visits nodes in ascending order from the **starting point**
	- thus every time you visit a node, you must have reached it in the minimum steps possible from wherever you started your BFS
- use a queue
Example 1: [1091. Shortest Path in Binary Matrix](https://leetcode.com/problems/shortest-path-in-binary-matrix/)

Given an `n x n` binary matrix `grid`, return the length of the shortest clear path in the matrix. If there is no clear path, return `-1`. A clear path is a path from the top-left cell `(0, 0)` to the bottom-right cell `(n - 1, n - 1)` such that all visited cells are `0`. You may move 8-directionally (up, down, left, right, or diagonally).
```python
from collections import deque

class Solution:
    def shortestPathBinaryMatrix(self, grid: List[List[int]]) -> int:
        if grid[0][0] == 1:
            return -1
        
        def valid(row, col):
            return 0 <= row < n and 0 <= col < n and grid[row][col] == 0
        
        n = len(grid)
        seen = {(0, 0)}
        queue = deque([(0, 0, 1)]) # row, col, steps
        directions = [(0, 1), (1, 0), (1, 1), (-1, -1), (-1, 1), (1, -1), (0, -1), (-1, 0)]
        
        while queue:
            row, col, steps = queue.popleft()
            if (row, col) == (n - 1, n - 1):
                return steps
            
            for dx, dy in directions:
                next_row, next_col = row + dy, col + dx
                if valid(next_row, next_col) and (next_row, next_col) not in seen:
                    seen.add((next_row, next_col))
                    queue.append((next_row, next_col, steps + 1))
        
        return -1
```
- work at each node is *O(1)*, which makes overall time complexity *O(n^2)*
- space complexity also *O(n^2)* as seen can grow to that size-
Example 2: [863. All Nodes Distance K in Binary Tree](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree/)

Given the `root` of a binary tree, a target node `target` in the tree, and an integer `k`, return an array of the values of all nodes that have a distance `k` from the target node.
- in binary tree, we only have pointers to children
- we can easily find the nodes at distance `k` that are in the target node's subtree, but what about all the other nodes?
- convert tree into graph by assigning every node a `parent` pointer
	- thus it becomes undirected graph
- then perform a BFS starting at `target`
	- after `k` steps, we return the nodes in the queue
```python
from collections import deque

class Solution:
    def distanceK(self, root: TreeNode, target: TreeNode, k: int) -> List[int]:
        def dfs(node, parent):
            if not node:
                return
            
            node.parent = parent
            dfs(node.left, node)
            dfs(node.right, node)
            
        dfs(root, None)
        queue = deque([target])
        seen = {target}
        distance = 0
        
        while queue and distance < k:
            current_length = len(queue)
            for _ in range(current_length):
                node = queue.popleft()
                for neighbor in [node.left, node.right, node.parent]:
                    if neighbor and neighbor not in seen:
                        seen.add(neighbor)
                        queue.append(neighbor)
            
            distance += 1
        
        return [node.val for node in queue]
```
> Example 3: [542. 01 Matrix](https://leetcode.com/problems/01-matrix/)
> 
> Given an `m x n` binary (every element is `0` or `1`) matrix `mat`, find the distance of the nearest `0` for each cell. The distance between adjacent cells (horizontally or vertically) is `1`.
> 
> For example, given `mat = [[0,0,0],[0,1,0],[1,1,1]]`, return `[[0,0,0],[0,1,0],[1,2,1]]`.
- distance of all `0`s is `0` (no change)
- find all the nearest `1`s starting from `0`s
```python
from collections import deque

class Solution:
    def updateMatrix(self, mat: List[List[int]]) -> List[List[int]]:
        def valid(row, col):
            return 0 <= row < m and 0 <= col < n and mat[row][col] == 1
        
        # if you don't want to modify the input, you can create a copy at the start
        m = len(mat)
        n = len(mat[0])
        queue = deque()
        seen = set()
        
        for row in range(m):
            for col in range(n):
                if mat[row][col] == 0:
                    queue.append((row, col, 1))
                    seen.add((row, col))
        
        directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]

        while queue:
            row, col, steps = queue.popleft()
            
            for dx, dy in directions:
                next_row, next_col = row + dy, col + dx
                if (next_row, next_col) not in seen and valid(next_row, next_col):
                    seen.add((next_row, next_col))
                    queue.append((next_row, next_col, steps + 1))
                    mat[next_row][next_col] = steps
        
        return mat
```

Example 4: [1293. Shortest Path in a Grid with Obstacles Elimination](https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/)

You are given an `m x n` integer matrix `grid` where each cell is either `0` (empty) or `1` (obstacle). You can move up, down, left, or right from and to an empty cell in one step. Return the minimum number of steps to walk from the upper left corner to the lower right corner given that you can eliminate at most `k` obstacles. If it is not possible, return `-1`.
- same as 1st example, with an additional state var `remain` that represents how many removals we have remaining
- at each square, if a neighbor is an obstacle, we can still walk to if if remain > 0
```python
from collections import deque

class Solution:
    def shortestPath(self, grid: List[List[int]], k: int) -> int:
        def valid(row, col):
            return 0 <= row < m and 0 <= col < n
        
        m = len(grid)
        n = len(grid[0])
        queue = deque([(0, 0, k, 0)])
        seen = {(0, 0, k)}
        directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
        
        while queue:
            row, col, remain, steps = queue.popleft()
            if row == m - 1 and col == n - 1:
                return steps
            
            for dx, dy in directions:
                next_row, next_col = row + dy, col + dx
                if valid(next_row, next_col):
                    if grid[next_row][next_col] == 0:
                        if (next_row, next_col, remain) not in seen:
                            seen.add((next_row, next_col, remain))
                            queue.append((next_row, next_col, remain, steps + 1))
                    # otherwise, it is an obstacle and we can only pass if we have remaining removals
                    elif remain and (next_row, next_col, remain - 1) not in seen:
                        seen.add((next_row, next_col, remain - 1))
                        queue.append((next_row, next_col, remain - 1, steps + 1))
        
        return -1

```
Example 5: [1129. Shortest Path with Alternating Colors](https://leetcode.com/problems/shortest-path-with-alternating-colors/)

You are given a directed graph with `n` nodes labeled from `0` to `n - 1`. Edges are red or blue in this graph. You are given `redEdges` and `blueEdges`, where `redEdges[i]` and `blueEdges[i]` both have the format `[x, y]` indicating an edge from `x` to `y` in the respective color. Return an array `ans` of length `n`, where `answer[i]` is the length of the shortest path from `0` to `i` where edge colors alternate, or `-1` if no path exists.
- store `RED` or `BLUE` along with the node to indicate what the last edge was
- perform a BFS starting from both `(0, RED)` and `(0, BLUE)`
- every time we traverse an edge, we need to first make sure that we are only considering edges of the correct color, then when making the traversal we need to switch `RED <> BLUE`
- > One neat trick to flip between `1` and `0` is `f(x) = 1 - x`. `f(1) = 0` and `f(0) = 1`.
- whenever we introduce new state variables, we need to also include those variables in `seen`
	- so we treat `(node, color)` as one state in `seen`
```python
from collections import defaultdict, deque

class Solution:
    def shortestAlternatingPaths(self, n: int, redEdges: List[List[int]], blueEdges: List[List[int]]) -> List[int]:
        RED = 0
        BLUE = 1
        
        graph = defaultdict(lambda: defaultdict(list))
        for x, y in redEdges:
            graph[RED][x].append(y)
        for x, y in blueEdges:
            graph[BLUE][x].append(y)
        
        ans = [float("inf")] * n
        queue = deque([(0, RED, 0), (0, BLUE, 0)])
        seen = {(0, RED), (0, BLUE)}
        
        while queue:
            node, color, steps = queue.popleft()
            ans[node] = min(ans[node], steps)
            
            for neighbor in graph[color][node]:
                if (neighbor, 1 - color) not in seen:
                    seen.add((neighbor, 1 - color))
                    queue.append((neighbor, 1 - color, steps + 1))
        
        return [x if x != float("inf") else -1 for x in ans]
```

## Implicit Graphs
- regardless of input format, it the problem wants the shortest path or fewest operations, BFS is a great candidate
> Example 1: [752. Open the Lock](https://leetcode.com/problems/open-the-lock/)
> 
> You have a lock with 4 circular wheels. Each wheel has the digits `0` to `9`. The wheels rotate and wrap around - so `0` can turn to `9` and `9` can turn to `0`. Initially, the lock reads `"0000"`. One move consists of turning a wheel one slot. You are given an array of blocked codes `deadends` - if the lock reads any of these codes, then it can no longer turn. Return the minimum number of moves to make the lock read `target`.

- consider each lock state as a node
- edges are all nodes that differ by only one position by a value of 1
	- e.g. `"5321"` & `"5331"` are neighbors
- from here we can just perform a simple BFS from `"0000"`, with the 1 condition that we cannot visit any nodes in `deadends`
	- turn `deadends` into a set for *O(1)* checking
- to find neighbors of a node, we can loop over each of the 4 slots, and each slot, increment and decrement the slot by `1`
- to handle the wrap-arond case, we can use the modulo operator:
	- `decrement(x) = (x - 1) % 10`
	- `increment(x) = (x + 1) % 10`
```python
class Solution:
    def openLock(self, deadends: List[str], target: str) -> int:
        def neighbors(node):
            ans = []
            for i in range(4):
                num = int(node[i])
                for change in [-1, 1]:
                    x = (num + change) % 10
                    ans.append(node[:i] + str(x) + node[i + 1:])

            return ans

        if "0000" in deadends:
            return -1

        queue = deque([("0000", 0)])
        seen = set(deadends)
        seen.add("0000")
        
        while queue:
            node, steps = queue.popleft()
            if node == target:
                return steps
        
            for neighbor in neighbors(node):
                if neighbor not in seen:
                    seen.add(neighbor)
                    queue.append((neighbor, steps + 1))
        
        return -1
```
Example 2: [399. Evaluate Division](https://leetcode.com/problems/evaluate-division/)

You are given an array `equations` and a number array `values` of the same length. `equations[i] = [x, y]` represents `x / y = values[i]`. You are also given an array `queries` where `queries[i] = [a, b]` which represents the quotient `a / b`. Return an array `answer` where `answer[i]` is the answer to the ithith query, or `-1` if it cannot be determined.

For example, let's say we have `equations = [["a", "b"], ["b", "c"]]` and `values = [2, 3]`. This input represents ab=2ba​=2 and bc=3cb​=3. If we had a query `["a", "c"]`, the answer to that query would be `6`, because we can deduce that ac=6ca​=6.


# Heaps
- data structure implementation of a priority queue
- add element in *O(log n)*
- remove minimum element in *O(log n)*
- find minimum element in *O(1)*
- convert an existing array to a heap in *linear time O(n)*!
- **great option if you need to find the min/max of something repeatedly**
```python
# In Python, we will use the heapq module
# Note: heapq only implements min heaps
from heapq import *

# Declaration: heapq does not give you a heap data structure.
# You just use a normal list, and heapq provides you with
# methods that can be used on this list to perform heap operations
heap = []

# Add to heap
heappush(heap, 1)
heappush(heap, 2)
heappush(heap, 3)

# Check minimum element
heap[0] # 1

# Pop minimum element
heappop(heap) # 1

# Get size
len(heap) # 2

# Bonus: convert a list to a heap in linear time
nums = [43, 2, 13, 634, 120]
heapify(nums)

# Now, you can use heappush and heappop on nums
# and nums[0] will always be the minimum element
```
Example 1: [1046. Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)

You are given an array of integers `stones` where `stones[i]` is the weight of the ithith stone. On each turn, we choose the heaviest two stones and smash them together. Suppose the heaviest two stones have weights `x` and `y` with `x <= y`. If `x == y`, then both stones are destroyed. If `x != y`, then `x` is destroyed and `y` loses `x` weight. Return the weight of the last remaining stone, or `0` if there are no stones left.
- repeatedly find 2 maximum elements
- convert `stones` into a max heap, so we can pop the 2 max elements, perform the smash, and re-add to the heap (all in *O(log n)*
```python
import heapq

class Solution:
    def lastStoneWeight(self, stones: List[int]) -> int:
        stones = [-stone for stone in stones]
        heapq.heapify(stones) # turns an array into a heap in linear time
        while len(stones) > 1:
            first = abs(heapq.heappop(stones))
            second = abs(heapq.heappop(stones))
            if first != second:
                heapq.heappush(stones, -abs(first - second))

        return -stones[0] if stones else 0
```
Python's [heap implementation](https://docs.python.org/3/library/heapq.html) only implements min heaps. To simulate a max heap, we can just make all values we put on the heap negative.

## Top `k`
- "find the `k` best elements
- just sorting the input has a time complexit of *O(n log(n))*
- using a heap, we can instead find the top `k` elements in *O(n log(k)*
	- k < n, so this is an improvement
Example 1: [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

Given an integer array `nums` and an integer `k`, return the `k` most frequent elements. It is guaranteed that the answer is unique.

# Tries
- data structure also know as a "prefix tree"
- stores characters of a string at each node
- all paths from the root to a node represent a string of characters on the path
![[Pasted image 20240807141129.png]]

- used to effectively implement string searching/matching algorithms
- keep track of which words have a prefix
```python
# note: using a class is only necessary if you want to store data at each node.
# otherwise, you can implement a trie using only hash maps.
class TrieNode:
    def __init__(self):
        # you can store data at nodes if you wish
        self.data = None
        self.children = {}

def build_trie(words):
    root = TrieNode()
    for word in words:
        curr = root
        for c in word:
            if c not in curr.children:
                curr.children[c] = TrieNode()
            curr = curr.children[c]
        # at this point, you have a full word at curr
        # you can perform more logic here to give curr an attribute if you want
    
    return root
```
- building a trie takes O(n * k) time, where n is length of `words` and `k` is average length of strings in `words`
Example: [1268. Search Suggestions System](https://leetcode.com/problems/search-suggestions-system/)

You are given an array of strings `products` and a string `searchWord`. Design a system that suggests at most three product names from `products` after each character of `searchWord` is typed. Suggested products should share a common prefix with `searchWord`. If there are more than three products with a common prefix, choose the three lexicographical minimums. Return a list of lists of the suggested products after each character of `searchWord` is typed.

- As `searchWord` is typed, a prefix is formed
	- for example, if `searchWord = "abcde"`, then the prefixes after each character are `"a", "ab", "abc", "abcd", "abcde"`.
	- For each prefix, we need to find (up to) 3 of the lexicographically smallest words from `products` that share the prefix.

- The brute force approach would be to iterate over `products` for each prefix and check which ones match.
	- This would have a time complexity of O(n⋅m2), where `n = products.length` and `m = searchWord.length`
- It would cost us O(n⋅k) to build a trie from `products` where kk is the average length of each product, and then we could find all the words with matching prefixes in just O(m) time
	- Because `k` is small, this time complexity of O(n⋅k+m) is much better.

Remember: each node in the trie represents a prefix, with the root representing the empty string. We can use an attribute `suggestions` to store the 3 products that should be returned. To stay in line with the problem's constraints, we will limit the size to 3 and keep it sorted (which is cheap since the size is limited to 3).

Once the trie is built, we can traverse the tree by starting at the root and iterating over `searchWord`. At each node, the trie instantly gives us the answer.

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.suggestions = []
        
class Solution:
    def suggestedProducts(self, products: List[str], searchWord: str) -> List[List[str]]:        
        root = TrieNode()
        for product in products:
            node = root
            for c in product:
                if c not in node.children:
                    node.children[c] = TrieNode()
                node = node.children[c]

                node.suggestions.append(product)
                node.suggestions.sort()
                if len(node.suggestions) > 3:
                    node.suggestions.pop()
        
        ans = []
        node = root
        for c in searchWord:
            if c in node.children:
                node = node.children[c]
                ans.append(node.suggestions)
            else:
                # deadend reached
                node.children = {}
                ans.append([])

        return ans
```

- 
# Greedy algorithms
- any algorithm that makes the locally optimal decision at every step
	- if we are choosing some elements and the problem wants us to find the maximum sum of elements we take, then given 2 numbers, it's optimal to take the larger one
	- a decision is local when it considers only the available options at the current step
		- based on the information it has at the time, and doesn't consider any consequences that may happen in the future from this decision
- **most greedy problems will be asking for the max or min of something**

>Given an integer array `nums` and an integer `k`, split `nums` into subsequences, where each subsequences' maximum and minimum element is within `k` of each other. What is the minimum number of subsequences needed?

For example, given `nums = [3, 6, 1, 2, 5]` and `k = 2`, the answer is `2`. The subsequences are `[3, 1, 2]` and `[6, 5]`.
- in this problem actual order of elements in subsequence doesn't matter as we are just getting the max & min
- without the order requirement, subsequence is the same as subset
- minimize number of groupings,  in other words maximize the number of elements in the groups
- start with the smallest number in the input `x`
- we want all elements in the range `[x, x + k]` to be grouped
- so, it's best to greedily take all elements within the range `[x, x + k]` for the smallest number `x`
	- after that, we can "erase" those numbers from the array, and we have the same problem with a different `x`
	- sort array & iterate over it
		- sorting doesn't change anything because we logically reduced subsequences to subsets
```python
class Solution:
    def partitionArray(self, nums: List[int], k: int) -> int:
        nums.sort()
        ans = 1
        x = nums[0]
        
        for i in range(1, len(nums)):
            if nums[i] - x > k:
                x = nums[i]
                ans += 1
        
        return ans
```

> Example 3: [502. IPO](https://leetcode.com/problems/ipo/)
> 
> LeetCode would like to work on some projects to increase its capital before `IPO`. You are given `n` projects where the ithith project has a profit of `profits[i]` and a minimum capital of `capital[i]` is needed to start it. Initially, you have `w` capital. When you finish a project, the profit will be added to your total capital. Return the max capital possible if you are allowed to do up to `k` projects.
- out of all available projects, we should do the one with the most profit at each iteration - hence greedy!
- sort inputs by capital, then use a pointer `i` that stores the index of the most expensive project we can afford

# Binary search
- *O(log n)* in worst case, where `n` is the size of the search space
- search space needs to be sorted
- find the index of `x` in sorted `arr`
- find the first or last index in which `x` can be inserted to maintain sort
- first, check the middle element of `arr`
- if this element is too small, all elements to the left are also too small, so we discard/ignore that half
- likewise if x is too large, ignore right half
- repeat the process on the remaining half, until we find `x`
1. Declare `left = 0` and `right = arr.length - 1`. These variables represent the inclusive bounds of the current search space at any given time. Initially, we consider the entire array.
2. While `left <= right`:
    - Calculate the middle of the current search space, `mid = (left + right) // 2` (floor division)
    - Check `arr[mid]`. There are 3 possibilities:
        - If `arr[mid] = x`, then the element has been found, return.
        - If `arr[mid] > x`, then halve the search space by doing `right = mid - 1`.
        - If `arr[mid] < x`, then halve the search space by doing `left = mid + 1`.
3. If you get to this point without `arr[mid] = x`, then the search was unsuccessful. The `left` pointer will be at the index where `x` would need to be inserted to maintain `arr` being sorted.
```python
def binary_search(arr, target):
    left = 0
    right = len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            # do something
            return
        if arr[mid] > target:
            right = mid - 1
        else:
            left = mid + 1
    
    # target is not in arr, but left is at the insertion point
    return left
```
If input has duplicates, modify the binary search template to find either the first or last position of a given element
if `target` appears multiple times, then the following template will find the left-most index:
```python
def binary_search(arr, target):
    left = 0
    right = len(arr)
    while left < right:
        mid = (left + right) // 2
        if arr[mid] >= target:
            right = mid
        else:
            left = mid + 1

    return left
```

Example 2: [74. Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/)

Write an efficient algorithm that searches for a value `target` in an `m x n` integer matrix `matrix`. Integers in each row are sorted from left to right. The first integer of each row is greater than the last integer of the previous row.
- because each row is sorted, and less than the next row, just treat the matrix as one array
	- length of `m * n`
	- Each row has `n` elements. That means that row `0` is indices `[0, n - 1]`. Row `1` is indices `[n, 2 * n - 1]`, and so on. This is equivalent to the floor division of `n`, aka `row = i // n` - the row increments every `n` indices
	- The column can range between `[0, n - 1]`. Every `n` indices, the column resets to `0`. This is perfect for the modulo operator. `col = i % n`.
```python
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        m, n = len(matrix), len(matrix[0])
        left, right = 0, m * n - 1
        
        while left <= right:
            mid = (left + right) // 2
            row = mid // n
            col = mid % n
            num = matrix[row][col]
            
            if num == target:
                return True
            
            if num < target:
                left = mid + 1
            else:
                right = mid - 1
        
        return False
```

## On solution spaces
- more creative way to use binary search - on a solution space/answer
- "what is the max/min that something can be done"
- binary search can be used if the following criteria are met:
1. you can quickly (*O(n)* or better) verify if the task is possible for a given number `x`
2. if the task is possible for a number `x`, and you are looking for:
	1. a maximum, then it is also possible for all numbers less that `x`
	2. a minimum, then it is also possible for all numbers greater than `x`
3. if the task is not possible for a number `x`, and you are looking for:
	1. a maximum, then it is also possible for all numbers greater than `x`
	2. a minimum, then it is also impossible for all numbers less than `x`
- imagine 2 zones, one where it is possible and one where it is impossible
	- no breaks, no overlap, and separated by a threshold:
![[Pasted image 20240809154434.png]]
- when a problem wants you to find the min/max, it wants you to find the threshold where the task transitions from impossible to possible
#### approach
1. Establish the possible solution space by identifying the minimum possible answer and the maximum possible answer
2. binary search the solution space
	1. for each `mid`, we perform a check to see if the task is possible
	2. depending on the result, we halve the search space
	3. eventually, we hone in on the threshold
- if 1st requirement is met (quick verification if `mid` is possible), then this will give us a time complexity of *O(n * log k)*, where `k` is the solution space's range
- even if the possible solution space is huge, logarithms run so fast that this is a very efficient time complexity
- write a function `check` that takes an integer and checks if the task is possible for that integer
	- in most cases, the algorithm we use in this function will be a greedy one

Example 1: [875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas)

>Koko loves to eat bananas. There are `n` piles of bananas, the ithith pile has `piles[i]` bananas. Koko can decide her bananas-per-hour eating speed of `k`. Each hour, she chooses a pile and eats `k` bananas from that pile. If the pile has less than `k` bananas, she eats all of them and will not eat any more bananas during the hour. Return the minimum integer `k` such that she can eat all the bananas within `h` hours.

- eating speed `k`, and the task is possible, then all eating speeds greater than `k` will also work
- if it isn't possible, then all eating speeds slower than `k` will not work
- binary search for answer
- `ceiling(bananas / k)` time
	- loop over piles and find `sum(bananas/k)` for each pile, and check if that is less than or equal to `h`
- for the binary search, the bounds should start at the minimum and maximum possible answer
- the minimum possible answer is `1` - koko needs to eat more than `0` bananas per hour
- maximum possible answer is `max(piles)` - no point in having any eating speed faster than this
```python
class Solution:
    def minEatingSpeed(self, piles: List[int], h: int) -> int:
        def check(k):
            hours = 0
            for bananas in piles:
                hours += ceil(bananas / k)
            
            return hours <= h
        
        left = 1
        right = max(piles)
        while left <= right:
            mid = (left + right) // 2
            if check(mid):
                right = mid - 1
            else:
                left = mid + 1

        return left
    
```

# Backtracking
- optimization that involves abandoning a path once it is determined that the path cannot lead to a solution
- similar to binary search trees - if you're looking for x, and the root node is greater than x, then you can ignore the entire right subtree
- abandoning a path is sometimes called "pruning"
- **great tool whenever a problem wants you to find all of something, or there isn't a clear way to find a solution without checking all logical possibilities**
- **always implemented with recursion**
- you will be building something, e.g.
	- directly: modifying an array
	- indirectly: using variables to represent some state
- analogy: possiblilities represented as a tree, e.g all letters of alphabet
![[Pasted image 20240810213831.png]]
- 26^n possibilities!
#### psuedocode:
```javascript
// let curr represent the thing you are building
// it could be an array or a combination of variables

function backtrack(curr) {
    if (base case) {
        Increment or add to answer
        return
    }

    for (iterate over input) {
        Modify curr
        backtrack(curr)
        Undo whatever modification was done to curr
    }
}
```
- each call to `backtrack` represents a node in the tree
- each iteration in the loop represents a child of the current node
- calling `backtrack` in that loop represents moving to a child
- the line where you undo the modifications is the "backtracking" step
	- equivalent to moving back up the tree from a child to its parent
- at any given node, the path from the root to the node represents a candidate that is being built
- the leaf nodes are complete solutions and represent when the base case is reached
- the root of this tree is an empty candidate and represents the scope that the original `bactrack()` call is being made from
## Generation
- **comon type of problem that can be solved with backtracking are problems that ask you to generate *all* of something
Example 1: [46. Permutations](https://leetcode.com/problems/permutations/)

Given an array `nums` of distinct integers, return all the possible permutations in any order.

For example, given `nums = [1, 2, 3]`, return `[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]`.

- a permutation contains all the elements of `nums` with no duplicates
- let's build each permutation using a recursive function `backtrack(curr)`, where `curr` is the current permutation being built
- base case would be when `curr.length == nums.length`
	- we've completed the permutation & can't go further
	- on the base case, add `curr` to the answer and return
- to build all permutations, we need all elements at the first index
	- for each of those elements, we need all other elements at 2nd index, and so on
- therefore, we should loop over the entire input on each call to `backtrack`
- because a permutation cannot have duplicates, we should check if a number is already in `curr` before adding it to `curr`
- using `nums = [1, 2, 3]`, the answer tree looks like:
![[Pasted image 20240810215611.png]]
```python
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        def backtrack(curr):
            if len(curr) == len(nums):
                ans.append(curr[:])  # create a copy because curr is only a reference to the array's address
                return
        
            for num in nums:
                if num not in curr:
                    curr.append(num)
                    backtrack(curr)
                    curr.pop()  # get rid of num so it does not stick around when we go back up the tree
            
        ans = []
        backtrack([])
        return ans
```
When adding to the answer, we need to create a copy of `curr` because `curr` is only a [reference to the array's address](https://stackoverflow.com/questions/373419/whats-the-difference-between-passing-by-reference-vs-passing-by-value).

Example 2: [78. Subsets](https://leetcode.com/problems/subsets/)

Given an integer array `nums` of unique elements, return all subsets in any order without duplicates.

For example, given `nums = [1, 2, 3]`, return `[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]`
- use an integer argument that represents a starting point for iteration at each function call
	- this avoids duplicates
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        def backtrack(curr, i):
            if i > len(nums):
                return

            ans.append(curr[:])
            for j in range(i, len(nums)):
                curr.append(nums[j])
                backtrack(curr, j + 1)
                curr.pop()

        ans = []
        backtrack([], 0)
        return ans
```

# Dynamic programming
- **optimized recursion**
- returns the answer to the original problem as if the arguments you passed to it were the input
- argument represent a **state**
- states can be repeated, usually an exponential number of times
- to avoid repeating computation, we use something called **memoization**
- when we find the answer for a given state, we cache that value in a hash map
- then in the future, if we ever see the same state again, we can just refer to the cached value without needing to re-calculate it
without DP: below is *O(2^n)*:
```python
def fibonacci(n):
    if n == 0:
        return 0
    if n == 1:
        return 1
    
    return fibonacci(n - 1) + fibonacci(n - 2)
```
e.g. `fibonacci(6)`:
![[Pasted image 20240810221209.png]]
- lots of repeated computation
- to avoid this, we **memoize** the results from our function calls
```python
def fibonacci(n):
    if n == 0:
        return 0
    if n == 1:
        return 1

    if n in memo:
        return memo[n]
    
    memo[n] = fibonacci(n - 1) + fibonacci(n - 2)
    return memo[n]

memo = {}
```
- this improves our time complexity from *O(2^n)* to *O(n)*!

## Top-down vs. bottom-up
- above method using memoization is called **top-down** DP
	- start from top, move down towards base cases
- another way to appreach a DP problem is with **bottom-up** algorithm
	- start at the bottom (base cases) and work our way up to larger problems
- done iterively and known as **tabulation**
```python
def fibonacci(n):
    arr = [0] * (n + 1)
    # base case - the second Fibonacci number is 1
    arr[1] = 1
    for i in range(2, n + 1):
        arr[i] = arr[i - 1] + arr[i - 2]
    
    return arr[n]
```
- Usually, a bottom-up implementation is faster. This is because iteration has less overhead than recursion, although this is less impactful if your language implements [tail recursion](https://en.wikipedia.org/wiki/Tail_call).
- However, a top-down approach is usually easier to write. With recursion, the order that we visit states does not matter. With iteration, if we have a multidimensional problem, it can sometimes be difficult figuring out the correct configuration of your for loops.

## When to use DP:

Problems that should be solved with DP usually have two main characteristics:

1. The problem will be asking for an optimal value (max or min) of something or the number of ways to do something.
    - What is the minimum cost of doing ...
    - What is the maximum profit of ...
    - How many ways are there to ...
    - What is the longest possible ...
2. At each step, you need to make a "decision", and decisions affect future decisions.
    - A decision could be picking between two elements
    - Decisions affecting future decisions could be something like "if you take an element `x`, then you can't take an element `y` in the future"

## DP Framework
3 main components:
>For this section, we're going to use [Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs) as an example. We will start with a top-down solution.

>You are given an integer array `cost` where `cost[i]` is the cost of the ithith step on a staircase. Once you pay the cost, you can either climb one or two steps. You can either start from the step with index 0, or the step with index 1. Return the minimum cost to reach the top of the floor (outside the array, not the last index of `cost`).

It is recommended that you open the problem link in a new tab so you can easily follow along.
1. **function or data structure that will compute/contain the answer to the problem for any given state**
	- what is the function returning?
	- what arguments should the function take? (state variables)
		- `dp(state)` that returns the min cost to climb the stairs for a given state
		- only state var we need is an index along the input, `i`
		- `dp(i)` represents the min cost to climb the stairs up to the `i`th step
2. **a recurrence relation to transition between states**
	- equation used to calculate states
		- e.g. Fibonacci recurrence relation: Fn​=Fn−1​+Fn−2​.
	- to get to the 100th step, we must have arrived from either the 99th or 98th step
		- therefore, the min cost of climbing to the 100th step is either:
			- the min cost of getting to the 99th step + cost of 99th step
			- the min cost of getting to the 98th step + cost of 98th step
		- `dp(100) = min(dp(99) + cost[99], dp(98) + cost[98])`
			- `dp(i) = min(dp(i - 1) + cost[i - 1], dp(i - 2) + cost[i - 2])`
			- this is the recurrence relation of the problem
		- hardest part of constructing a DP algorithm
3. **Base cases**
	- we need base cases so that our function eventually returns actual values
	- `dp(0) = dp(1) = 0`
		- with these base cases, we can find `dp(2)`, and with that, we can find `dp(3)`, onwards until we can get `dp(98` and `dp(99)`, then finally `dp(100`
```python
class Solution:
    def minCostClimbingStairs(self, cost: List[int]) -> int:
        # 1. A function that returns the answer
        def dp(i):
            if i <= 1:
                # 3. Base cases
                return 0
            
            if i in memo:
                return memo[i]
            
            # 2. Recurrence relation
            memo[i] = min(dp(i - 1) + cost[i - 1], dp(i - 2) + cost[i - 2])
            return memo[i]
        
        memo = {}
        return dp(len(cost))
```

#### bottom-up solution
1. Start by implementing the top-down approach.
    
2. Initialize an array dpdp that is sized according to the state variables. For example, let's say the input to the problem was an array numsnums and an integer kk that represents the maximum number of actions allowed. Your array dpdp would be 2D with one dimension of length nums.lengthnums.length and the other of length kk. In the top-down approach, we had a function `dp`. We want these two to be equivalent. For example, the value of `dp(4, 6)` can now be found in `dp[4][6]`.
    
3. Set your base cases, same as the ones you are using in your top-down function. In the example we just looked at, we had `dp(0) = dp(1) = 0`. We can initialize our `dp` array values to `0` to implicitly set this base case. As you'll see soon, other problems will have more complicated base cases.
    
4. Write a for-loop(s) that iterate over your state variables. If you have multiple state variables, you will need nested for-loops. These loops should **start iterating from the base cases and end at the answer state**.
    
5. Now, each iteration of the inner-most loop represents a given state, and is equivalent to a function call to the same state in top-down. Copy-paste the logic from your function into the for-loop and change the function calls to accessing your array. All dp(...)dp(...) changes into dp[...]dp[...].
    
6. We're done! dpdp is now an array populated with the answer to the original problem for all possible states. Return the answer to the original problem, by changing return dp(...)return dp(...) to return dp[...]return dp[...].
```python
class Solution:
    def minCostClimbingStairs(self, cost: List[int]) -> int:
        n = len(cost)
        # Step 2
        dp = [0] * (n + 1)
        
        # Step 3: Base cases are implicitly defined as they are 0

        # Step 4
        for i in range(2, n + 1):
            # Step 5
            dp[i] = min(dp[i - 1] + cost[i - 1], dp[i - 2] + cost[i - 2])
        
        # Step 6
        return dp[n]
```

## 1D problems
Example 1: [198. House Robber](https://leetcode.com/problems/house-robber/)

You are planning to rob houses along a street. The ithith house has `nums[i]` money. If you rob two houses beside each other, the alarm system will trigger and alert the police. What is the most money you can rob without alerting the police?
- state variables:
	- `i`: maximum money that can be robbed if we consider up to and including house `i`
	- at house `i`, we have 2 possibilities:
		1. rob the house
			- gain `nums[i]` money, but can't rob previous house
			- if we skip previous house, that means we must have arrived from 2 houses back
			- the amount of money we had 2 houses ago is `dp(i - 2)`
				- therefore, if we rob the `i`th house, we will have `dp(i - 2) + nums[i]` money
		2. don't rob the house
			- don't gain any money, but could have arrived from previous house, meaning we will have `dp(i - 1)` money
	- always choose the max profit
	- recurrence relation is `dp(i) = max(dp(i - 1), dp(i - 2) + nums[i])`
- base case:
	- if there's only 1 house, we rob it
	- if there's 2 houses, we rob the max
	- therefore, base case is `dp(0) = nums[0]` and `dp(1) = max(nums[0], nums[1])`
```python
from functools import cache

class Solution:
    def rob(self, nums: List[int]) -> int:
        @cache
        def dp(i):
            # Base cases
            if i == 0:
                return nums[0]
            if i == 1:
                return max(nums[0], nums[1])

            # Recurrence relation
            return max(dp(i - 1), dp(i - 2) + nums[i])

        return dp(len(nums) - 1)
```

Example 2: [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

Given an integer array `nums`, return the length of the longest strictly increasing subsequence.

- we know it's DP because it asks for max length, and decisions made earlier affect optimal outcome later
	- e.g. `nums = [1, 2, 5, 3, 4]`
		- a greedy algorithm would grab the 5, but then we could not take 3 or 4
- what's the function & variables?
	- return the length of the LIS at `i`
	- `dp(i)` returns the LIS that ends with the `i`th element
- 
- whats the recurrence relation?
	- we can only use the current element if the previous element was less than the current number
	- so we will only consider indices `j` in the range `[0, i]`, where `nums[i] > nums[j]`
		- because `dp(j)` returns the LIS that ends with the jth element, and `nums[i] > nums[j]`, that means we can take whatever subsequence ends at `j` and just add `nums[i]` to it
			- giving us a length of `dp(j) + 1`
	- thus, our recurrence relation is:
`dp(i) = max(dp(j) + 1) for all j: [0, i), if nums[i] > nums[j]`

- base case: each element by itself is technically an increasing subsequence with length `1`
```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        @cache
        def dp(i):
            ans = 1 # Base case

            # Recurrence relation
            for j in range(i):
                if nums[i] > nums[j]:
                    ans = max(ans, dp(j) + 1)
            
            return ans

        return max(dp(i) for i in range(len(nums)))
    
```

>Example 3: [2140. Solving Questions With Brainpower](https://leetcode.com/problems/solving-questions-with-brainpower/)

>You are given a 0-indexed 2D integer array `questions` where `questions[i] = points, brainpower`. You have to process the questions in order. Solving question `i` will earn you  points but you will be unable to solve each of the next brainpoweribrainpoweri​ questions. If you skip question ii, you get to decide on the next question. Return the maximum points you can score.

- Solve the question. We gain `questions[i][0]` points, but we cannot solve the next `i` questions. The next question we can solve is at index `j = i + questions[i][1] + 1`. Therefore, the total score is `questions[i][0] + dp(j)`.
- Skip the question. Like the problem says, this means we move on to the next question and make another decision there. The score is `dp(i + 1)`.

`dp(i) = max(questions[i][0] + dp(j), dp(i + 1))`, where `j = i + questions[i][1] + 1`

- **since we are moving forward through the array instead of backwards, our base case must be at the end**

- if `i >= len(questions)`, then we cannot score any more points
	- therefore our base case is `dp(i) = 0, when i >= n`
```python
class Solution:
    def mostPoints(self, questions: List[List[int]]) -> int:
        @cache
        def dp(i):
            if i >= len(questions):
                return 0
            
            j = i + questions[i][1] + 1
            return max(questions[i][0] + dp(j), dp(i + 1))
    
        return dp(0)
```