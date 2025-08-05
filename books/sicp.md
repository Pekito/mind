# SICP

## Exercises from Chapter 1

### Exercise 1.1: Below is a sequence of expressions. What is the result printed by the interpreter in response to each expression? Assume that the sequence is to be evaluated in the order in which it is presented.

```scheme
10
(+ 5 3 4)
(- 9 1)
(/ 6 2)
(+ (* 2 4) (- 4 6))
(define a 3)
(define b (+ a 1))
(+ a b (* a b))
(= a b)
(if (and (> b a) (< b (* a b)))
    b
    a)
(cond ((= a 4) 6)
      ((= b 4) (+ 6 7 a))
      (else 25))
(+ 2 (if (> b a) b a))
(* (cond ((> a b) a)
         ((< a b) b)
         (else -1))
   (+ a 1))
```

Answer

```scheme
10
12
8
3
6
3
()
()
19
false
4
16
6
16
```

----

### Translate the following expression into prefix form'

![image Exercise 1.2](./assets/exercise_1_2.png)

```scheme
( / (
    + 5 4 (
        - 2 (- 3 (+ 6 (/ 4 5)))    
        )
    ) 
    (
        * 3 (- 6 2) (- 2 7)
    )
)
```

----

### Exercise 1.3: Define a procedure that takes three numbers as arguments and returns the sum of the squares of the two larger numbers.

```scheme
(define (square (* x x)))
(define (square_sum x y) (+ (square x) (square y)))
(define (sum_of_largest x y z) (
    cond (
        ((and (>= x y) (>= y z)) (square_sum x y))
        ((and (>= y x) (>= x z)) (square_sum x y))
        ((and (>= z y) (>= y x)) (square_sum z y))
        ((and (>= y z) (>= z x)) (square_sum z y))
        ((and (>= x z) (>= z y)) (square_sum x z))
        ((and (>= z x) (>= x y)) (square_sum x z))
    )
))
```

----

### Exercise 1.4: Observe that our model of evaluation allows for combinations whose operators are compound expressions. Use this observation to describe the behavior of the following procedure:

```scheme
(define (a-plus-abs-b a b)
  ((if (> b 0) + -) a b))
```

If b is greater than 0, it performs + a b, if not, - a b.

----
### Exercise 1.5: Ben Bitdiddle has invented a test to determine whether the interpreter he is faced with is using applicative-order evaluation or normal-order evaluation. He defines the following two procedures:

```scheme
(define (p) (p))

(define (test x y) 
  (if (= x 0) 
      0 
      y))
```

Then he evaluates the expression

```scheme
(test 0 (p))
```

What behavior will Ben observe with an interpreter that uses applicative-order evaluation? What behavior will he observe with an interpreter that uses normal-order evaluation? Explain your answer. (Assume that the evaluation rule for the special form if is the same whether the interpreter is using normal or applicative order: The predicate expression is evaluated first, and the result determines whether to evaluate the consequent or the alternative expression.)

----

### Exercise 1.6: Alyssa P. Hacker doesn’t see why if needs to be provided as a special form. “Why can’t I just define it as an ordinary procedure in terms of cond?” she asks. Alyssa’s friend Eva Lu Ator claims this can indeed be done, and she defines a new version of if:

```scheme
(define (new-if predicate 
                then-clause 
                else-clause)
  (cond (predicate then-clause)
        (else else-clause)))
```

Eva demonstrates the program for Alyssa:

```scheme
(new-if (= 2 3) 0 5)
5

(new-if (= 1 1) 0 5)
0
```

Delighted, Alyssa uses new-if to rewrite the square-root program:

```scheme
(define (sqrt-iter guess x)
  (new-if (good-enough? guess x)
          guess
          (sqrt-iter (improve guess x) x)))
```

What happens when Alyssa attempts to use this to compute square roots? Explain.

As stated before, Lisp procedures always evaluate the params before proceding, so it will cause the program to run indefinitely since sqrt-iter is a function and new-if will evalute it forever.

### Exercise 1.7: The good-enough? test used in computing square roots will not be very effective for finding the square roots of very small numbers. Also, in real computers, arithmetic operations are almost always performed with limited precision. This makes our test inadequate for very large numbers. Explain these statements, with examples showing how the test fails for small and large numbers. An alternative strategy for implementing good-enough? is to watch how guess changes from one iteration to the next and to stop when the change is a very small fraction of the guess. Design a square-root procedure that uses this kind of end test. Does this work better for small and large numbers?

```scheme

(define (good-enough? old-guess new-guess)
  (< (/ (abs (- new-guess old-guess)) old-guess) 0.001))

(define (sqrt-iter currentGuess lastGuess x) 
  (if (good-enough? currentGuess lastGuess) 
      currentGuess
      (sqrt-iter (improve currentGuess x) currentGuess x)))
(define (sqrt x)
    (sqrt-iter 1.0 1.0 x)
)
```

----

