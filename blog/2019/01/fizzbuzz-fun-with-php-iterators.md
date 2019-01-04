2019-01-01

# FizzBuzz fun with PHP Iterators

Or... NAFBI (Not Another FizzBuzz Implementation).

[FizzBuzz](https://en.wikipedia.org/wiki/Fizz_buzz)—that fun little coding exercise seemingly beloved of interviewers everywhere. Like all good diversions, it's as much about the journey as the destination... there's no end to the possible implementations, which means it's a good opportunity for the coder to show how simple, elegant, efficient or [enterprise-ready](https://github.com/EnterpriseQualityCoding/FizzBuzzEnterpriseEdition) they can be in (usually) a few lines.

I thought it was a good opportunity to look at some of PHP's [SPL iterators](http://php.net/manual/en/spl.iterators.php) and how we can compose them to create a short and simple NAFBI.

The [Standard PHP Library (SPL)](http://php.net/manual/en/intro.spl.php) is a collection of classes, interfaces and functions designed to solve common problems. It's definitely worth getting to know some of them as they can be very useful.

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

We're composing three SPL iterators and outputting a result.

[`ArrayIterator()`](http://php.net/manual/en/class.arrayiterator.php) is an object-oriented wrapper for PHP's `array` for use with iterators, and [`LimitIterator()`](http://php.net/manual/en/class.limititerator.php) allows iteration over a limited subset of items in an iterator.

[`InfiniteIterator()`](http://php.net/manual/en/class.infiniteiterator.php) is the most interesting of the three—it allows the infinite cycling over an array of values; e.g:

```php
$cycle = new InfiniteIterator(new ArrayIterator(['foo', 'bar', 'baz']));

foreach ($cycle as $item) {
    echo $item . ' ';
}
```

This will loop over `['foo', 'bar', 'baz']` until the end of time, or when you cancel the program... whichever comes first :)

> foo bar baz foo bar baz foo bar baz foo bar baz

Returning to our NAFBI:

The `Fizz|Buzz|FizzBuzz` pattern repeats over 15 iterations so we wrap an array in an `InfiniteIterator()` instance to represent that, using `null` as a placeholder for numeric values.

Secondly, we wrap the `InfiniteIterator()` in a `LimitIterator()` which will allow us to iterate over the values from `1` until the defined `MAX_ITERATIONS`.

Finally, when generating the program output we use PHP's null coalesce operator (`??`) to check if the current `$value` is `null`. If the current `$value` is null, [`LimitIterator::getPosition()`](http://php.net/manual/en/limititerator.getposition.php) returns the incrementing numeric position of the inner iterator.

This yields:

> 1  
> 2  
> fizz  
> 4  
> buzz  
> fizz  
> 7  
> 8  
> fizz  
> buzz  
> 11  
> fizz  
> 13  
> 14  
> fizzbuzz  
> 16  
> 17  
> fizz  
> 19  
> buzz  
> fizz  
> 22  
> 23  
> fizz  
> buzz  
> 26  
> fizz  
> 28  
> 29  
> fizzbuzz  

FizzBuzz! Hopefully this example shows how SPL iterators can be composed to provide useful functionality in a few lines. Thanks for reading—until next time!
