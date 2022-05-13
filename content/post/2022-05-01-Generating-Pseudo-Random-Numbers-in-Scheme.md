---
author: david

title: Generating Pseudo-Random Numbers in Scheme

date: 2022-05-01T10:31:17.387-04:00
modified: 2022-05-01T16:53:56.081-04:00
tags:
  - Random Numbers

  - functional programming

  - Scheme

  - pseudo-random numbers

  - PRNG

---

Recently, I needed to write a simple simulation in multiple programming languages. For the simulations to operate, they needed a source of random numbers from two statistical distributions: a uniform distribution and a standard distribution.

The numbers from the distributions needed to be "random", but not too random. I needed each generator in each language to produce the same sequence of "pseudo-random" numbers. And I needed the same sequence every time the simulators were run.

There are a number of algorithms to generate such sequences that always produce the same sequence starting from the same "seed".

The one I chose was an oldy but goody. (**NOTE**: This is a pseudo-random number generator. This is not adequate for cryptograhy or other uses where true randomness is required.) 

Back in 1986, Bennet L Fox published ["ALGORITHM 647: Implementation and Relative Efficiency of Quasirandom Sequence Generators" in ACM Transactions on Mathematical Software Volume 12 Issue 4 Dec. 1986 pp 362-376 https://doi.org/10.1145/22721.356187](https://dl.acm.org/doi/10.1145/22721.356187). One of the generators described in the paper produces pseudo-random numbers from a uniform distribution of numbers between 0 and 1. A listing of the software from that paper is available as a compressed file (`.gz`) [here](https://calgo.acm.org/647.gz). Towards the bottom of the file you can find the function `UNIF`:

```fortran
      REAL FUNCTION UNIF(IX)
C
C       PORTABLE PSEUDORANDOM NUMBER
C       GENERATOR IMPLEMENTING THE RECURSION
C
C       IX=16807*IX MOD(2**31-1)
C       UNIF=IX/(2**31-1)
C
C       USING ONLY 32 BITS INCLUDING
C       SIGN
C
C       INPUT:
C        IX =INTEGER STRICTLY BETWEEN
C           0 AND 2** 31 -1
C
C       OUTPUTS:
C        IX=NEW PSEUDORANDOM INTEGER
C           STRICTLY BETWEEN 0 AND
C           2**31-1
C        UNIF=UNIFORM VARIATE (FRACTION)
C             STRICTLY BETWEEN 0 AND 1
C
C      FOR JUSTIFICATION, SEE P. BRATLEY,
C      B.L. FOX, AND L.E. SCHRAGE (1983)
C      "A GUIDE TO SIMULATION"
C      SPRINGER-VERLAG, PAGES 201-202
C
      INTEGER K1,IX
C
      K1 = IX/127773
      IX = 16807* (IX-K1*127773) - K1*2836
      IF (IX.LT.0) IX = IX + 2147483647
      UNIF = IX*4.656612875E-10
C
      RETURN
      END
```

Implementations in other, "traditional" languages like C, Java, Julia, Zig, _etc_. look similar.

For Lisp family languages, I wanted to do something a little more idiomatic. One thing I wanted was better handling of the seed value, `IX` in the listing above. It should not require external storage somewhere, but should be easy to change too. 

The function should not have side effects either. The original function from the paper changes the value of its arguments. Versions that use a global seed variable need to mutate that global as well. Languages, like C, that have "static" variables can maintain changes to variables declared within the function across function invocations, but that makes it hard to use different starting values for the seed.

None of that is a problem in Scheme. The following is my attempt at the same function written in Scheme (Chez Scheme 9.5.8).

```scheme
;; Return a random uniform deviate in the range 0 <= x < 1.0.
(define algo-647-uniform
  (let ([seed 12345])
    (lambda new-seed
      (if (pair? new-seed)
          (set! seed (car new-seed))
          (let* ([k (floor (/ seed 127773))]
                 [partial (- (* 16807 (- seed (* k 127773))) (* k 2836))])
            (if (< partial 0)
                (set! seed (+ partial 2147483647))
                (set! seed partial))
            (* seed 4.656612875e-10))))))
```

Seasoned Schemers probably responded to the above with a "Well, duh!". For those not familiar with Scheme or some other member of the Lisp family of languages, let's walk through it.

`define` _binds_ an identifier to a _location_ in memory, not a value. So, above, the identifier `algo-647-uniform` is bound to the location of the object returned by the `let` form that follows.

The `let` form creates a lexical environment with local variables. In this case `seed` is bound to a location containing the value `12345`. Think of 12345 as the "default" seed, the one that will be used unless some action is taken to change it.

After the bindings, any number of expressions can be evaluated. In the listing, the `lambda` creates a new function (without a name) that takes a single argument, `new-seed`.

In the `lambda`, the arguments (`new-seed`) are passed as a list. If the function is invoked with a single argument, that list is a pair and `seed` is re-assigned to the value `new-seed`.

If the function is invoked without arguments, the `lambda` receives an empty list, `()`, which is not a pair and execution proceeds to generate the next pseudo-random number.

The result of the `let` form is the `lambda` function and the `define` associates the identifier `algo-647-uniform` with the location where the `lambda` is stored.

So, the function can be used two ways:

1. `(algo-647-uniform)`, with no arguments, will return the next pseudo-random number in the sequence.
2. `(algo-647-uniform 999)` will change the value of the seed used in the generator and returns nothing. The seed value can be changed at any time. 

The first 10 calls to `(algo-647-uniform)` with the default seed value of 12345 produce:

```shell
0.09661652850250932
0.8339946273432385
0.9477024976351657
0.0358785949795561
0.011545853228418662
0.051155220272651215
0.7657871677908032
0.5849297393665769
0.9141300529290503
0.7838003894756332
```

A way to generate random numbers from a normal, standard deviate was needed also. For that I chose the method of [Marsaglia](https://en.wikipedia.org/wiki/Marsaglia_polar_method) to generate a standard deviate from numbers provided by a uniform deviate, which we have above.

The Marsaglia Polar Method computes two values at a time. One call returns the first value and the next will return the second.

The Marsaglia method requires maintaining a little more state than the algorithm above, but we use a similar approach to writing the code.

```scheme
;; Return a random standard normal deviate.
(define gaussian-deviate-marsaglia
  (let* ([has-spare #f]
         [spare 0.0]
         [rsq 0.0] [r1 0.0] [r2 0.0]
         [nxt-rsq (lambda ()
                    (set! r1 (- (* 2.0 (algo-647-uniform)) 1.0))
                    (set! r2 (- (* 2.0 (algo-647-uniform)) 1.0))
                    (set! rsq (+ (* r1 r1) (* r2 r2)))
                    rsq)])
    (lambda ()
      (if has-spare
          (begin
            (set! has-spare #f)
            spare)
          (let loop ([_ (nxt-rsq)])
            (if (or (>= rsq 1.0) (= rsq 0.0))
                (loop (nxt-rsq))
                (let ([fac (sqrt (/ (* -2.0 (log rsq)) rsq))])
                  (set! spare (* r1 fac))
                  (set! has-spare #t)
                  (* r2 fac))))))))
```

As before, the identifier is associated with the output of a variation of `let` named `let*`. Unlike `let`, the results of earlier bindings in a `let*` form can be used in later binding calculations in the same form. For example, the bindings for `r1` and `r2` are used in the binding for the `next-rsq` `lambda`.

Also, as before, the identifier is bound to the result of the `let*` form, e.g. the `lambda` that computes the random numbers.
 
In Chez 9.5.8, the first 10 results should be:

```shell
-1.0580380669115383
-0.5790254729247644
0.8434589541668004
-0.6708443571574382
-0.22644041228981196
-0.07818079860601053
-0.7443285279492631
-0.5232388154010481
-0.3300334725931046
0.41341813121639936
```

This technique, creating functions with access to state that was available at the time the function was created, is incredibly useful.

You can create functions that maintain state, but that state is only visible within the function, not outside. The chances of identifier collision are greatly reduced and the state needed by the function cannot be altered by calculations outside of the function.

When the utility of this finally dawned on me in the early days of studying the language, it was another one of those "Aha!" moments that Lisp family languages are so famous for.