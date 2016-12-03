# node-cctz [![Build Status](https://travis-ci.org/floatdrop/node-cctz.svg?branch=master)](https://travis-ci.org/floatdrop/node-cctz)

> Blazingly fast timezone manipulations ⚡️

[CCTZ](https://github.com/google/cctz) is a C++ library for translating between absolute and civil times using the rules of a time zone.


## Install

You will need C++11 compatible compiler to build this binding. For most systems this will work:

```
$ npm install --save cctz
```

If you have Ubuntu 12.04, then install `clang-3.4` and set-up environment:

```
$ sudo apt-get install clang-3.4
$ export CXX=clang++
$ export npm_config_clang=1
```


## Usage

```js
const cctz = require('cctz');

const lax = cctz.tz('America/Los_Angeles');
const tp = cctz.convert(new cctz.CivilTime(2015, 9, 22, 9), lax);

const nyc = cctz.tz('America/New_York');
console.log(cctz.format('Talk starts at %T %z (%Z)', tp, nyc));

// => Talk starts at 12:00:00 -0400 (EDT)
```


## API

### tz(name)

> Alias for `cctz.load_time_zone`

Use this method instead `new TimeZone` – because it caches `TimeZone` objects inside.

Returns `TimeZone` object.

##### name
Type: `string`

Timezone name, that should be loaded (from `/usr/share/zoneinfo`).

### parse(format, input, timezone)

Parses `input` string according to `format` string (assuming `input` in `timezone`).

Returns unix timestamp or `undefined` if parsing failed.

##### format

Type: `string`

Format of `input` argument. See [strptime](http://www.cplusplus.com/reference/ctime/strptime/) documentation and [Google CCTZ](https://github.com/google/cctz/blob/6a694a40f3770f6d41e6ab1721c29f4ea1d8352b/include/time_zone.h#L197) sources for syntax.

##### input

Type: `string`

Input string to parse.

### format(format, unix, timezone)

Returns formatted unix timestamp according to timezone.

##### format

Type: `string`

Format of output. See [strftime](http://www.cplusplus.com/reference/ctime/strftime/) documentation and [Google CCTZ](https://github.com/google/cctz/blob/6a694a40f3770f6d41e6ab1721c29f4ea1d8352b/include/time_zone.h#L197) sources for syntax.

##### unix

Type: `number`

Unix timestamp in seconds (can have fractional part).

##### timezone

Type: `TimeZone`

TimeZone objcet, that represents target timezone for formatting.

### convert(time, timezone)

Converts `CivilTime` to unix timestamp and vice versa.

##### time

Type: `CivilTime` or `number`

If `time` is `CivilTime`, then method returns Unix timestamp (without fractional part).
Otherwise returns `CivilTime`.

##### timezone

Type: `TimeZone`

TimeZone objcet, that represents target timezone for converting.


### CivilTime

Holder for [`cctz::civil_second`](https://github.com/google/cctz/blob/6a694a40f3770f6d41e6ab1721c29f4ea1d8352b/include/civil_time.h#L22) with getters and setters for properties.

#### CivilTime(year = 1970, month = 1, day = 1, hour = 0, minute = 0, second = 0)

Creates CivilTime object with next properties:

- `year` – getter and setter
- `month` – getter and setter [1:12]
- `day` – getter and setter [1:31]
- `hour` – getter and setter [0:23]
- `minute` – getter and setter [0:59]
- `second` – getter and setter [0:59]
- `yearday` – only getter [1:356]
- `weekday` – only getter [0:6]

##### CivilTime.startOfYear()

Returns new CivilTime object with start of year.

##### CivilTime.startOfMonth()

Returns new CivilTime object with start of month.

##### CivilTime.startOfDay()

Returns new CivilTime object with start of day.

##### CivilTime.clone()

Returns cloned CivilTime object.


### TimeZone

Holder for [`cctz::time_zone`](https://github.com/google/cctz/blob/6a694a40f3770f6d41e6ab1721c29f4ea1d8352b/include/time_zone.h#L37).

#### TimeZone(name)

Creates __new__ object with TimeZone.

##### TimeZone.lookup(unix)

Returns [`cctz::absolute_lookup`](https://github.com/google/cctz/blob/6a694a40f3770f6d41e6ab1721c29f4ea1d8352b/include/time_zone.h#L60) object.

##### TimeZone.lookup(civiltime)

Returns [`cctz::civil_lookup`](https://github.com/google/cctz/blob/6a694a40f3770f6d41e6ab1721c29f4ea1d8352b/include/time_zone.h#L85) object.

##### TimeZone.name

Name of TimeZone.


## Tips

#### How to create unix timestamp

This is extremly simple:

```js
const timestamp = new Date() / 1000;
```

All methods expect unix timestamp with fractional seconds, so there is no need for `Math.floor`.


## Benchmarks

```
Format          (baseline) x 2,866,904 ops/sec ±6.22% (77 runs sampled)
                    (cctz) x 1,071,124 ops/sec ±2.97% (81 runs sampled)
                  (moment) x    79,488 ops/sec ±3.57% (79 runs sampled)

Parse           (baseline) x 2,099,187 ops/sec ±3.43% (83 runs sampled)
                    (cctz) x 1,165,998 ops/sec ±7.08% (73 runs sampled)
                  (moment) x    18,440 ops/sec ±2.19% (82 runs sampled)

Add hour        (baseline) x 13,995,323 ops/sec ±2.34% (85 runs sampled)
               (cctz-unix) x 32,914,870 ops/sec ±3.83% (81 runs sampled)
              (cctz-civil) x  5,623,556 ops/sec ±1.53% (84 runs sampled)
                  (moment) x    566,368 ops/sec ±2.98% (81 runs sampled)
```

Run `npm i` and then `npm run bench`.

## License

MIT © [Vsevolod Strukchinsky](mailto://floatdrop@gmail.com)
