2019-01-01

# FizzBuzz fun with PHP Iterators

Happy new year!

[FizzBuzz](https://en.wikipedia.org/wiki/Fizz_buzz) - that fun little coding exercise beloved of interviewers everywhere. Like all good diversions, it's as much about the journey as the destination... there's no end to the possible implementations. It's a good chance for coders to show how elegant or efficient or [hilariously ridiculous](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition) they can be in (mostly) a few lines.

I thought it was a good chance to use some of PHP's [SPL iterators](http://php.net/manual/en/spl.iterators.php). PHP's [Standard PHP Library (SPL)](http://php.net/manual/en/intro.spl.php) is a collection of classes, interfaces and functions designed to solve common problems. It's definitely worth getting to know some of them as they can be very useful.

Let's take a look:

```php

const MAX_ITERATIONS = 30;

$values = new ArrayIterator([
    'FizzBuzz',
    null,
    null,
    'Fizz',
    null,
    'Buzz',
    'Fizz',
    null,
    null,
    'Fizz',
    'Buzz',
    null,
    'Fizz',
    null,
    null,
]);

$cycle = new InfiniteIterator($values);
$limit = new LimitIterator($cycle, 1, MAX_ITERATIONS);

foreach ($limit as $value) {
    printf("%s\n", $value ?? $limit->getPosition());
}
```

[`ArrayIterator()`](http://php.net/manual/en/class.arrayiterator.php) is an object-oriented wrapper for PHP's `array` for use with iterators and [`LimitIterator()`](http://php.net/manual/en/class.limititerator.php) allows iteration over a limited subset of items in an iterator.

[`InfiniteIterator()`](http://php.net/manual/en/class.infiniteiterator.php) is the most interesting of the three - it allows the infinite cycling over an array of values; e.g:

```php
$cycle = new InfiniteIterator(new ArrayIterator(['foo', 'bar', 'baz']));

foreach ($cycle as $item) {
  echo $item . PHP_EOL;
}
```

This will loop over `['foo', 'bar', 'baz']` until the end of time, or when you cancel the program... whichever comes first :)

> foo
> bar
> baz
> foo
> bar
> baz

Going back to our FizzBuzz example - the `Fizz|Buzz|FizzBuzz` pattern repeats over 15 iterations so we create an `InfiniteIterator()` instance to represent that, adding `null` as a placeholder for numeric values.

Then, when generating the program output we use PHP's null coalesce operator (`??`) to check if the current `$value` is `null`. If the current `$value` is null, [`LimitIterator::getPosition()`](http://php.net/manual/en/limititerator.getposition.php) returns the incrementing numeric position of the inner iterator instead.

And there we have it - Not Another FizzBuzz Implementation (NAFBI). Hopefully this example shows how SPL is full of useful tools (and some silly names like `RecursiveIteratorIterator`) that can perform useful functions in a few lines.

Thanks for reading - until next time!
