[[Python hacks]]

For example, find the minimum and maximum numbers in the range divisible by 3:

```python
a,b = int(input()), int(input())
a += -a%3
b -= b%3
print((a+b)/2)
```

My solution was much worse:

```python
a = int(input())
b = int(input())

start = -(-a // 3 * 3) if a > 0 else -(abs(a) // 3 * 3)
stop = b // 3 * 3 if b > 0 else b // 3 * 3

print((start + stop) / 2)
```