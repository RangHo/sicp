# Exercise solutions from Chapter 1.1

## Exercise 1.1 solution

``` scheme
10 ; -> 10

(+ 5 3 4) ; -> 12

(- 9 1) ; -> 8

(/ 6 2) ; -> 3

(+ (* 2 4) (- 4 6)) ; -> 6

(define a 3)
(define b (+ a 1))
(+ a b (* a b)) ; -> 19

(= a b) ; -> #f

(if (and (> b a) (< b (* a b)))
    b
    a) ; -> 4

(cond ((= a 4) 6)
      ((= b 4) (+ 6 7 a))
      (else 25)) ; -> 16

(+ 2 (if (> b a) b a)) ; -> 6

(* (cond ((> a b) a)
         ((< a b) b)
         (else -1))
   (+ a 1)) ; -> 16

```

## Exercise 1.2 solution

``` scheme
(/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 5)))))
   (* 3 (- 6 2) (- 2 7)))
```

## Exercise 1.3 solution

```scheme
(define (square x) (* x x))
(define (sum-of-square x y) (+ (square x) (square y)))
(define (sum-of-squares-of-largest-two x y z)
        (cond ((and (<= x y) (<= x z)) (sum-of-square y z))
              ((and (<= y x) (<= y z)) (sum-of-square x z))
              (else (sum-of-square x y))))
```

## Exercise 1.4 solution

When evaluating the leftmost expression (`if (> b 0) + -)`, the result is not a value; rather, the evaluated result is a built-in procedure.
Thus, depending on the value of `b`, we choose whether `+` or `-` to be applied to `a` and `b`.

## Exercise 1.5 solution

When the interpreter is using *applicative-order evaluation*, the following would happen (`->` denotes applicative order evaluation):
```scheme
(test 0 (p))
-> (test 0 (p))
-> (test 0 (p))
-> ...
```
Since the procedure `p` evaluates to the evaluation of `p` itself, using applicative-order evaluation creates an infinite recursion.
Thus, `test` never evaluates to a value.

When the interpreter is using *normal-order evaluation*, the following would happen (`=>` denotes normal order evaluation):
```scheme
(test 0 (p))
=> (if (= 0 0) 0 (p))
=> (if #t 0 (p))
=> 0
```
Assuming that `if` is a special construct that conditionally evaluate a value depnding on the value of the predicate, the infinite-recursing procedure `p` is never evaluated.
Thus, `test` successfully evaluates to `0`.

## Exercise 1.6 solution

When Alyssa tries to use this program, the `sqrt-iter` procedure using `new-if` would never return.
As demonstrated in the [solution to Exercise 1.5](#exercise-15-solution), `new-if` is a regular procedure, thus follows the *applicative-order evaluation* rule of typical LISP interpreters.
this causes `sqrt-iter` to be evaluated within `sqrt-iter` calls without bounds, causing infinite recursion.

## Exercise 1.7 solution

In the text, we used the absolute tolerance of `0.001`. For *sane* whole numbers this value works just fine, but when we are dealing with small values, the tolerance itself may be too big to properly detect if the guess is actually good enough. For instance, attempting to calculate `(sqrt 0.0001)` gives the following result:
``` scheme
> (sqrt 0.0001)
0.0323084483304812
```

The answer to the expression above is fairly trivial -- it should be `0.01` -- but the value returned by our interpreter is far from being correct.

On the other hand, since computers represent numbers with floating point integer with fixed length of bytes, it may be impossible for the processor to represent comparatively small differences. In that case, the *good enough* check would always fail as the difference between the guess and the square of number remains larger than our absolute tolerance.

We can fix this by omitting tolerance check altogether and forcing the interpreter to improve the guess until no further improvement can be done, but depending on situations, that would be horribly inefficient. So, we can look at the differences *between* guesses and apply some tolerance there.
``` scheme
;; Mark the guess good enough if the improvement is within 0.1% range
;; of the previous guess
(define (good-enough? guess x)
  (< (abs (- (improve guess x) guess))
  (* guess 0.001)))
```

## Exercise 1.8 solution

Just like the [solution to Exercise 1.7](#exercise-17-solution), we can use the same structure.
``` scheme
;; Square the given number
(define (square x)
  (* x x))

;; Improve using Newton's method
(define (improve guess x)
  (/ (+ (/ x (square guess)) (* 2 guess)) 3))

;; Check if the guess is tolerable
(define (good-enough? guess x)
  (< (abs (- (improve guess x) guess))
  (* guess 0.001)))

;; Recursive function
(define (cbrt-iter guess x)
  (if (good-enough? guess x)
      guess
      (cbrt-iter (improve guess x) x)))

;; Exported form
(define (cbrt x)
  (cbrt-iter 1.0 x))
```
