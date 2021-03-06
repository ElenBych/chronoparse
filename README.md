# `chronoparse` [![npm version](https://badge.fury.io/js/chronoparse.svg)](https://badge.fury.io/js/chronoparse)[![Build Status](https://travis-ci.com/dylanrenwick/chronoparse.svg?branch=master)](https://travis-ci.com/dylanrenwick/chronoparse)[![Coverage Status](https://coveralls.io/repos/github/dylanrenwick/chronoparse/badge.svg?branch=master)](https://coveralls.io/github/dylanrenwick/chronoparse?branch=master)

chronoparse is a simple nodejs module designed for parsing user-input timespan strings.  
Currently it supports the format:
```
<quantity> <timespan>
```
Where `<quantity>` is a non-negative integer, and `<timespan>` is one of the following:
```
second, seconds, s - 1 second
minute, minutes, m - 60 seconds
hour, hours, h - 60 minutes
day, days, d - 24 hours
week, weeks, w - 7 days
month, months - 30 days
year, years, y - 365 days
decade, decades - 10 years
```

chronoparse parses the given string, and returns an object using the following schema:
```js
{
    totalSeconds, // an integer representing the total length of the timespan in seconds
    startDate, // the given startDate in the options object
    endDate, // a datetime totalSeconds from startDate
    timezone, // the current local timezone, specified as "UTC+/-X" where X is the offset in hours
    parts // an array of each timespan string parsed
}
```

## Usage

chronoparse exports a single function, called `parse`. This function takes a string to be parsed, and an optional options object.

```js
const chronoparse = require("chronoparse");

let result1 = chronoparse("13 days");

console.log(result1.totalSeconds);
// expected output: 1123200

let result2 = chronoparse("2 minutes 3 seconds");

console.log(result2.totalSeconds);
// expected output: 123
```

The options object may contain any or all of the following options:
```js
{
    delimiter, // a string to use to separate multiple timespans, defaults to whitespace
    startDate, // a Date object specifying when to calculate datetimes from, defaults to now
    max // a non-negative integer specifying the maximum number of timespans to parse in the string, defaults to no limit
}
```

### Delimiters

chronoparse can handle compound timespans, such as `2 hours 20 minutes 14 seconds` however, by default, it expects multiple timespans to be separated by only whitespace. If you wish to use other delimiters, such as commas, you should use the `delimiter` option:
```js
let result = chronoparse("2 minutes, 3 seconds", {delimiter: ","});

console.log(result.totalSeconds);
// expected output: 123
```
Note that the trailing space after the delimiter does not need to be specified in the options object, as chronoparse will automatically trim whitespace from the start of the string before attempting to parse the next timespan.

### Max timespans

In some situations, you may wish to only parse a certain number of timespans from the string. In such situations, you should use the `max` option:
```js
let result = chronoparse("1 hour, 2 minutes, 3 seconds", { delimiter: ",", max: 2 });

console.log(result.totalSeconds);
// expected output: 3720
```

chronoparse will parse only the first `max` timespans in a string, and will stop parsing after it has parsed `max` timespans. This means that your string may contain anything, including invalid timespans, after `max` valid timespans, however this is not recommended.
```js
let result = chronoparse("1 hour, 2 minutes, 3 is the coolest number", { delimiter: ",", max: 2 });

console.log(result.totalSeconds);
// expected output: 3720
```

**NOTE:** The default value of `max` is `-1`. This is interpreted as "no limit".

## License and Usage

chronoparse is licensed under the MIT License, meaning it is provided as-is, and can be used freely for any purposes, commercial or otherwise. See [LICENSE](https://github.com/dylanrenwick/chronoparse/blob/master/LICENSE) for the full license.