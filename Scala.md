### Tail call recursion

In traditional recursion, the typical model is that you perform your recursive calls first, and then you take the return value of the recursive call and calculate the result. In this manner, you don't get the result of your calculation until you have returned from every recursive call.
In tail recursion, you perform your calculations first, and then you execute the recursive call, passing the results of your current step to the next recursive step. This results in the last statement being in the form of (return (recursive-function params)). Basically, the return value of any given recursive step is the same as the return value of the next recursive call.


Consider a simple function that adds the first N natural numbers. (e.g. sum(5) = 1 + 2 + 3 + 4 + 5 = 15).

Here is a simple JavaScript implementation that uses recursion:

```
function recsum(x) {
    if (x === 1) {
        return x;
    } else {
        return x + recsum(x - 1);
    }
}
```

If you called recsum(5), this is what the JavaScript interpreter would evaluate:

```
recsum(5)
5 + recsum(4)
5 + (4 + recsum(3))
5 + (4 + (3 + recsum(2)))
5 + (4 + (3 + (2 + recsum(1))))
5 + (4 + (3 + (2 + 1)))
15
```

Note how every recursive call has to complete before the JavaScript interpreter begins to actually do the work of calculating the sum.

Here's a tail-recursive version of the same function:
```
function tailrecsum(x, running_total = 0) {
    if (x === 0) {
        return running_total;
    } else {
        return tailrecsum(x - 1, running_total + x);
    }
}
```
Here's the sequence of events that would occur if you called tailrecsum(5), (which would effectively be tailrecsum(5, 0), because of the default second argument).

```
tailrecsum(5, 0)
tailrecsum(4, 5)
tailrecsum(3, 9)
tailrecsum(2, 12)
tailrecsum(1, 14)
tailrecsum(0, 15)
15
```
