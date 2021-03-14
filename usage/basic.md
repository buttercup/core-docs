# Basic Usage

The core library provides a large number of classes that serve a broad range of uses. For basic interaction with a password vault, very few are required.

> Remember that before using any crypto-dependent Buttercup classes or functions, such as `Vault` or `TextDatasource`, you must call `init()` or set up a custom [application environment](/usage/app-env).

## Core Concepts

The core library makes use of the following standard concepts:

 * **Vaults**, for storing all the encrypted and secure data
 * **Entries**, for storing logins or other secret details (like a document)
 * **Groups**, for hierarchically storing bunches of entries
 * Datasources, for reading and writing vaults from and to certain endpoints (files, hosting providers etc.)
 * Facades, for transmitting complex vaults as JSON between certain application interfaces

These concepts will appear a lot throughout this documentation.


## Manipulating a Vault

Vaults can be created completely empty, or with a standard structure:

```typescript
import { Vault } from "buttercup";

// Non-empty, standard vault
const standardVault = Vault.createWithDefaults();

// Completely empty vault, no trash etc.
const emptyVault = new Vault();
```

> Note that we did not call `init` in this example - you only need to call it once in an environment, and we assume that you understand its requirement. It won't appear in further general examples.

We recommend always using the standard approach, as having an empty vault means items are not sent to trash before being completely deleted.

The `Vault` interface provides methods to create new groups, which can then in-turn create new entries:

```typescript
import { Vault } from "buttercup";

const vault = Vault.createWithDefaults();
const myGroup = vault.createGroup("New Group");
const myEntry = myGroup.createEntry("My Email account");
```

In the example above, `vault` now contains a new group and inside that a new entry. These items can be fetched later, without the original references.

## Saving and Loading

Vaults can be saved and loaded via a Datasource. There are several datasources available, and all of them extend `TextDatasource`, which provides the core encryption and decryption logic used by all the others.

> When creating a custom datasource it is recommended that you extend `TextDatasource` and override methods as necessary.

A `TextDatasource` doesnt effectively save vault contents anywhere, nor load contents from some source. It is merely an in-memory datasource and takes in either encrypted vault content or raw vault content, and outputs the opposite.

Each datasource requires a specific constructor implementation, along with customised `load` and `save` functions. Due to the strong similarities datasource usage is quote straightforward.

The following example shows how to save a vault to disk using the `FileDatasource`:

```typescript
import { Credentials, FileDatasource, Vault } from "buttercup";

const password = "some-password";

(async function() {
    const datasourceCreds = Credentials.fromDatasource({
        type: "file",
        path: "/home/user/vault.bcup"
    }, password);
    const datasource = new FileDatasource(datasourceCreds);

    const vault = Vault.createWithDefaults();

    // Writes encrypted vault to disk:
    await datasource.save(vault.format.history, Credentials.fromPassword(password));
})();
```

> **Important:** Writing vaults to a datasource this way _does not_ support merging of changes from the remote, and is therefore not recommended for applications where synchronising changes from potentially serveral sources is required. Use a `VaultSource` and `VaultManager` for this.

In the example above, a datasource and vault are created, and the vault then saved using the datasource. The datasource facilitates the encryption of the vault contents (using the underlying `TextDatasource`) and the writing to the remote source - in this case, the file system.

The `Credentials` interface is used here to securely store and transport a user's credentials (password in this case) around inside the current application. [`Credentials`](/usage/credentials) is used very often, and is required when authenticating any secure subject (such as vaults or datasources).

The next example shows how to load a vault from disk using the `FileDatasource`:

```typescript
import { Credentials, DatasourceLoadedData, FileDatasource, Vault } from "buttercup";

const password = "some-password";

(async function() {
    const datasourceCreds = Credentials.fromDatasource({
        type: "file",
        path: "/home/user/vault.bcup"
    }, password);
    const datasource = new FileDatasource(datasourceCreds);

    // Read vault from disk (returns history and format)
    const loadedState: DatasourceLoadedData = await datasource.load(Credentials.fromPassword(password));
    // Create a new vault instance from the loaded data
    const vault = Vault.createFromHistory(loadedState.history, loadedState.Format);
})();
```
