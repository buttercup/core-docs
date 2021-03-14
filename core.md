# Buttercup Core

The core library, `buttercup`, can be considered a SDK for the Buttercup ecosystem. All the major Buttercup applications use it heavily to provide secure password management interfaces and features.

`buttercup` is available as an [NPM package](https://www.npmjs.com/package/buttercup), and can be installed by running the following:

```bash
npm install buttercup --save
```

You can then import it into a JavaScript or Typescript project easily:

```typescript
import { Vault, init } from "buttercup";

init();

const vault = new Vault();
```

## Web Usage

Buttercup can be used in a **modern** web environment also, but you should import from the `buttercup/web` path instead:

```typescript
import { Vault, init } from "buttercup/web";

init();

const vault = new Vault();
```

In projects that share source files, you can alias `buttercup` to `buttercup/web` in tools like webpack:

```javascript
// webpack.config.js

module.exports = {
    // ...
    resolve: {
        alias: {
            buttercup: require.resolve("buttercup/web")
        }
    }
};
```

## Other Environments

Other environments can be supported (besides those built-in, like NodeJS (native) and current browsers), but certain feature overrides must be implemented.

The `init` function initialises all core crypto functionality for the targetted platform, but must be **omitted** when using a custom [application environment](/usage/app-env). For unsupported or custom environments, the functionality normally provided under the `init` call needs to be polyfilled.

### React Native

React Native is _not_ natively supported by Buttercup Core. There _is_ a working implementation, however, within the [Buttercup Mobile](https://github.com/buttercup/buttercup-mobile) application repository.
