# Liberator

*A proxy for circumventing PHP visibility keyword restrictions.*

[![Current version image][version-image]][current version]
[![Current build status image][build-image]][current build status]
[![Current Windows build status image][windows-build-image]][current windows build status]
[![Tested against HHVM][hhvm-image]][current hhvm build status]
[![Current coverage status image][coverage-image]][current coverage status]

[build-image]: https://img.shields.io/travis/eloquent/liberator/master.svg?style=flat-square "Current build status for the master branch"
[coverage-image]: https://img.shields.io/codecov/c/github/eloquent/liberator/master.svg?style=flat-square "Current test coverage for the master branch"
[current build status]: https://travis-ci.org/eloquent/liberator
[current coverage status]: https://codecov.io/github/eloquent/liberator
[current hhvm build status]: http://hhvm.h4cc.de/package/eloquent/liberator
[current version]: https://packagist.org/packages/eloquent/liberator
[current windows build status]: https://ci.appveyor.com/project/eloquent/liberator
[hhvm-image]: https://img.shields.io/hhvm/eloquent/liberator/master.svg?style=flat-square "Tested against HHVM"
[version-image]: https://img.shields.io/packagist/v/eloquent/liberator.svg?style=flat-square "This project uses semantic versioning"
[windows-build-image]: https://img.shields.io/appveyor/ci/eloquent/liberator/master.svg?label=windows&style=flat-square "Current Windows build status for the master branch"

## Installation

- Available as [Composer] package [eloquent/liberator].

[composer]: http://getcomposer.org/
[eloquent/liberator]: https://packagist.org/packages/eloquent/liberator

## What is Liberator?

*Liberator* allows access to **protected** and **private** methods and
properties of objects as if they were marked **public**. It can do so for both
objects and classes (i.e. static methods and properties).

*Liberator*'s primary use is as a testing tool, allowing direct access to
methods that would otherwise require complicated test harnesses or mocking to
test.

Note that good design pretty much always negates the need to use this library.
If *Liberator* is required, it should be treated as a [code smell].

[code smell]: https://en.wikipedia.org/wiki/Code_smell

## Usage

### For objects

Take the following class:

```php
class SeriousBusiness
{
    private function foo($adjective)
    {
        return 'foo is ' . $adjective;
    }

    private $bar = 'mind';
}
```

Normally there is no way to call `foo()` or access `$bar` from outside the
`SeriousBusiness` class, but *Liberator* allows this to be achieved:

```php
use Eloquent\Liberator\Liberator;

$object = new SeriousBusiness;
$liberator = Liberator::liberate($object);

echo $liberator->foo('not so private...'); // outputs 'foo is not so private...'
echo $liberator->bar . ' = blown';         // outputs 'mind = blown'
```

### For classes

The same concept applies for static methods and properties:

```php
class SeriousBusiness
{
    static private function baz($adjective)
    {
        return 'baz is ' . $adjective;
    }

    static private $qux = 'mind';
}
```

To access these, a *class liberator* must be used instead of an *object
liberator*, but they operate in a similar manner:

```php
use Eloquent\Liberator\Liberator;

$liberator = Liberator::liberateClass('SeriousBusiness');

echo $liberator->baz('not so private...'); // outputs 'baz is not so private...'
echo $liberator->qux . ' = blown';         // outputs 'mind = blown'
```

Alternatively, *Liberator* can generate a class that can be used statically:

```php
use Eloquent\Liberator\Liberator;

$liberatorClass = Liberator::liberateClassStatic('SeriousBusiness');

echo $liberatorClass::baz('not so private...');      // outputs 'baz is not so private...'
echo $liberatorClass::liberator()->qux . ' = blown'; // outputs 'mind = blown'
```

Unfortunately, there is (currently) no __getStatic() or __setStatic() in PHP,
so accessing static properties in this way is a not as elegant as it could be.

## Applications for Liberator

- Writing [white-box] style unit tests (testing protected/private methods).
- Modifying behavior of poorly designed third-party libraries.

[white-box]: http://en.wikipedia.org/wiki/White-box_testing
