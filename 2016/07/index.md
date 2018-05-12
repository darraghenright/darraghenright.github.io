# darraghenright.github.io

[Home](/)

2016/07

~~~python
class Person(object):
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return 'Hi, my name is {}'.format(self.name)
~~~

and

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
