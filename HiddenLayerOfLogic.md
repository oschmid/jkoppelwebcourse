## The Three Levels of Software

A select in a schemaless DB like MongoDB that expects a field that was never inserted.

## The Design of Software is a Thing Apart

`return n == 13;` could be a check for whether something is a baker’s dozen or whether a number is unlucky.

## Painless Functional Specs

The design spec is the consensus building document of the whole project. According to Spolsky almost every decision and piece of work both contributes and comes out of the spec. These include help documents written by technical writers, marketing documents, product development sales pitches for investors, UI mockups, test plans, use cases and scenarios, non-goals, open issues, software architecture diagrams, and the actual software.

## Optional: Modules Matter Most

In Java, having multiple interfaces for the same implementation would require adapter classes for receiving say UDP and TCP messages to both interact with the same microservice implementation.

## Exercise 1

Any operation that doesn't reduce z (subtraction, division, negation, etc) will not alter the postcondition. Allowable operations could be addition, multiplication, or operations not on z.

"Forgetting" assertions corresponds to having another module operate on lower level assertions while we concern ourselves with higher level assertions guaranteed by that module's spec.

## Exercise 2

```
{a=4}
b := 2 - a
{b = 2}
c := b * 2
{c = 4}
d := c + 1
{d = 5}
```

## Exercise 3

```
{true}
d := (2-(a+1)/a)/2;
{((a<=0) => d=1) /\ ((a>0) => d=0)}
m := d*2+(1-d)*3;
{((a<=0) => m=2) /\ ((a>0) => m=3)}
x := b*2
{(x=2b) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
{(2x=4b) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
x := x*2
{(x=4b) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
{(mx=4bm) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
x := m*x
{(x=4bm) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
{(x+1=4bm+1) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
x := x+1
{(x=4bm+1) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
{((a<=0) => x=8*b+1) /\ ((a>0) => x=12*b+1)}
```

## Exercise 4

I reordered the statements (as much as possible, while still maintaining control flow) and can see that this version has a smaller abstract state. But I’m not sure how to use the consequence rule and still get to the final postcondition.

```
{true}
x := b*2;
{x=2b}
{2x=4b}
x := x*2;
{x=4b}
d := (2-(a+1)/a)/2;
{(x=4b) /\ ((a<=0) => d=1) /\ ((a>0) => d=0)}
m := d*2+(1-d)*3;
{(x=4b) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
{(mx=4bm) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
x := m*x;
{(x=4bm) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
{(x+1=4bm+1) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
x := x+1;
{(x=4bm+1) /\ ((a<=0) => m=2) /\ ((a>0) => m=3)}
{((a<=0) => x=8*b+1) /\ ((a>0) => x=12*b+1)}
```

## Exercise 5

```
{true}
i := 0
{ arr[i]==val || (i<=n /\ forall j, (j >= 0 && j<i) => arr[j] != val) }
while arr[i] != val && i < n do
    i := i+1
    { j=j+1 }
end
{ arr[i] == val || (i==n /\ forall j, (j >= 0 && j<i) => arr[j] != val) }
{ arr[i] == val || (forall j, (j >= 0 && j<n) => arr[j] != val) }
```

## Optional: Code vs Logical Correctness

1. 30 or 31
1. If we call sieve(6), then sieve(-5), and finally sieve(5), sieve will incorrectly return 3 instead of 2. Adding a precondition to sieve that n>0
1. ```void sieve(int n) {
    // {n>0}
    int *mem = (int*)malloc(sizeof(int)*n); // assume no overflow
    // {forall k, (k>=0 && k<n)=>mem[k]==0}
    int count = 0
    // {count=0}
    for (int d=2; d<n; d++) {
        // TODO
        if(mem[d]==0) count++;
        for(int j=d; j<n; j+=d) {
            mem[j]=1;
        }
        // 
        free(mem);
    }
    // 
    printf(“%d\n”, count);
    // { count is the number of primes < n, count has been printed }
}
```

## Optional Case Study: Android Main Thread

1. ```
public static void prepareMainLooper() {
    prepare(false);
    synchronized (Looper.class) {
        if (sMainLooper != null) {
            throw new IllegalStateException(“The main Looper has already been prepared.”);
        }
        sMainLooper = myLooper();
        // { isMainThread = true }
    }
}
```
1. `{ (Looper.myLooper() == Looper.getMainLooper()) => (isMainThread == true) }`
1. Instrumentation.java checks that Looper.myLooper() == Looper.getMainLooper() in validateNotAppThread()
   DynamicAnimation.java makes similar checks that myLooper() != getMainLooper() in start() and cancel().
1. { isMainThread }
   Instrumentation.validateNotAppThread()
   { throws RuntimeException if isMainThread }

   { isMainThread }
   start()/cancel()
   { throws AndroidRuntimeException if !isMainThread }

   Callers of these methods also get a postcondition that an exception is thrown if isMainThread is not the expected value. All transitive callers should also get a postcondition that they can terminate with a thrown exception. Catching that exception would be a way of learning the value of isMainThread.
1. They know that Looper has up to 2 threads and if it has only one then we’re on the main thread.
1. A boolean method Looper.isOnMainThread()?
1. TODO
1. TODO
1. Apps must check for which thread they’re on before doing GUI actions, or when starting long running processes. If they’re not on the expected thread they also have to handle communicating with the main thread. TODO complexity?

## Optional: The DIF Model of Bugs

(Before I saw the semi-colon I thought the uninitialized array on line 2 was the bug.)

1. I’d want a printout of seats, probably before the loop, i at each step of the loop where seats[i] == null, and then the count after the loop. While trying to print out each step of the loop I’d add {}’s and notice the ; defect.
1. 5.5: {seats was initialized and is not null}
   8:  {count=0}
       {i=0}
   11:{count is the number of elements of seats equal to null}
      {i==seats.length}
1. My first printout would tell me whether ‘seats’ was initialized. The printout of count after the loop would show that it’s still 0 and was not added to as expected.
1. The infection begins between lines 9 and 10 where we expect 0<=i<n but because of the semicolon defect the loop runs to completion and i==n. So we see a fault when Java throws an ArrayOutOfBoundsException on line 10.
1. Hoare triples show the logic level abstract state of the software, infections are when the actual state deviates from these expected values, and printouts can show values of a specific run of the program which can be compared to expected abstract straight and show whether or not it has deviated.

## Research Corner: The Programmer’s Apprentice

I think there’s a mistake in the sentence: “On the right, they have a high-level representation of pushing onto a stack, which corresponds to the low-level representation on the **right.**” Should **right** be **left**?

