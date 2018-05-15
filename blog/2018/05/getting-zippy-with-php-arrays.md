2018-05-13

# Getting zippy with PHP arrays

## Introduction

`zip` - not to be confused with the archive format - is a useful merging operation that is a native feature in many languages including Python, Ruby and functional languages such as Elixir and Haskell.  

Exact behaviour varies by language but, generally speaking, `zip` combines multiple input sequences into an output sequence of values grouped by their corresponding index.

## Examples

This is best illustrated by example. Python's native [`zip()`](https://docs.python.org/3.3/library/functions.html#zip) function combines one or more lists and groups their values into a list of tuples; e.g:

```python
zip([1, 2], ['a', 'b'], ['A', 'B'])

# [
#   (1, 'a', 'A'),
#   (2, 'b', 'B')
# ]
```

Elixir - which has two slightly different implementations, as illustrated below - also zips lists into tuples; e.g:

```elixir
# Enum.zip/1 accepts an iterable 
# containing an arbitrary but 
# finite number of iterables
Enum.zip [[1, 2], [:a, :b], [:A, :B]]

# [
#   {1, :a, :A}, 
#   {2, :b, :B}
# ]

# Enum.zip/2 accepts exactly two iterables
Enum.zip [1, 2], [:a, :b]

# [
#   {1, :a}, 
#   {2, :b}
# ]

```

Ruby takes a predictably OOP approach to combine values into an array of arrays; e.g:

```
[1, 2].zip [:a, :b], [:A, :B]

# [
#   [1, :a, :A], 
#   [2, :b, :B]
# ]
```

JavaScript doesn't have native `zip` functionality (or tuples) but utility libraries such as [Lodash](https://lodash.com/docs/4.17.10#zip) provide the functionality to combine values into an array of arrays; e.g:

```javascript
_.zip([1, 2], ['a', 'b'], ['A', 'B']);

// [
//   [1, 'a', 'A'],
//   [2, 'b', 'B']
// ]
```

## And in PHP?

PHP doesn't have a native `zip()` function either, but a lesser-known feature of `array_map()` approximates its behaviour. As stated in the [documentation](http://php.net/manual/en/function.array-map.php):

> An interesting use of this function is to construct an array of arrays, which can be easily performed by using NULL as the name of the callback function

Interesting indeed! `array_map()` is normally used to apply a callback to one or more arrays, but if you pass `null` as its first argument, and pass it two or more arrays - more on this presently - it will implicitly "zip" their values; e.g:

```php
array_map(null, [1, 2], ['a', 'b'], ['A', 'B']);

// [
//   [1, 'a', 'A'],
//   [2, 'b', 'B'],
// ]
```

## A PHP implementation

Let's use the behaviour of `array_map()` to create our own `zip()` function. Using the [variadic syntax](http://php.net/manual/en/functions.arguments.php#functions.variable-arg-list.new) (`...`) introduced in PHP 5.6, we can elegantly unpack variadic arguments, and add some type hinting for good measure:

```php
function zip(array ...$arrays): array
{
    return array_map(null, ...$arrays);
}

zip([1, 2], ['a', 'b'], ['A', 'B']);

// [
//   [1, 'a', 'A'],
//   [2, 'b', 'B'],
// ]
```

## A better PHP implementation

However, as implied above, there's a catch... passing only one array to `array_map()` just returns the same output as input:

```php
array_map(null, [1, 2]);

// [1, 2] *not* [[1], [2]]
```

This is not consistent with the behaviour of `zip` in other languages, so let's rectify that:

```php
function zip(array $first, array ...$rest): array
{
    return $rest ? array_map(null, $first, ...$rest)
                 : array_chunk($first, 1);
}
```

Again we are using PHP's variadic syntax to accept and unpack a dynamic number of arguments, while retaining `array` type checking on those arguments. When only one array is passed to `zip()` the variadic `$rest` argument is an empty array, which which we can use to assert a condition.

And, if only one array is present, we instead use PHP's built in [`array_chunk()`](http://php.net/manual/en/function.array-chunk.php) to format the output into an array of single element sub-arrays as required.

This gives us the following behaviour:

```php
zip([]); 

// []

zip([1, 2]);

// [
//   [1],
//   [2],
// ]

zip([1, 2], ['a', 'b']);

// [
//   [1, 'a'],
//   [2, 'b'],
// ]
```

## What about arrays with different lengths?

This is where things get interesting. As mentioned at the beginning, implementations vary. Our implementation does what I'll call a "full" zip, where the values are zipped according to the longest input, and non-existent values are substituted with `null`:

```php
zip([1, 2], ['a'], ['A', 'B', 'C']));

// [
//   [1, 'a', 'A'],
//   [2, null, 'B'],
//   [null, null, 'C']
// ]  
```

This is the same behaviour as Lodash's zip implementation. Ruby is similar - adding `nil` values - but stops when the called array's length is reached. Elixir and Python stop zipping at the length of the shortest list. Maybe an interesting exercise for the reader to figure out an elegant way to emulate this behaviour? :)
