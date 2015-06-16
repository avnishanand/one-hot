# one-hot

One hot encode vectors using a streaming implementation.

[![wercker status](https://app.wercker.com/status/1b44d13a2f20cff87dfafebb0f544ffe/s "wercker status")](https://app.wercker.com/project/bykey/1b44d13a2f20cff87dfafebb0f544ffe)

See Wikipedia [https://en.wikipedia.org/wiki/One-hot](https://en.wikipedia.org/wiki/One-hot).

``` javascript
var OneHot = require('one-hot');
```


## var oneHot = new OneHot(opts)

Instantiate a new instance of OneHot.

`opts` is an optional object where:

 * `opts.oneCold = true` will use `0` as the hot value and `1` as the cold value. default: false, ie. `1` is hot `0` is cold


## Example

See tests folder for more examples...

``` javascript
var testIVs = [
  [0, 1, 2, 'a', 3],
  [3, 4, 5, 'b', 6],
  [6, 7, 8, 'c', 9]
];

var oneHot = new OneHot();
oneHot.analyze(testIVs, function(err) {
  if (err) throw err;

  oneHot.encode(testIVs, function(err, encodedData) {
    if (err) throw err;

    // print out the encoded input vectors
    console.log(encodedData);
  });
});
```


## Streaming methods

These are the streaming methods. See test directory for an example.

#### var writeStreamAnalyze = oneHot.analyze()

This must be called with a single pass over all the data to determine how to one hot encode the data.

#### var writeStreamEncode = oneHot.encode()

This method will one hot encode each input vector via stream transform. Call this method after `oneHot.analyze()` has completed.


## Non-Streaming methods

These are the non-streaming methods.

#### oneHot.analyze(data, cb)

This must be called with a single pass over all the data to determine how to one hot encode the data. `data` must be an array of input vectors and `cb` must be a callback.

#### oneHot.encode(data, cb)

This method will one hot encode each input vector in `data`. `data` must be an array of input vectors and `cb` must be a callback with a signature of `(err, encodedData)` where `encodedData` will be all the one hot encoded data. Call this method after `oneHot.analyze()` has completed.


## Methods

#### var encodedIndex = getEncodedIndexFromOriginalIndex(originalIndex, featureValue)

This method will return the new index (the hot index) of a given original index and it's feature value. You must specify a feature because of the one-to-many relationship between original and encoded. For example:

``` javascript
var oneHot = new OneHot()
var ws = oneHot.analyze()
var originalIVs = [[1, 2, 3, 'a'], [1, 2, 3, 'b']]
... // removed for brevity (see tests)
var encodedIndex = oneHot.getEncodedIndexFromOriginalIndex(3, 'a'); // encodedIndex could be 3 or 4, [1, 2, 3, 1, 0], or [1, 2, 3, 0, 1] depending on the order in which the ivs were processed
```

#### var originalIndex = getOriginalIndexFromEncodedIndex(encodedIndex)

This method will return the original index (before encoding) of a given encoded index. For example:

``` javascript
var oneHot = new OneHot()
var ws = oneHot.analyze()
var originalIVs = [[1, 2, 3, 'a'], [1, 2, 3, 'b']]
... // removed for brevity (see tests)
var originalIndex = oneHot.getOriginalIndexFromEncodedIndex(4); // 3
```

#### var internalIndex = getInternalIndexFromOriginalIndex(originalIndex, featureValue)

This method will return the index used internally to one hot encode the `featureValue`, (the hot index) of a given original index and it's feature value. You must specify a feature because of the one-to-many relationship between original and encoded. For example:

``` javascript
var oneHot = new OneHot()
var ws = oneHot.analyze()
var originalIVs = [[1, 2, 3, 'a'], [1, 2, 3, 'b']]
... // removed for brevity (see tests)
var encodedIndexA = oneHot.getInternalIndexFromOriginalIndex(3, 'a'); // 4, ie, [1, 2, 3, <encoded column: {a, b}>, 1, 0]
var encodedIndexB = oneHot.getInternalIndexFromOriginalIndex(3, 'b'); // 5, ie, [1, 2, 3, <encoded column: {a, b}>, 0, 1]
```

#### var originalIndex = getOriginalIndexFromInternalIndex(internalIndex)

This method will return the original index from the index used internally to one hot encode. For example:

``` javascript
var oneHot = new OneHot()
var ws = oneHot.analyze()
var originalIVs = [[1, 2, 3, 'a'], [1, 2, 3, 'b']]
... // removed for brevity (see tests)
var encodedIndexA = oneHot.getOriginalIndexFromInternalIndex(4); // 3, ie, [1, 2, 3, <encoded column: {a, b}>, 1, 0] => [1, 2, 3, 'a']
var encodedIndexB = oneHot.getOriginalIndexFromInternalIndex(5); // 3, ie, [1, 2, 3, <encoded column: {a, b}>, 0, 1] => [1, 2, 3, 'b']
```

#### var encodedIndex = getEncodedIndexFromInternalIndex(internalIndex)

This method will return the encoded index from the index used internally to one hot encode. For example:

``` javascript
var oneHot = new OneHot()
var ws = oneHot.analyze()
var originalIVs = [[1, 2, 3, 'a'], [1, 2, 3, 'b']]
... // removed for brevity (see tests)
var encodedIndexA = oneHot.getEncodedIndexFromInternalIndex(4); // 3, ie, [1, 2, 3, <encoded column: {a, b}>, 1, 0] => [1, 2, 3, 1, 0]
var encodedIndexB = oneHot.getEncodedIndexFromInternalIndex(5); // 4, ie, [1, 2, 3, <encoded column: {a, b}>, 0, 1] => [1, 2, 3, 0, 1]
```

#### var internalIndex = getInternalIndexFromEncodedIndex(encodedIndex)

This method will return the the index used internally to one hot encode from the encoded index. For example:

``` javascript
var oneHot = new OneHot()
var ws = oneHot.analyze()
var originalIVs = [[1, 2, 3, 'a'], [1, 2, 3, 'b']]
... // removed for brevity (see tests)
var encodedIndexA = oneHot.getInternalIndexFromEncodedIndex(3); // 4, ie, [1, 2, 3, 1, 0] => [1, 2, 3, <encoded column: {a, b}>, 1, 0]
var encodedIndexB = oneHot.getInternalIndexFromEncodedIndex(4); // 5, ie, [1, 2, 3, 0, 1] => [1, 2, 3, <encoded column: {a, b}>, 0, 1]
```
