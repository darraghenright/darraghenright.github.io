# Test

2016/07

Python

```python
class Person(object):
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return 'Hi, my name is {}'.format(self.name)
```

and Elixir:

```elixir
# split lines!
def split_lines(text) do
  text
  |> String.downcase()
  |> String.split(~r/\n/)
done
```

and PHP

```php

final class Coordinate
{
    private $latitude;
    private $longitude;

    public function __construct(Latitude $latitude, Longitude $longitude)
    {
        $this->latitude  = $latitude;
        $this->longitude = $longitude;
    }

    public function asValues(): array
    {
        return [
          $this->latitude,
          $this->longitude,
        ]
    }
}
```
