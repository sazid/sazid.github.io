---
---


# Diffie-Hellman Algorihtm

Assume, there are two users Alice and Bob. They both want to exchange some
information with each other but they don't want others to eavsedrop on what they
want to talk about. So they decided to encrypt each other's messages. But there
is a problem - how will they share the encryption key (which they'll use to
encrypt their messages) with each other without others getting hold of it?

[YouTube - A complete overview of SSL/TLS and its cryptographic system](https://youtu.be/-f4Gbk-U758?t=730)

[Wikipedia](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)

This is where the Diffie-Hellman algorithm comes in. At first, both Alice and
Bob share two numbers that are accessible publicly.

```text
g - base
p - modulus (should be a prime number)
```

Then, each of them picks a secret number.

```text
Alice picks the secret number - a
Bob picks the secret number - b
```

After they each select a secret number, they perform the following operation
to get a new number - A and B.

```text
For Alice, A = (g^a) mod p
For Bob,   B = (g^b) mod p
```

Next, they send this number to each other publicly.

Note that, even if they share this number publicly, its almost impossible to
guess what's the secret number each of them picked because of loss of
information when modded with 'p'.

After the exchange, Alice now has Bob's public number/key - B and Bob has
Alice's public number/key - A. They apply the following operation to get a new
number that is exactly the **same**.

```text
For Alice, S = (B^a) mod p
For Bob,   S = (A^b) mod p
```

The number S will be the same for both of them as mentioned earlier!

```python
#!/usr/bin/env python

def diffie_hellman(g: int, p: int, a: int, b: int):
    A = (g**a) % p
    B = (g**b) % p

    S1 = (B**a) % p
    S2 = (A**b) % p

    assert S1 == S2, "Incorrect implementation, both S1 and S2 should be equal"

    return S1


def main():
    import random

    g = 2
    p = 97

    for i in range(10):
        a = random.randint(1, 100)
        b = random.randint(1, 100)
        print(diffie_hellman(g, p, a, b))


if __name__ == "__main__":
    main()
```

Output

```text
79
6
75
1
24
70
9
1
81
61
```
