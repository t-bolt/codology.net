---
title: "SICP - Solution: Exercise 1.46"
date: 2018-10-29T06:09:58+02:00
draft: false
---

**Exercise 1.46:** Several of the numerical methods described in this chapter are instances of an extremely general computational strategy known as _iterative improvement_. Iterative improvement says that, to compute something, we start with an initial guess for the answer, test if the guess is good enough, and otherwise improve the guess and continue the process using the improved guess as the new guess. Write a procedure `iterative-improve` that takes two procedures as arguments: a method for telling whether a guess is good enough and a method for improving a guess. `Iterative-improve` should return as its value a procedure that takes a guess as argument and keeps improving the guess until it is good enough. Rewrite the `sqrt` procedure of 1.1.7 and the `fixed-point` procedure of 1.3.3 in terms of `iterative-improve`.

**Solution**

```scheme
(define (iterative-improve-A good-enough? improve)
  (define (iter guess)
    (if (good-enough? guess)
        guess
        (iter (improve guess))))
  (lambda (guess) (iter guess)))

(define (iterative-improve good-enough? improve-guess)
  (define (iter guess)
    (let ((next (improve-guess guess)))
      (if (good-enough? guess next)
          next
          (iter next))))
  (lambda (guess) (iter guess)))


; --- sqrt ---

(define (square x) (* x x))

(define (average x y)
  (/ (+ x y) 2))

(define (sqrt x)
  (define (improve-sqrt guess)
    (average guess (/ x guess)))
  (define (good-enough-sqrt? guess next)
    (< (abs (- (square next) x)) 0.001))
  ((iterative-improve good-enough-sqrt? improve-sqrt) 1.0))

(display (sqrt 11))(newline)

; --- fixed-point ---
(newline)
(define tolerance 0.00001)

(define (fixed-point f first-guess)

  (define (close-enough? v1 v2)
    (< (abs (- v1 v2))
       tolerance))

  (define (improve-guess guess)
    (f guess))

  (define (try guess)
    (display guess) (newline)
    (let ((next (improve-guess guess)))
      (if (close-enough? guess next)
          next
          (try next))))

  (try first-guess))

(define (average-damp f)
  (lambda (x)
    (average x (f x))))

(define (sqrt-fixed-point x)
  (fixed-point
   (average-damp
    (lambda (y) (/ x y)))
   1.0))

(display (sqrt-fixed-point 11))(newline)
```