# Application Environment

The "application enviroment" is a collection of core functionality, like encryption/decryption methods, specific to a certain environment. Buttercup Core supports 2 enviroments (built-in):

 * NodeJS on an x86/64 platform (`import from "buttercup"`)
 * Web on a modern browser (`import from "buttercup/web"`)

These implementations are handled by the underlying **AppEnv** system that manages features per platform required for Buttercup Core usage.

## Shared AppEnv

You can fetch the internal, shared AppEnv instance to perform custom implementations by using the following:

```typescript
import { getSharedAppEnv } from "buttercup";
import { decryptData, encryptData } from "./crypto";

const appEnv = getSharedAppEnv();

// Provide custom implementations
appEnv.setProperties({
    // ...
    "crypto/v1/decryptText": decryptData,
    "crypto/v1/encryptText": encryptData
});
```

**All** of the following properties must be implemented for Buttercup Core to function:

| Property                              | Status        | Description                                           |
|---------------------------------------|---------------|-------------------------------------------------------|
| `compression/v1/compressText`         | Deprecated    | Text compression, version 1.                          |
| `compression/v1/decompressText`       | Deprecated    | Text decompression, version 1.                        |
| `compression/v2/compressText`         |               | Text compression, version 2 (format B).               |
| `compression/v2/decompressText`       |               | Text decompression, version 2 (format B).             |
| `crypto/v1/decryptBuffer`             |               | Buffer decryption.                                    |
| `crypto/v1/encryptBuffer`             |               | Buffer encryption.                                    |
| `crypto/v1/decryptText`               |               | Text decryption.                                      |
| `crypto/v1/encryptText`               |               | Text encryption.                                      |
| `crypto/v1/setDerivationRounds`       |               | Encryption password derivation rounds override.       |
| `encoding/v1/base64ToBytes`           |               | Conversion for base64 -> buffer.                      |
| `encoding/v1/base64ToText`            |               | Conversion for base64 -> text.                        |
| `encoding/v1/bytesToBase64`           |               | Conversion for buffer -> base64.                      |
| `encoding/v1/textToBase64`            |               | Conversion for text -> base64.                        |
| `env/v1/isClosedEnv`                  |               | Signal method for whether an environment is "secure" or not. |
| `net/webdav/v1/newClient`             |               | Constructor for a new WebDAV client instance.         |
| `rng/v1/uuid`                         |               | UUID generator.                                       |