### Exercise 1.8: Newton’s method for cube roots is based on the fact that if y is an approximation to the cube root of x, then a better approximation is given by the value `((x/y^2) +2y) / 3`. Use this formula to implement a cube-root procedure analogous to the square-root procedure. (In 1.3.4 we will see how to implement Newton’s method in general as an abstraction of these square-root and cube-root procedures.)

```scheme

(define (good-enough? old-guess new-guess)
  (< (/ (abs (- new-guess old-guess)) old-guess) 0.001))

(define (improve guess x)
  (/ (+ (/ x (square guess)) (* 2 guess)) 3))

(define (cube x)
  (* x x x))

(define (cube-root-iter current-guess last-guess x)
  (if (good-enough? current-guess last-guess)
      current-guess
      (cube-root-iter (improve current-guess x) current-guess x)))

(define (cube-root x)
  (cube-root-iter 1.0 1.0 x))


```

### Exercise 1.9: Each of the following two procedures defines a method for adding two positive integers in terms of the procedures inc, which increments its argument by 1, and dec, which decrements its argument by 1.

```scheme
(define (+ a b)
  (if (= a 0) 
      b 
      (inc (+ (dec a) b))))

(define (+ a b)
  (if (= a 0) 
      b 
      (+ (dec a) (inc b))))
```

Using the substitution model, illustrate the process generated by each procedure in evaluating (+ 4 5). Are these processes iterative or recursive?

This definition is recursive

```scheme
(define (+ a b)
  (if (= a 0) 
      b 
      (inc (+ (dec a) b))))


(+ 3 4)
  (inc (+ 2 4))
    (inc (+ 1 4))
      (inc (+ 0 4))
        4
      (inc 4) -> 5
    (inc 5) -> 6
  (inc 6)
7
      

(define (+ a b)
  (if (= a 0) 
      b 
      (+ (dec a) (inc b))))
```

The second definition is iterative

```scheme
(+ 3 4)
(+ 2 5)
(+ 1 6)
(+ 0 7)
7
```

### Exercise 1.10: The following procedure computes a mathematical function called Ackermann’s function.

```scheme
(define (A x y)
  (cond ((= y 0) 0)
        ((= x 0) (* 2 y))
        ((= y 1) 2)
        (else (A (- x 1)
                 (A x (- y 1))))))
```

What are the values of the following expressions?

```scheme
(A 1 10)
(A 2 4)
(A 3 3)
```

Consider the following procedures, where A is the procedure defined above:

```scheme
(define (f n) (A 0 n))
(define (g n) (A 1 n))
(define (h n) (A 2 n))
(define (k n) (* 5 n n))
```

Give concise mathematical definitions for the functions computed by the procedures f, g, and h for positive integer values of n
. For example, (k n) computes 5n2

#### Answer

```scheme
(A 1 10)
(A 0 (A 1 9))
(A 0 (A 0 (A 1 8)))
(A 0 (A 0 (A 0 (A 1 7))))
(A 0 (A 0 (A 0 (A 0 (A 1 6)))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 1 5))))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 1 4)))))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 1 3))))))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 1 2)))))))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 1 1))))))))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 2)))))))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 4))))))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 0 (A 0 8)))))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 0 16))))))
(A 0 (A 0 (A 0 (A 0 (A 0 32)))))
(A 0 (A 0 (A 0 (A 0 64))))
(A 0 (A 0 (A 0 128)))
(A 0 (A 0 256))
(A 0 512)
1024
```

```scheme
(A 2 4)
(A 1 (A 2 3))
(A 1 (A 1 (A 2 2)))
(A 1 (A 1 (A 1 (A 2 1))))
(A 1 (A 1 (A 1 2)))
(A 1 (A 1 (A 0 (A 1 1))))
(A 1 (A 1 (A 0 2)))
(A 1 (A 0 (A 1 3)))
(A 1 (A 0 (A 0 (A 1 2))))
(A 1 (A 0 (A 0 (A 0 (A 1 1)))))
(A 1 (A 0 (A 0 (A 0 2))))
(A 1 (A 0 (A 0 4)))
(A 1 (A 0 8))
(A 1 16)
(A 0 (A 1 15))
(A 0 (A 0 (A 1 14)))
(A 0 (A 0 (A 0 (A 1 13))))
(A 0 (A 0 (A 0 (A 0 (A 1 12)))))
(A 0 (A 0 (A 0 (A 0 (A 0 (A 1 11))))))
[...]

```

```scheme

(A 3 3)

(A 2 (A 3 2))
(A 2 (A 2 (A 3 1)))
(A 2 (A 2 2))


(A 2 2)
(A 1 (A 2 1))

(A 1 2)
(A 0 (2))
```

`(define (f n) (A 0 n))` will define a function f(n) = n*2, since (= x 0) -> (* 2 y) \
`(define (g n) (A 1 n))` will define a function f(n) = n^2\
`(define (h n) (A 2 n))` will define a function that elevates a number to the tetration of 2 at height n
