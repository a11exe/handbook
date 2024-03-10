# Python

+ [Input & output](#input--output)
+ [Logical operators](#logical-operators)
+ [String functions](#string-functions)
+ [Arithmetic functions](#arithmetic-functions)

## Input & output

```python
print('Hello world!')
print("Hello world!")
print("""Hello world!""")
print("Program print 'Hello world!'")

# print
name = "User"
print("Hello,", name, ".") # Hello, User .
print("Hello,", name, ".", sep="") # Hello,User.
print(f"Hello, {name}.") # Hello, User.

# adding zeroes
print(f"{123:0>9}") # 000000123
print(f"{123:0<9}") # 123000000
print(f"{123:0^9}") # 000123000

# string operations
print("-" * 10) # ----------

# string to int (see also float(), str())
n_1 = "1"
n_2 = "2"
print(n_1 + n_2) # 12
n_1 = int(n_1)
n_2 = int(n_2)
print(n_1 + n_2) # 3

# operations
print(f"{2 ** 0.5:.2f}") # 1.41
print(10 // 3) # 3 Floor division
print(10 % 3) # 1 Modulus
last_digit = 1234 % 10 # 4
hour = 9
min = 5
print(f"{hour:02d}:{min:02d}") # 09:05
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
```

## String functions
+ `len()` - string length

## Arithmetic functions
+ `min("y", "c", "z")` # 1
+ 
+ `max(1, 2)` # 2
+ `abs(-10)` # 10 