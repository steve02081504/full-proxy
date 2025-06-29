# full-proxy

[![npm version](https://badge.fury.io/js/full-proxy.svg)](https://badge.fury.io/js/full-proxy)

A lightweight, zero-dependency utility for creating a proxy that completely intercepts all operations on an object, fetching a fresh target object for each operation.

This is useful when you want to work with an object that might change or be recreated over time, ensuring you always interact with the most up-to-date version.

## Installation

```bash
npm install full-proxy
```

## Usage

The `FullProxy` function takes a single argument: a `base` function that returns the target object. Every interaction with the proxy (e.g., getting, setting, calling a function) will invoke the `base` function to get the current target object.

### Example: Dynamic Configuration

Imagine you have a configuration object that can be reloaded at any time. Using `full-proxy`, you can ensure that your code always accesses the latest configuration values.

```javascript
import { FullProxy } from 'full-proxy';

let config = {
  host: 'localhost',
  port: 8080,
};

// This function simulates reloading the configuration from a source
function reloadConfig() {
  config = {
    host: 'api.example.com',
    port: 443,
  };
  console.log('Configuration reloaded!');
}

// Create a proxy for our configuration object
const configProxy = FullProxy(() => config);

// Access initial configuration
console.log(configProxy.host); // Output: localhost
console.log(configProxy.port); // Output: 8080

// Simulate a configuration reload
reloadConfig();
// Configuration reloaded!

// Access the updated configuration through the proxy
console.log(configProxy.host); // Output: api.example.com
console.log(configProxy.port); // Output: 443
```

## API

### `FullProxy<T>(base: () => T): T`

Creates a new proxy object.

- **`base`**: A function that returns the target object for each operation. This function is called every time an operation (like `get`, `set`, `has`, etc.) is performed on the proxy.
- **Returns**: A new `Proxy` object of type `T` that wraps the object returned by the `base` function.

## How It Works

`full-proxy` uses the `Proxy` object's handler to intercept all 13 available traps:

- `apply`
- `construct`
- `defineProperty`
- `deleteProperty`
- `get`
- `getOwnPropertyDescriptor`
- `getPrototypeOf`
- `has`
- `isExtensible`
- `ownKeys`
- `preventExtensions`
- `set`
- `setPrototypeOf`

For each trap, it calls the `base()` function to get the current target object and then uses `Reflect` to perform the corresponding operation on that fresh object. This ensures that every interaction is with the most up-to-date version of the target.
