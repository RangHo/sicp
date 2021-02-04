# Chapter 1.1: The Elements of Programming

Three ways to control complexity of a program:
- black box abstraction
- conventional interfaces
- metalinguistic abstraction

Programming languages have three common features to achieve such control:
- **primitive elements** (the simplest unit that a programming language supports)
- **means of combination** (simple units come together to form a complex element)
- **means of abstraction** (a combined element is also considered as a unit)

## 1.1.1. Expressions

**Scheme** interpreter will *evaluate* the expression that user provided.

```scheme
42
> 42

(+ 1 1)
> 2

(+ (* 3
      (+ (* 2 4)
         (+ 3 5)))
   (+ (- 10 7)
      6))
> 57
```

## 1.1.2. Naming and the Environment

Scheme's approach on *means of abstraction*: `define`-ing variables. When we use `define`, Scheme interpreter automatically associates the *value* to the *name*.
Associated names are saved in a memory called the **environment** in Scheme.

Example of associating a value to a name:
```scheme
(define pi 3.14) ; pi = 2.14
(define r 10)    ; r = 10
```

Example of associating a compound expression to a name:
```scheme
; circumference = pi * r * r
(define circumference (* pi r r))

circumference ; using the previous definitions
> 314
```

Associating a compound expression `(* pi r r)` to `circumference` :arrow_right: **means of abstraction**!

## 1.1.3. Evaluating Combinations

To evaluate a combination, we need to:
1. evaluate the subexpressions of the combination
2. apply the leftmost subexpression to other subexpresssions

That is, we need to evaluate subexpressions when evaluating an expression. :arrow_right: evaluation is *recursive* in nature!

Stop condition of this recursion are when met with primitive values:
- the values of numerals are the numbers that they name
- the values of built-in operators are the built-in assembly code
- the values of other names are the objects `define`d in the environment

## 1.1.4. Compound Procedures

Just as we can define a variable, we can define a **procedure**:
```scheme
(define (name parameters...) body)
```

Using this, we can define a procedure that squares a number:
```scheme
(define (square x) (* x x))

(square 11)
> 121
```

We can go further by creating a procedure that uses `square` procedure:
```scheme
(define (cube x) (* x (square x)))

(cube 11)
> 1331
```

## 1.1.5. The Substitution Model for Procedure Application

To evaluate a complex procecure, we can use the **substitution model**, where we fully *expand* the procedures down to primitive values, and then combine them again.
For example, evaluating `(cube 11)` would look like this:
```scheme
(cube 11)
-> (* 11 (square 11))
-> (* 11 (* 11 11))
-> (* 11 121)
-> 1331
```

> Note that this *fully-expand-and-reduce* strategy (a.k.a. *normal-order evaluation*) sometimes fails, causing infinite recursion, etc.
> There is a way to overcome this with some fancy Lambda Calculus magic, but here we can introduce an *applicative-order evaluation*,
> where we evaluate the arguments first, and then applying the result to a procedure.

## 1.1.6. Conditional Expressions and Predicates

Consider an absolute value function in mathematics. `abs(x)` returns `x` when `x >= 0` and returns `-x` when `x < 0`.
This is called a *case analysis*, and Scheme has a special construct called `cond` to implement this:
```scheme
(cond (predicate body)
      (predicate body)
      (predicate body)
      ...
      (predicate body))
```

Using this construct, we can implement our own version of `abs(x)`:
```scheme
(define (abs x)
        (cond ((< x 0) (- x))
              (else x)))
```

There is another construct that implement conditional called `if`:
```scheme
(if predicate consequent alternative)
```
In this form, `consequent` is evaluated if `predicate` is true, `alternative` if false.

There are three useful special forms that combine predicates together:
```scheme
; Logical AND
(and pred1 pred2 ... predn)

; Logical OR
(or pred1 pred2 ... predn)

; Logical NOT
(not predicate)
```

## 1.1.7. Example: Square roots by Newton's Method

Newton's method of approximating the square root of a number is to continuously improving our guess by averaging our guess with the number divided by our guess.

```scheme
(define (average x y) (/ (+ x y) 2))
(define (improve guess x) (average guess (/ x guess)))
(define (good-enough? guess x) (< (abs (- (square guess) x)) 0.001))
(define (sqrt-iter guess x)
        (if (good-enough? guess x)
            guess
            (sqrt-iter (improve guess x) x)))
```
