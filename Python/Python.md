# Python

+ [Input & output](#input--output)

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
```