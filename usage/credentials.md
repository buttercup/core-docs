# Credentials

The `Credentials` class is a core piece of functionality that underpins most of the Buttercup core codebase. It provides a secure store for passwords and other credentials, as well as a required interface for interacting with vaults and datasources when loading or saving. It's also used to transport datasource details that may, for some sources, include other secret data such as WebDAV or Dropbox login details or tokens.

Credentials work by storing the secret details in private memory space, not accessible directly on the interface, so therefore not exposed to any code that comes across the reference. The credentials content can be exported, but only in encrypted format, by using `toSecureString`. This uses the initially provided password to encrypt, so the exported format is not readable without it.

`Credentials` is most often used to transport just a single, primary password:

```typescript
await datasource.load(
    Credentials.fromPassword(userPrimaryPassword)
);
```

Datasource details can also be stored:

```typescript
const datasourceCredentials = Credentials.fromDatasource({
    type: "file",
    path: "/path/to/vault.bcup"
}, mainPassword);
```

> Note the `mainPassword` parameter in the code above - No `Credentials` instance can be created without a main password, as it's required for the encryption processes behind `toSecureString`.

A credentials instance can be exported using `await creds.toSecureString()`, which results in an encrypted string containing the contents of the `Credentials` instance. This secure string can then be later re-instantiated as a `Credentials` instance by using `const creds = await Credentials.fromSecureString(str)`.

## Purposes

A `Credentials` instance is embued with a list of **purposes** - activities that the instance can be used for. If the list doesn't contain the activity currently being requested of a `Credentials` instance, a security error will be thrown.

Purposes can only be subtracted, from within a set of credentials, never added to or modified in any other manner. Consuming interfaces such as datasources will reduce/restrict the further possible uses of a `Credentials` instance to improve security (by reducing over-exposure of secrets). This can be performed by calling `restrictPurposes`.

The following purposes are always initially present in all `Credentials` instances:

| Purpose                                   | Description                                               |
|-------------------------------------------|-----------------------------------------------------------|
| `Credentials.PURPOSE_DECRYPT_VAULT`       | Allows the decryption of vaults, from within datasources, using the main password. |
| `Credentials.PURPOSE_ENCRYPT_VAULT`       | Allows for encryption of vaults, via datasources, using the main password. |
| `Credentials.PURPOSE_SECURE_EXPORT`       | Allows for exporting of credential content via `toSecureString`. |

The loading and saving of vaults via datasources is controlled by `PURPOSE_DECRYPT_VAULT` and `PURPOSE_ENCRYPT_VAULT`. This is why it is necessary to use different `Credentials` instances for creating a datasource and loading/saving via one.
