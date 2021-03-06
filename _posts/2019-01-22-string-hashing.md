---
---

I've always wondered how c++ `std::map` works. Turns out its typically
implemented as a *Binary Search Tree (BST for short)*. Why a BST? The
most probable answer I can come up with, is how hashing is
implemented. How? Let's see.

I'll just go over string hashing as I don't know the specifics about
how other data types are hashed. Basically, string hashing is a
process of turning any string into a unique integer that identifies
only that particular string. So, for example *hash(a) != hash(b)* for
any two strings *a* and *b* if *a != b*. In pactice, generating a
completely unique integer is not possible in c++ because of
limitations in integer sizes.

Generating a string hash is quite easy. Here's how we'll do it. There
are other ways to generate a hash.

## Formula

*hash(s) = { s[0] + s[1].p + s[2].p^2 + ... + s[n-1].p^(n-1) } % MOD
m*

> *s* is the target string, *p* is represents power (we'll use the
powers of a prime number) and, *m* denotes a number to MOD with (this
should be as large as possible) and *n* denotes the length of the
string.

## Code

```c++
#include <bits/stdc++.h>
using namespace std;

// NOTE: hashing only for lowercase letters
// In case of uppercase or other letters, set p = 53 and
// change the c-'a'+1 line to something suitable if necessary
long long compute_hash(string const& s) {
    const int p = 31;
    const int m = 1e9 + 9;
    long long hash_value = 0;
    long long p_pow = 1;
    for (char c : s) {
        hash_value = (hash_value + (c - 'a' + 1) * p_pow) % m;
        p_pow = (p_pow * p) % m;
    }
    return hash_value;
}

int main() {
    cout << compute_hash("a") << endl;
    cout << compute_hash("b") << endl;
    cout << compute_hash("c") << endl;
    cout << compute_hash("abc") << endl;
    cout << compute_hash("abcd") << endl;
    cout << compute_hash("sadfasdfasdfadsfasdfasdfasdfasdfas") << endl;
    cout << compute_hash("sadfasdfasdfadsfasdfasdfasdfasdfaa") << endl;
    
    return 0;
}
```

## Output

```
1
2
3
2946
122110
377376292
787694314
```

As can be seen, we're doing `mod` with a certain integer `m` which is
a prime number. Selecting a prime for both `p` and `m` is important
for avoiding collisions (same hash for different strings) as much as
possible. If we didn't mod with 10^9 + 9, it would be modded when the
multiplications overflowed the max integer size.

Now, coming to why `std::map` is implemented as a `BST`. It's because
we can't necessarily take an array of size 10^9 + 9 to store the
hashes for mapping as it will take a lot of memory and the compiler
will also complain about it. We need to come up with a data structure
which can store arbitrary integers and this is where `BST` comes in
handy. A simple implementation might look like this:

## Code

```c++
struct node {
    long long key;
    string value;
};

node BST[100];

long long compute_hash(string const& s);

string search(node *n, string k) {
    long long hash_of_k = compute_hash(k);
    if (hash_of_k exists in BST) return node->value;
    else return "";
}

void insert(node *n, string val) {
    long long hash_of_val = compute_hash(val);
    if (hash_of_val exists in BST) {
        n->value = val;
    } else {
        node new_node;
        new_node->k = compute_hash(val);
        new_node->value = val;
    }
}

int main() {
    node *root = new node;
    
    insert(root, "abc");
    insert(root, "test");

    cout << search(root, "abc") << endl;
    cout << search(root, "hello") << endl;
}
```

Obviously, the `std::map` is implemented in a more sophisticated way.
I've just thought about how it might be represented in a very simple
way.
