
## Logarithms
Logarithms (or *logs* for short) are the inverse/flip of exponentials. They answer, how many times do we multiply something to get a given number. "How many 10s do we multiply together to get 10000?". The answer to this question is: log<sub>10</sub>10000

Logs take the following form:

> log<sub>base</sub>x = y

where for our example:
base = 10,
x = 10000,
y = 4 (answer)


```python
# We multiply 10s 4 times together to get 10000
print(10*10*10*10)
print(2*2*2*2*2)

from math import log, ceil
print(log(10000, 10)) # how many times do we multiply 10 together to get 10000?
print(log(32, 2)) # how many times do we multiply 2 together to get 32?
```

    10000
    32
    4.0
    5.0

