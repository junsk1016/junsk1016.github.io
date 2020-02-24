---
layout: single
title: "Test Posting - Python"
categories:
  - Python
tags:
  - jeykll
  - github
  - Minimalmistakes
  - blog
  - Python
---

Category - PYTHON test!

Example : Fibonacci Function

```python
def Fibonacci_Function(number):
  str = [0, 1]
  a, b = 0, 1
  i = 1
  while i <= number:
    a, b = b, a+b
    str.append(b)
    i = i + 1
  return str

n = Fibonacci_Function(100)
print(n)
```
