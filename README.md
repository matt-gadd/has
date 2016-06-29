# dojo-has

[![Build Status](https://travis-ci.org/dojo/has.svg?branch=master)](https://travis-ci.org/dojo/has)
[![codecov](https://codecov.io/gh/dojo/has/branch/master/graph/badge.svg)](https://codecov.io/gh/dojo/has)
[![npm version](https://badge.fury.io/js/dojo-has.svg)](http://badge.fury.io/js/dojo-has)

A feature detection library.

This package provides an API for expressing feature detection to allow developers to branch code based upon the
detected features.  The features can also be asserted statically, thereby allowing integration into a build
optimization tool that can be used to make certain branches of code "dead" which can be elided during a build
step.  The `has` module is also capable of allowing conditional loading of modules with certain loaders.

## Features

### Feature Branching

The most common feature is branching in code based upon a feature flag.  `has()` essentially manages feature
flags, returning either a *truthy* value if the feature is present or a *falsey* value if the feature isn't present.
The `has()` module is the default export of the main module of the package:

For example:

```typescript
import has from 'dojo-has';

if (has('host-browser')) {
	/* Browser Related Code */
}
else {
	/* Non-Browser Related Code */
}
```

`has()` can be used in any conditional expression, like a ternary operator:

```typescript
function getArrayOrString(): number[] | string {
	return has('some-feature') ? [ 1, 2, 3 ] : '[ 1, 2, 3 ]';
}
```

### Included Features

Other Dojo 2 packages leverage the `has()` package to express features that are then used within the package.  Because
of this, there are very few features expressed in this foundational package.  The intention is that this package is
used to enable a developer to express other features.  The flags though that are included in this package are:

|Feature Flag|Description|
|------------|-----------|
|`host-browser`|Determines if the current environment contains a `window` and `document` object in the global context, therefore it being generally safe to assume the code is running in a browser environment.|
|`host-node`|Attempts to detect if the environment appears to be a node environment.|

### Adding a Feature Test/Feature Detection

The main module of the package exports a function named `add()` which allows the addition of features flags.  The feature
tests can be expressed as a static value, or as a function which will be lazily evaluated when the feature flag is first
requested from `has()`.  Once evaluated, the value is cached.

An example of adding a feature:

```typescript
import { add } from 'dojo-has';

add('my-feature', true);
add('my-lazy-feature', () => {
	/* will not be called yet */
	return true;
});

if (has('my-lazy-feature')) { /* feature function called here */
	/* do something */
}
```

If a feature flag is already added, the value can be overridden by supplying `true` as the 3rd argument of the `add()`
function:

```typescript
add('my-feature', false, true);
```

The module also has an `exits()` function which returns `true` if the feature flag has been added to `has()`:

```typescript
import { exists, add } from 'dojo-has';

if (!exists('my-feature')) {
	add('my-feature', false);
}
```

### Conditional Module Loading

When using an AMD loader that supports loader plugins (such as [`dojo-loader`](https://github.com/dojo/loader)) then
`dojo-has` can be used to conditionally load modules or substitute one module for another.  The module ID is specified
using the plugin syntax followed by the feature flag and a ternary operator of what to do if the feature is *truthy*
or *falsey*:

```typescript
import foo from 'dojo-has!host-browser?foo/browser:foo/node';

/* foo is now the default export from either `foo/browser` or `foo/node` */
```

The module IDs supplied in the ternary operator can be specified as absolute MIDs or relative MIDs based on the loading
module, just as if you were just directly importing the module.

When using TypeScript, TypeScript will not be able to automatically resolve the module shape, therefore you will often
have to make a global declaration of the module that is in the scope of the project where the module name matches the
full MID you will be importing:

```typescript
declare module 'dojo-has!host-browser?foo/browser:foo/node' {
	export * from 'foo/browser'; /* Assumes that foo/browser and foo/node have the same shape */
}
```

## How do I use this package?

TODO: Add appropriate usage and instruction guidelines

## How do I contribute?

We appreciate your interest!  Please see the [Dojo 2 Meta Repository](https://github.com/dojo/meta#readme) for the
Contributing Guidelines and Style Guide.

## Testing

Test cases MUST be written using [Intern](https://theintern.github.io) using the Object test interface and Assert assertion interface.

90% branch coverage MUST be provided for all code submitted to this repository, as reported by istanbul’s combined coverage results for all supported platforms.

To test locally in node run:

`grunt test`

To test against browsers with a local selenium server run:

`grunt test:local`

To test against BrowserStack or Sauce Labs run:

`grunt test:browserstack`

or

`grunt test:saucelabs`

## Licensing information

The original Dojo 1 `has()` API was based upon Peter Higgin's [has.js](https://github.com/phiggins42/has.js/).

© 2016 Dojo Foundation & contributors. [New BSD](http://opensource.org/licenses/BSD-3-Clause) license.
