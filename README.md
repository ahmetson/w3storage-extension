# W3Storage Extension

**W3Storage Extension** enables your app's content storage on 
[IPFS](https://ipfs.tech) and [Filecoin](https://filecoin.io).

It's the wrapper of [https://web3.storage/](https://web3.storage/)

> **For curious people**
> 
> This extension is of the [database](./DATABASE.md) type of service. 
> This means, **w3storage extension** is compatible with other storages.
> 
> Refer to the [database](./DATABASE.md) service reference for
> type of commands that this extension should implement.

Todo:
* Use [w3name](https://web3.storage/products/w3name/) instead StaticOrganization.

# Requirements
Requires `W3_STORAGE_API_TOKEN` environment variable.

To get the storage api token, go to [https://web3.storage/](https://web3.storage/).
Create an account.
Follow the official [web3 documentation](https://web3.storage/docs/#get-an-api-token) to get API token.

Create `.env` and add `W3_STORAGE_API_TOKEN`.

# Commands to use

> It's based on the `service-lib/extension/database`.
> Command names are same for any `database` kind of service.
> 
> Here we describe the way how it's interpreted for **w3storage extension**.

Here, we discuss how the `database` extension commands
are interpreted in **w3storage extension**.

### Select Row
Command name `select-row`.

Request Parameters:

| Name     | Type       | Description                                                                             |
|----------|------------|-----------------------------------------------------------------------------------------|
| `tables` | `string[]` | The tables should have only one element which is the `CID`                              |
| `fields` | `string[]` | The fields should have only one element which is the file name. Example: `testnet.json` |


Reply Parameters:

| Name                      | Type | Description                                                              |
|---------------------------|------|--------------------------------------------------------------------------|
| `outputs`                 | `key_value.KeyValue` | The `outputs` parameter has a sub parameter which matches the file name. |
| `outputs.<file name.json>` | `string` | The content of the file fetched from IPFS |                               |


`select-row` command gets the content of the data from the storage. If the request fails, then
it will return an error.

### Select
Command name `select`

| Name     | Type       | Description                                                                                                                 |
|----------|------------|-----------------------------------------------------------------------------------------------------------------------------|
| `tables` | `string[]` | The tables should have at least only one element which is the `CID`                                                         |
| `fields` | `string[]` | The fields should have the same amount of elements as `tables` parameter. Each element is the file name within the `table`. |

Reply parameters:

| Name                      | Type | Description                                                              |
|---------------------------|------|--------------------------------------------------------------------------|
| `rows`                    | `key_value.KeyValue[]` | The `rows` parameter has a sub parameter which matches the file name. |
| `rows.$.<file name.json>` | `string` | The content of the file fetched from IPFS |                               |

The `fields` reply parameters length is same as `tables` request
parameter.
Each element in the `rows` has one element which
matches the fields name.

`select` command gets multiple files from the storage


### Insert
Command name `insert`.

Request Parameters:

| Name        | Type       | Description                                                                                    |
|-------------|------------|------------------------------------------------------------------------------------------------|
| `fields`    | `string[]` | The fields should have only one element which is the file name. Example: `testnet.json`        |
| `arguments` | `any[]`    | The arguments should have only one element which is the string content. Example: `hello world` |


Reply parameters:

| Name   | Type     | Description                           |
|--------|----------|---------------------------------------|
| `id`   | `string` | The CID of the file generated by IPFS |

`insert` command sets a new file on IPFS and returns the
content id (CID). If the file already exists, then
simply returns the CID itself.

### Update
Command name `update`
is the alias of insert.


### Exist
Command name `exist`

| Name     | Type       | Description                                                                                                                 |
|----------|------------|-----------------------------------------------------------------------------------------------------------------------------|
| `tables` | `string[]` | The tables should have at least only one element which is the `CID`                                                         |
| `fields` | `string[]` | The fields should have the same amount of elements as `tables` parameter. Each element is the file name within the `table`. |


Reply parameters:

| Name     | Type      | Description               |
|----------|-----------|---------------------------|
| `exist`  | `boolean` | Result of the file check. |

`exist` command checks the file written in `fields[0]` request parameter
in the `tables[0]` CID. The result is set in `exist` reply
parameter.

In case of any error, the request will be replied with an error.

### Delete
Command name `delete`

| Name     | Type       | Description                                                                                                                 |
|----------|------------|-----------------------------------------------------------------------------------------------------------------------------|
| `tables` | `string[]` | The tables should have at least only one element which is the `CID`                                                         |
| `fields` | `string[]` | The fields should have the same amount of elements as `tables` parameter. Each element is the file name within the `table`. |

There are no reply parameters.

`delete` command sets an empty content in the file.
Since the IPFS is immutable, it doesn't have a `delete`
operation, therefore it will call `insert` content 
with `arguments[0]` request parameter set to `""`.
