---
layout: post
author: Xuanrui
title: A Leaky Implementation of Natural Numbers in C
location: Somerville, MA
---

Natural numbers are mathematically defined inductively as $$n = zero \mid succ(n)$$. 
This is the intuitive definition used in many languages like Coq, Idris and Agda, and 
can be implemented easily in languages like OCaml. On the other hand, I also did it
in C just for fun. The code is here:

```c
#include <stdlib.h>
#include <stdio.h>
#include <assert.h>

typedef enum tag tag;
typedef struct nat nat;

enum tag { ZERO, SUCC };
struct nat {
    tag tag;
    nat *pred;
};
```

Because I can't usually pass stack pointers around (it's bad and prone to bugs), all 
natural numbers have to be allocated on the stack. Also, because C doesn't have recursive 
data structures, we have to do it with a pointer.

```c
nat *succ(nat *n)
{
    nat *successor = malloc(sizeof(nat));
    successor->tag = SUCC;
    successor->pred = n;
    return successor;
}

nat *pred(nat *n)
{    
    if (n->tag == ZERO) {
        return NULL;
    } else {
        return n->pred;
    }
}

nat *add(nat *m, nat *n)
{
    if (n->tag == ZERO) {
        return m;
    } else {
        return succ(add(m, n->pred));
    }
}

nat *mult(nat *m, nat *n)
{
    if (n->tag == ZERO) {
        nat *z = malloc(sizeof(nat));
        z->tag = ZERO;
        z->pred = NULL;
        return z;
    } else if ((n->pred)->tag == ZERO) {
        return m;
    } else {
        return add(mult(m, n->pred), m);
    }
}

int from_nat(nat *n)
{
    if (n->tag == ZERO) {
        return 0;
    } else {
        return 1 + from_nat(n->pred);
    }
}

nat *to_nat(int n)
{
    if (n < 0) {
        return NULL;
    } else if (n == 0) {
        nat *z = malloc(sizeof(nat));
        z->tag = ZERO;
        z->pred = NULL;
        return z;
    } else if (n == 1) {
        nat *one = malloc(sizeof(nat));
        one->tag = SUCC;
        one->pred = to_nat(0);
        return one;
    } else {
        nat *this = malloc(sizeof(nat));
        this->tag = SUCC;
        this->pred = to_nat(n-1);
        return this;
    }
}

void free_nat(nat *n)
{
    if (!n) {
        return;
    } else if (n->tag == ZERO){
        free(n);
    } else {
        free_nat(n->pred);
        free(n);
    }
}
```

Finally, we could test this implementation by calculating the value of 3 * 4, which should be 12.
```c
int main(void)
{
    nat *three = to_nat(3);
    nat *four = succ(three);
    nat *twelve = mult(three, four);
    printf("Product of %d and %d is: %d\n", from_nat(three), from_nat(four),
           from_nat(twelve));
    free_nat(twelve);
    return 0;
}

```

The implementaion is still leaky, but not by very much. However, it's functionally correct!
