# Python

+ [Run](#run)
+ [Data types](#data-types)
+ [Variables](#variables)
+ [Input & output](#input--output)
+ [Logical operators](#logical-operators)
+ [String functions](#string-functions)
+ [Arithmetic functions](#arithmetic-functions)
+ [Loops](#loops)
+ [Arrays](#arrays)
+ [Unpacking](#unpacking)
+ [Deque](#deque)
+ [HashSet](#hashset)
+ [HashMap](#hashmap)
+ [DefaultDict](#defaultdict)
+ [Tuples](#tuples)
+ [Heap](#heap)
+ [Functions](#functions)
+ [Class](#class)
+ [csv to sql](#csv-to-sql)

## Run

```shell
python3 ./Python/test.pl.py
```

## Data types

| Type      |   |
|-----------|---|
| Text Type | str  |
| Numeric Types | int, float, complex  |
| Sequence Types | list, tuple, range  |
| Mapping Type | dict  |
| Set Types | set, frozenset  |
| Boolean Type  | bool |
| Binary Types | bytes, bytearray, memoryview |
| None Type | NoneType |

## Variables

```python
n, m = 0, "abc"
# none is null
n = 4
n = None  # reassign value
```

## Input & output

```python
print('Hello world!')
print("Hello world!")
print("""Hello world!""")
print("Program print 'Hello world!'")

# print
name = "User"
print("Hello,", name, ".")  # Hello, User .
print("Hello,", name, ".", sep="")  # Hello,User.
print(f"Hello, {name}.")  # Hello, User.

# adding zeroes
print(f"{123:0>9}")  # 000000123
print(f"{123:0<9}")  # 123000000
print(f"{123:0^9}")  # 000123000

# string operations
print("-" * 10)  # ----------

# string to int (see also float(), str())
n_1 = "1"
n_2 = "2"
print(n_1 + n_2)  # 12
n_1 = int(n_1)
n_2 = int(n_2)
print(n_1 + n_2)  # 3

# operations
print(f"{2 ** 0.5:.2f}")  # 1.41
print(10 // 3)  # 3 Floor division
print(10 % 3)  # 1 Modulus
last_digit = 1234 % 10  # 4
hour = 9
min = 5
print(f"{hour:02d}:{min:02d}")  # 09:05
```

## Logical operators

```python
# if-elif-else
yesterday_temp = int(input())
today_temp = int(input())
if today_temp > yesterday_temp:
    print("Today is warmer than yesterday.")
elif today_temp < yesterday_temp:
    print("Today is colder than yesterday.")
else:
    print("Today is the same temperature as yesterday.")

# or, and
first_letter = input()
last_letter = input()
if (first_letter == "а" or first_letter == "А") and (
        last_letter == "я" or last_letter == "Я"):
    print("Right")
else:
    print("Wrong")

# if in string contains string
text = input()
if "if" in text:
    print("Right")
else:
    print("Wrong")

# match
color = input()
match color:
    case 'red' | 'yellow':
        print('Stop.')
    case 'green':
        print('drive')
    case _:
        print('wrong value')

# short if
print("A") if a > b else print("B")

# not
a = True
print(not a)
```

## String functions

+ `len(str)` - string length

```python
# string immutable
s = "123"
print(s[1:2])

# valid numeric strings can be converted
print(int("100") + int("100"))  # 200

# and numbers can be converted to strings
print(str(100) + str(100))  # 100100

# ASCII value
print(ord("a"))  # 97

# combine with a delimiter
strings = ["1", "2", "3"]
print(",".join(strings))  # 1,2,3
```

## Arithmetic functions

+ `min("y", "c", "z")` # 1
+
+ `max(1, 2)` # 2
+ `abs(-10)` # 10
+ `5 / 2` # 2.5
+ `5 // 2` # 2
+ `3 // -2` # -2 round down, so negative round to -2
+ `int(3 / -2)` # -1
+ `10 % 3` # 1
+ `-10 % 3` # 2
+ `math.fmod(-10, 3)` # -1
+ `math.floor(3 / 2)` # 1
+ `math.ceil(3 / 2)` # 2
+ `math.sqrt(4)` # 2.0
+ `math.pow(2, 3)` # 8.0
+ `float("inf")` # max Int
+ `float("-inf")` # min Int

## Loops

while

```python
n = 0
while n < 5:
    print(n)
    n += 1
```

for

```python
for i in range(5):
    print(i)

for i in range(2, 5):
    print(i)

for i in range(5, 1, -1):
    print(i)

arr = [1, 2, 3]
for i in range(len(arr)):
    print(arr[i])

for n in arr:
    print(n)

# index and value
for i, n in enumerate(arr):
    print(f"{i} {n}")

# loop through two arrays simultaneously
nums1 = [1, 2, 3]
nums2 = [3, 4, 5]

for n1, n2 in zip(nums1, nums2):
    print(f"{n1} {n2}")
```

## Arrays

like list

```python
arr = [1, 2, 3]
arr[-1]  # 3 last value
arr[-2]  # 2 last second value
arr.append(4)
arr.append(5)
arr.pop()
arr.insert(1, 7)
arr[0] = 0

arr[1] * 5  # init array of size 5
len(arr)

arr = [1, 2, 3, 4]
arr[1:3]  # [2, 3] sublist aka slicing
arr.reverse()
arr.sort()  # ascending order
arr.sort(reverse=True)  # descending order

arr = ["fff", "aa", "erere"]
arr.sort(key=lambda x: len(x))

arr = [i for i in range(5)]  # [0, 1, 2, 3, 4]
arr = [i + i for i in range(5)]  # [0, 2, 4, 6, 8]

# 2D List
arr = [[0] * 4 for i in range(4)]  # 2d array of length 4 filled 0
```

## Unpacking

```python
a, b, c = [1, 2, 4]
```

## Deque

Queues (double ended queue)

```python
from collections import deque

queue = deque()
queue.append(1)
queue.append(2)
print(queue)  # deque([1, 2])

queue.popleft()
print(queue)  # deque([2])

queue.appendleft(3)  # deque([3, 2])
queue.pop()  # deque([3])
```

## HashSet

```python
mySet = set()
mySet.add(1)
mySet.add(2)
mySet.add(2)
print(mySet)  # {1, 2}
print(len(mySet))  # 2
print(1 in mySet)  # True
mySet.remove(2)  # {1}
# list to set
pritn(set([1, 2, 3]))

mySet = {i in i in range(5)}
```

## HashMap

aka dict

```python
myMap = {}
myMap["alice"] = 88
myMap["bob"] = 77
print("alice" in myMap)
myMap.pop("alice")

myMap = {"alice": 80, "bob": 90}

myMap = {i: i * 3 for i in range(3)}

val = myMap["xx"]  # KeyError if key is absent
print(map.get("xx"))  # None
print(map.get("xx", "default"))  # Default value

# looping through map
for key in myMap:
    print(key, myMap[key])

for val in myMap.values():
    print(val)

for key, value in myMap.items():
    print(key, value)

if key in dic.keys()
```

list (array) can't be used as key in hashmap.
A list can be converted to a tuple, which can be used as a dict key: e.g.

```python
d = {tuple([1, 2, 3]): 'value'}
```

## Tuples

like array but immutable

```python
tup = (1, 2, 3)
```

can be used as key's for hash map/set
!! list can't be used as keys

```python
myMap = {(1, 2): 3}

mySet = set()
mySet.add((1, 2))
print((1, 2) in mySet)
```

## DefaultDict

Return a new dictionary-like object. `defaultdict` is a subclass of the built-in dict class.
Provides default value for missing key.

Using list as the default_factory, it is easy to group a sequence of key-value pairs into a dictionary of lists:

```python
s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
d = defaultdict(list)
for k, v in s:
    d[k].append(v)

sorted(d.items())
# [('blue', [2, 4]), ('red', [1]), ('yellow', [1, 3])]
```

Setting the default_factory to int makes the defaultdict useful for counting (like a bag or multiset in other
languages):

```python
s = 'mississippi'
d = defaultdict(int)
for k in s:
    d[k] += 1

sorted(d.items())
# [('i', 4), ('m', 1), ('p', 2), ('s', 4)]
```

## Heap

under the hood are arrays

```python
import heapq

minHeap = []
heapq.heappush(minHeap, 3)
heapq.heappush(minHeap, 2)
heapq.heappush(minHeap, 4)

print(minHeap[0])  # 2 - min value

while len(minHeap):
    print(heapq.heappop(minHeap))
```

no max heap by default, work around is to use min heap and multiply by -1 when push and pop

```python
maxHeap = []
heapq.heappush(maxHeap, -1 * 3)
heapq.heappush(maxHeap, -1 * 2)
heapq.heappush(maxHeap, -1 * 4)

print(-1 * maxHeap[0])  # 2 - min value

while len(maxHeap):
    print(-1 * heapq.heappop(maxHeap))

arr = [2, 1, 3, 4]
heapq.heapify(arr)

while arr:
    print(heapq.heappop(arr))
```

## Functions

```python
def myFunc(m, n):
    return m * n


print(myFunc(2, 3))
```

Nested functions have access to outer variables

```python
def outer(a, b):
    c = "c"

    def inner():
        return a + b + c

    return inner()


print(outer("a", "b"))
```

Can modify objects but not to reassign unless using nonlocal word

```python
def double(val):
    val = val * 2


val = 5
double(val)
print(val)
```

## Class

```python
class MyClass:
    # Constructor
    def __init__(self, nums):
        # Create member variables
        self.nums = nums
        self.size = len(nums)

    def getLength(self):
        return self.size
```

## Csv to Sql

```python
# https://konbert.com/convert/excel/to/csv
import uuid;

f = open("data.csv", "r")
fr = open("result.sql", "w", encoding="utf-8")
for str in f:
    stringData = str.split(';')
    stringData = ["'{0}'".format(element) for element in stringData]
    stringData = [element.replace('\n', '') for element in stringData]
    formattedString = ", ".join(stringData)
    formattedString.replace('\n', ' ').replace('\r', '')

    fr.write(f"('{uuid.uuid4()}', {formattedString}),\n")

f.close()
```