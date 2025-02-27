# Framework virtual modules

Virtual modules are the means for userland consumption of Fusion-owned static analysis and build artifacts in a way that:

1. Does not expose any underlying implementation details, such as module bundlers or transpilers
2. Provides a strong API contract with type safety with editor support
3. Provides a high degree of robustness with build-time errors in cases where static analysis will fail, including user misuse like not providing statically analyzable arguments.

In practice, a virtual module is implemented via a coupled agglomeration of babel plugin(s), webpack loader(s), and webpack plugin(s).

Fusion.js currently provides the following virtual modules:

## assetUrl

```js
import {assetUrl} from 'fusion-core';

assetUrl('path/to/some/file');
// => Path to the asset
```

#### Under-the-hood

- Webpack `file-loader`
- Custom babel plugin

## workerUrl

The workerUrl virtual module allows transpiling and loading a Web Worker. The result of the virtual call should be passed into the `Worker` constructor.

```js
import {workerUrl} from 'fusion-core';

// Path to the asset
const url = workerUrl('path/to/some/worker.js');
const myWorker = new Worker(url);
```

#### Under-the-hood

- Custom webpack plugin
- Custom babel plugin
- Custom webpack loader, forked from worker-loader.

## chunkId

This is a useful building block for implementing things such as translations and module async/lazy loading.

```js
import {chunkId} from 'fusion-core';

chunkId('path/to/some/module');
// => Array of client-side chunk ids for the module
```

#### Under-the-hood

- Custom webpack plugin
- Custom babel plugin
- Custom webpack loader
