
# Vertically slice/rotate a multidimensional PHP array with `array_map` and `call_user_func_array`

Yesterday, while trawling through the `array_map()` entry in the PHP documentation I noticed the following line:

> An interesting use of this function is to construct an array of arrays, which can be easily performed by using NULL as the name of the callback function

Interesting indeed. In other words:

```php
<?php

$a = array('a', 'b', 'c');
$b = array('A', 'B', 'C');
$c = array(1, 2, 3);

$result = array_map(null, $a, $b, $c);
print_r($result);
```

Outputs:

```
Array
(
    [0] => Array
        (
            [0] => 'a'
            [1] => 'A'
            [2] => 1
        )
    [1] => Array
        (
            [0] => 'b'
            [1] => 'B'
            [2] => 2
        )
    [2] => Array
        (
            [0] => 'c'
            [1] => 'C'
            [2] => 3
        )
)
```

Cool!

In short, it’s a quick and painless way to rotate/vertically slice a bunch of data - just feed array_map() any number of arrays, and create a new multidimensional array that groups the values of the original arrays by their original element position.

## However...

A glaring limitation of this however is that it’s a bit manual - you must add each array explicitly. How can we vary the number of arrays passed as arguments dynamically?

## Another example

Let’s find a solution for this, taking a slightly different example: a multidimensional array containing various translations of the numbers 1, 2, 3.

Like so:

```php
$data = array(
    array(1, 2, 3),
    array('one', 'two', 'three'),
    array('aon', 'do', 'tri'),
    array('un', 'dos', 'tres'),
);
```

Let’s also imagine that there might be an arbritary number of different translations at run-time: French, German, Japanese etc. This might serve to be a better real-world representation of, say, a data set that was returned from a database call.

## Who you gonna call!?

Not for the first (or last) time, `call_user_func_array()` bounds to the rescue.

To clarify, `call_user_func_array()` is used to "call a callback with an array of parameters". Here’s a really basic example to illustrate what this means in practice:

```php
<?php

function adder($a, $b)
{
    return $a + $b;
}

$result = call_user_func_array('adder', array(1, 2));
echo $result; // 3
```

The first argument is a PHP callable, and the second value is an array, the elements of which are mapped to the argument list of the callback. This is equivalent in the above example to invoking `adder(1, 2);`.

## Solution

We can almost use the `$data` array as-is. Look at the `array_map()` function signature and remember that we need to pass null as the first argument:

```
array array_map ( callable $callback , array $arr1 [, array $... ] )
```

So, as a preceding step, prepend a null to the data array... and then:

```
array_unshift($data, null); /* or use the array union operator: array(null) + $data */
$result = call_user_func_array('array_map', $data);
print_r($result);
```

Viola!

```
Array
(
    [0] => Array
        (
            [0] => 1
            [1] => 'one'
            [2] => 'aon'
            [3] => 'un'
        )
    [1] => Array
        (
            [0] => 2
            [1] => 'two'
            [2] => 'do'
            [3] => 'dos'
        )
            /* .... and so on .... */
)
```

## Epilogue

As a real-world example, it’s pretty useful for example if you want to create series of numeric data that you’re going to JSONify and display as grouped/stacked bars in a JavaScript charting tool.

It’s also pretty forgiving - if an array is shorter than the others PHP will square things up by adding null values where appropriate.

It’d be interesting to test this out more and see if there what limits or performance issues pop up - playing with large arrays can get slow and some of the array_* functions do not have a great reputation for efficiency. However for a simple use case like above it’s a quick, neat way to avoid doing horrible nested loops to twist your data :)
