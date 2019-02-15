2019-02-15

# RangeError: Maximum call stack size exceeded

Seen in [Cork](https://en.wikipedia.org/wiki/Cork_(city)), Ireland today:

```
RangeError: Maximum call stack size exceeded
    at Tooth.brushTeeth (repl:1:13)
    at Tooth.brushTeeth (repl:1:18)
    at Tooth.brushTeeth (repl:1:18)
    at Tooth.brushTeeth (repl:1:18)
    at Tooth.brushTeeth (repl:1:18)
    at Tooth.brushTeeth (repl:1:18)
    at Tooth.brushTeeth (repl:1:18)
    at Tooth.brushTeeth (repl:1:18)
    at Tooth.brushTeeth (repl:1:18)
    at Tooth.brushTeeth (repl:1:18)
>
```
![](images/self_brushing.jpg)

Always remember to brush your teeth, but also, always remember to include a base case!
