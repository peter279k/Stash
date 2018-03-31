Stash
=====

![Stash](stash.png)

-----

[![Latest Stable Version](https://img.shields.io/packagist/v/PHLAK/Stash.svg)](https://packagist.org/packages/PHLAK/Stash)
[![Total Downloads](https://img.shields.io/packagist/dt/PHLAK/Stash.svg)](https://packagist.org/packages/PHLAK/Stash)
[![Author](https://img.shields.io/badge/author-Chris%20Kankiewicz-blue.svg)](https://www.ChrisKankiewicz.com)
[![License](https://img.shields.io/packagist/l/PHLAK/Stash.svg)](https://packagist.org/packages/PHLAK/Stash)
[![Build Status](https://img.shields.io/travis/PHLAK/Stash.svg)](https://travis-ci.org/PHLAK/Stash)
[![StyleCI](https://styleci.io/repos/55566401/shield?branch=master&style=flat)](https://styleci.io/repos/55566401)

Simple PHP caching library -- by, [Chris Kankiewicz](https://www.ChrisKankiewicz.com) ([@PHLAK](https://twitter.com/PHLAK))

Introduction
------------

Stash is a simple PHP caching library supporting multiple, interchangeable
caching back-ends and an expressive (Laravel inspired) API.

Supported caching back-ends:

  - File - File-based caching. Stores cache items as files in a directory on disk.
  - Memcached - High-performance, distributed memory object caching system
  - Redis - In-memory data structure store.
  - APCu - PHP's native APC User Cache.
  - Ephemeral - A transient, in-memory array that only exists for the lifetime of the script.

Like this project? Keep me caffeinated by [making a donation](https://paypal.me/ChrisKankiewicz).

Requirements
------------

  - [PHP](https://php.net) >= 5.4

Install with Composer
---------------------

```bash
composer require phlak/stash
```

Initializing the Client
-----------------------

First, import Stash:

```php
use PHLAK\Stash;
```

Then instantiate Stash for your back-end of choice with the named constructor:

    Stash\Cache::file($config);
    Stash\Cache::memcached($config);
    Stash\Cache::redis($config);
    Stash\Cache::apcu($config);
    Stash\Cache::ephemeral();

The `$config` parameter accepts a driver-specific [closure](https://secure.php.net/manual/en/class.closure.php)
for setting configuration options for your chosen driver. Refer to the specific
documentation about each driver below for more info. Not all drivers require a
configuration function.

Alternatively you may use the `Stash\Cache::make()` factory method to
instantiate your driver.

    $stash = Stash\Cache::make($driver, $config);

The `make()` method takes two parameters. The first (`$driver`) should be one of
the following lowercase strings representing your desired caching driver.

    - `apcu`
    - `ephemeral`
    - `file`
    - `memcached`.
    - `redis`

The second (`$config`) is the same driver-specific configuration closure as when
using a named constructor. Refer to the specific documentation about each driver
below for more info.

----

#### File Cache

The file cache configuration closure must call `$this->setCacheDir($path)` where
`$path` is a path to a valid directory in which your cache files will be stored.

```php
$stash = Stash\Cache::file(function () {
    $this->setCacheDir('path/to/cache');
});
```

#### Memcached

The Memcached configuration closure receives an instance of the Memcached object
as it's only parameter, you can use this parameter to connect and configure
Memcached. At a minimum you must connect to one or more Memcached server via the
`addServer()` or `addServers()` methods.

Reference the [PHP Memcached documentation](https://secure.php.net/manual/en/book.memcached.php)
for additional configuration options.

```php
$stash = Stash\Cache::memcached(function ($memcached) {
    $memcached->addServer('localhost', 11211);
    // $memcached->setOption(Memcached::OPT_PREFIX_KEY, 'some_prefix');
});
```

#### Redis

The Redis configuration closure receives an instance of the Redis object as it's
only parameter, you can use this parameter to connect to and configure Redis. At
a minimum you must connect to one or more Redis server via the `connect()` or
`pconnect()` methods.


Reference the [phpredis documentation](https://github.com/phpredis/phpredis#readme)
for additional configuration options.

```php
$stash = Stash\Cache::redis(function ($redis) {
    $redis->pconnect('localhost', 6379);
    // $redis->setOption(Redis::OPT_PREFIX, 'some_prefix');
});
```

#### APCu

The APCu driver caches items in PHPs APC user cache.

```php
$stash = Stash\Cache::apcu();
```

The APCu driver does not require a configuration closure. However, if you
wish to set a cache prefix you may pass a configuration closure that calls
`$this->setPrefix($prefix)` where `$prefix` is a string of your desired prefix.

```php
$stash = Stash\Cache::apcu(function () {
    $this->setPrefix('some_prefix');
});
```

#### Ephemeral

The Ephemeral driver caches items in a PHP array that exists in memory only for
the lifetime of the script. The Ephemeral driver does not take a configuration
closure.

```php
$stash = Stash\Cache::ephemeral();
```

Usage
-----

Add an item to the cache for a specified duration:

```php
$stash->put($key, $data, $minutes = 0);
```

Add an item to the cache permanently:

```php
$stash->forever($key, $data);
```

Retrieve an item from the cache:

```php
$stash->get($key, $default = false);
```

Check if an item exists in the cache:

```php
$stash->has($key);
```

Retrieve item from cache or, when item does not exist, execute a closure. The
result of the closure is then stored in the cache for the specified duration
and returned for immediate use.

```php
$stash->remember($key, $minutes, function() {
    // return something
});
```

or remember permanently:

```php
$stash->rememberForever($key, function() {
    // return something
});
```

Increment an integer:

```php
$stash->increment($key, $value = 1);
```

Decrement an integer:

```php
$stash->decrement($key, $value = 1);
```

Remove an item from the cache:

```php
$stash->forget($key);
```

Delete all items from the cache:

```php
$stash->flush();
```

Changelog
---------

A list of changes can be found on the [GitHub Releases](https://github.com/PHLAK/Stash/releases) page.

Troubleshooting
---------------

Please report bugs to the [GitHub Issue Tracker](https://github.com/PHLAK/Stash/issues).

Copyright
---------

This project is licensed under the [MIT License](https://github.com/PHLAK/Stash/blob/master/LICENSE).
