---
title: Open Basin Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript
  - swift
  - shell

toc_footers:
  - <a href='https://dashboard.openbasin.io'>Sign Up for Developer Keys</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Open Basin API
---

# Introduction

<aside>
Beta <code>v0.01</code>
</aside>

Welcome to the Open Basin API! You can use our API to access the Open Basin endpoints, which enables your users to access their data in your applications.

We have language bindings in Shell, JavaScripts, and Swift! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Get Started

> To setup configuration, use this code:

```javascript
// basin.js

import { initializeApp } from "open-basin";

const config = {
        network: "MUMBAI",
        provider: {
            id: ${PROVIDER_ID},
            address: ${PROVIDER_ADDRESS},
            key: ${API_KEY}
        }
}

const basin = initializeApp(config);

export default basin;
```

```swift
// AppDelegate.swift

import OpenBasin

Basin.configure(for: .mumbai,
                id: ${PROVIDER_ID},
                address: ${PROVIDER_ADDRESS},
                key: ${API_KEY})
```

```shell
# With shell, you can just pass the correct headers and
# parameters with each request.
curl "api.openbasin.io/*" \
  -H Authorization: "Bearer ${API_KEY}" \
  -d provider_id: "${PROVIDER_ID}" \
  -d provider_address: "${PROVIDER_ADDRESS}"
```

> Make sure to replace `${}` with the proper values.

Open Basin uses API keys, Public Addresses, and IDs to allow access to the API. You can register for new Open Basin keys at our [developer portal](https://dashboard.openbasin.io).

Open Basin expects for the API key to be included in the header and the Address and ID in the parameters of all API requests to the server that looks like the following:

`Authorization: ${API_KEY}`

`provider_address: ${PROVIDER_ID}`

`provider_id: ${PROVIDER_ADDRESS}`

# How It Works

Open Basin is a structured data lake that anyone can read or write to. All stored data has owner(s) that control access to it. As an open system, any provider can request access to any set of data. Once an owner(s) grants a provider permission, the provider can interact with the data as instructed by its owner(s).

## The System

All data on Open Basin is stored on IPFS and structured on Ethereum. When writing to Open Basin the process is as follows:

1. HTTPS POST with data, standard, bucket, and owner(s).
2. If the provider has permissions, Open Basin validates the data against the provided standard and bucket.
3. The data is encrypted and stored in IPFS.
4. The IPFS address is encrypted and stored on the Open Basin Ethereum ledger.

## Glossary

Term | Description
--------- | -----------
Owner | Has complete control over their data by providing and revoking permissions to their data.
Provider | Reads and writes data on behalf of the owners. Providers can only work with data as the owners see fit.
Standard | Valid JSON Schema to enforce a specified data structure. When storing data on Open Basin, all data must be validated against its corresponding standard.
Bucket | A set of validated data where owners pool data of the same type or category. Buckets enforce specified data standards so providers can expect a consistent structure.
Document | A wrapper object for the data stored on the ledger that contains document metadata and raw data.

<aside class="notice">For more information or clarity, join our <a href="https://discord.com/invite/M3NdGXgS">Discord server</a> to chat with us.</aside>

# Authentication

> Using **User Profile** as example:

In order to interact with data on Open Basin, owners must allow you to do so.

## Request Permissions

```javascript
import basin from './basin.js';

await basin.auth.request.bucket("public_profiles", "${OWNER_ADDRESS}");v
```

```swift
import OpenBasin

Basin.auth
    .request
    .owner("${OWNER_ADDRESS}")
    .bucket(in: "public_profiles") { result in
        switch result {
        case .success(let permissions):
            print(permissions)
        case .error(let error):
            print(error)
        }
    }
```

```shell
Permission methods are note supported via Shell
```
> The above command returns JSON structured like below:

```json
{
    "status": bool
}
```

Requests access to an owner's data in a specified bucket.

The user will be directed to an Open Basin web page to execute the authorization.

### Parameters

Parameter | Description
--------- | -----------
owner | The address of the owner
bucket | The id of the bucket
provider_address | The address of the requestor

<aside class="success">
Note — Permission can be revoked at any time.
</aside>

## Check Permissions

```javascript
import basin from './basin.js';

const permitted = await basin.auth.permissions.bucket("public_profiles", "${OWNER_ADDRESS}");

console.log(permitted);
```

```swift
import OpenBasin

Basin.auth
    .permissions
    .owner("${OWNER_ADDRESS}")
    .bucket(in: "public_profiles") { result in
        switch result {
        case .success(let permissions):
            print(permissions)
        case .error(let error):
            print(error)
        }
    }
```

```shell
curl "api.openbasin.io/datastore/standards/all/standards" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}" \
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d owner: "${OWNER_ADDRESS}" \
    -d bucket: "public_profiles"
```
> The above command returns JSON structured like below:

```json
{
    "status": bool
}
```

Checks if the requesting provider has permissions to an owner's data in a specified bucket.

### HTTP Request

`GET api.openbasin.io/datastore/auth/bucket/permissions`

### Parameters

Parameter | Description
--------- | -----------
owner | The address of the owner
bucket | The id of the bucket
provider_address | The address of the requestor

# Standards

Standards are a valid JSON schema that enforces structure and format of data.

### Properties

`id: string` - A unique identifier

`minter: address` - The address of the provider that minted the standard

`name: string` - The name of the standard

`timestamp: date` - The date and time at which the standard was minted

`schema: JSON Schema` - The schema used to validate data against

<aside class="notice">View deployed standards on the <a href="https://dashboard.openbasin.io/standards">Open Basin dashboard</a>.</aside>

## Mint Standards

```javascript
import basin from './basin.js';

await basin.mint.standard(
    "profile_v0",
    "profile",
    {
        "$schema": "http://json-schema.org/draft-06/schema#",
        "$ref": "#/definitions/profile_v0",
        "definitions": {
            "profile_v0": {
                "type": "object",
                "additionalProperties": {},
                "properties": {
                    "name": {
                        "type": "string"
                    },
                    "image": {
                        "type": "string"
                    }
                },
                "required": [
                    "name",
                    "image"
                ],
                "title": "profile_v0",
                "description": "Your core prfile data."
            }
        }
    }
);
```

```swift
import OpenBasin

Basin.mint.standard(id: "profile_v0",
                    name: "profile",
                    schema: [
                        "$schema": "http://json-schema.org/draft-06/schema#",
                        "$ref": "#/definitions/profile_v0",
                        "definitions": [
                            "profile_v0": [
                                "type": "object",
                                "additionalProperties": [:],
                                "properties": [
                                    "name": [
                                        "type": "string"
                                    ],
                                    "image": [
                                        "type": "string"
                                    ]
                                ],
                                "required": [
                                    "name",
                                    "image"
                                ],
                                "title": "profile_v0",
                                "description": "Your core profile data."
                            ]
                        ]
                    ])
{ result in
    switch result {
    case .success(let response)
        print(response)
    case .error(let error)
        print(error)
    }
}
```

```shell
curl "api.openbasin.io/datastore/standard/mint" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}" \
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d id: "profile_v0" \
    -d name: "profile" \
    -d schema: {
        "id": "profile_v0",
        "name": "profile",
        "schema": {
            "$schema": "http://json-schema.org/draft-06/schema#",
            "$ref": "#/definitions/profile_v0",
            "definitions": {
                "profile_v0": {
                    "type": "object",
                    "additionalProperties": {},
                    "properties": {
                        "name": {
                            "type": "string"
                        },
                        "image": {
                            "type": "string"
                        }
                    },
                    "required": [
                        "name",
                        "image"
                    ],
                    "title": "profile_v0",
                    "description": "Your core prfile data."
                }
            }
        }
    }
```

> The above command returns JSON structured like below:

```json
{
    "status": "success"
}
```

Creates a Standard and stores on Open Basin

### HTTP Request

`POST api.openbasin.io/datastore/standard/mint`

### Query Parameters

Parameter | Description
--------- | -----------
id | The ID of the standard to mint
name | The name of the standard
schema | The JSON schema of the standard

<aside class="success">
Note — Standards are immutable.
</aside>

## Get Standards

```javascript
import basin from './basin.js';

const standards = await basin.api.standards.all.get();

console.log(standards);
```

```swift
import OpenBasin

Basin.api.standards.getAllStandards { result in
    switch result {
    case .success(let standards)
        print(standards)
    case .error(let error)
        print(error)
    }
}
```

```shell
curl "api.openbasin.io/datastore/standards/all/standards" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}" \
    -d provider_address: "${PROVIDER_ADDRESS}"
```

> The above command returns JSON structured like below:

```json
[
    {
        "standard": {
        "id": string,
        "minter": address,
        "name": string,
        "schema": any,
        "timestamp": date,
        "token": number
        },
        "standard_id": string
    }
]
```

This endpoint retrieves all standards.

### HTTP Request

`GET api.openbasin.io/datastore/standards/all/standards`

### Query Parameters

No additional parameters

## Get a Specific Standard

```javascript
import basin from './basin.js';

const standard = await basin.api.standard("profile_v0").get();

console.log(standard)
```

```swift
import OpenBasin

Basin.api.standard("profile_v0").getStandard { result in
    switch result {
    case .success(let standard)
        print(standard)
    case .error(let error)
        print(error)
    }
}
```

```shell
curl "api.openbasin.io/datastore/standard" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}"\
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d id: "profile_v0"
```

> The above command returns JSON structured like this:

```json
{
    "id": string,
    "minter": address,
    "name": string,
    "schema": any,
    "timestamp": date,
    "token": number
}
```

This endpoint retrieves a specific Standard.

### HTTP Request

`GET api.openbasin.io/datastore/standard`

### Query Parameters

Parameter | Description
--------- | -----------
id | The ID of the standard to retrieve

# Buckets

Buckets are sets of data that conform to a set of whitelisted standards and serve the same purpose.

### Properties

`id: string` - A unique identifier

`minter: address` - The address of the provider that minted the bucket

`name: string` - The name of the bucket

`description: string` - The description of the data the bucket holds

`timestamp: date` - The date and time at which the bucket was minted

`allowed_standards: string[]` - The set of whitelisted standards that the bucket dat must conform to

<aside class="notice">View deployed buckets on the <a href="https://dashboard.openbasin.io/buckets">Open Basin dashboard</a>.</aside>

## Mint Bucket

```javascript
import basin from './basin.js';

await basin.mint.bucket(
    "public_profiles",
    "Your Profiles",
    "Your public profile to be used on other apps."
    ["profile_v0"]
);
```

```swift
import OpenBasin

Basin.mint.bucket(id: "public_profiles",
                  name: "Your Profiles",
                  description: "Your public profile to be used on other apps.",
                  standards: ["profile_v0"]) { result in
    switch result {
    case .success(let response)
        print(response)
    case .error(let error)
        print(error)
    }
}
```

```shell
curl "api.openbasin.io/datastore/bucket/mint" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}" \
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d id: "public_profiles" \
    -d name: "Your Profiles" \
    -d description: "Your public profile to be used on other apps." \
    -d standards: ["profile_v0"]
```

> The above command returns JSON structured like below:

```json
{
    "status": "success"
}
```

Creates a Bucket and stores on Open Basin

### HTTP Request

`POST api.openbasin.io/datastore/bucket/mint`

### Query Parameters

Parameter | Description
--------- | -----------
id | The ID of the bucket to mint
name | The name of the bucket
description | The description of the bucket
allowed_standards | The list of whitelisted standards for the bucket

<aside class="success">
Note — Only the list of allowed standards can be changed.
</aside>

## Update Bucket Standards

```javascript
import basin from './basin.js';

await basin.mint.appendStandard(
    "public_profiles",
    "profile_v1"
);
```

```swift
import OpenBasin

Basin.mint.appendStandard(to: "public_profiles",
                          standard: "profile_v1") { result in
    switch result {
    case .success(let response)
        print(response)
    case .error(let error)
        print(error)
    }
}
```

```shell
curl "api.openbasin.io/datastore/bucket/standard/append" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}" \
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d bucket: "public_profiles" \
    -d standard: "profile_v1"
```

> The above command returns JSON structured like below:

```json
{
    "status": "success"
}
```

Appends a new standard to the bucket's standard whitelist.

### HTTP Request

`POST api.openbasin.io/datastore/bucket/standard/append`

### Query Parameters

Parameter | Description
--------- | -----------
bucket | The ID of the bucket to update
standard | The standard to append

## Get Buckets

```javascript
import basin from './basin.js';

const buckets = await basin.api.buckets.all.get();

console.log(buckets);
```

```swift
import OpenBasin

Basin.api.buckets.getAll { result in
    switch result {
    case .success(let buckets)
        print(buckets)
    case .error(let error)
        print(error)
    }
}
```

```shell
curl "api.openbasin.io/datastore/buckets/all" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}" \
    -d provider_address: "${PROVIDER_ADDRESS}"
```

> The above command returns JSON structured like below:

```json
[
    {
        "standard": {
            "id": string,
            "minter": address,
            "name": string,
            "description": string,
            "timestamp": date,
            "allowed_standards": string[]
        },
        "standard_id": string
    }
]
```

This endpoint retrieves all buckets

### HTTP Request

`GET api.openbasin.io/datastore/buckets/all`

### Query Parameters

No additional parameters

## Get a Specific Bucket

```javascript
import basin from './basin.js';

const bucket = await basin.api.bucket("public_profiles").get();

console.log(bucket)
```

```swift
import OpenBasin

Basin.api.bucket("public_profiles").getBucket { result in
    switch result {
    case .success(let bucket)
        print(bucket)
    case .error(let error)
        print(error)
    }
}
```

```shell
curl "api.openbasin.io/datastore/bucket" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}"\
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d id: "public_profiles"
```

> The above command returns JSON structured like this:

```json
{
    "id": string,
    "minter": address,
    "name": string,
    "description": string,
    "timestamp": date,
    "allowed_standards": string[]
}
```

This endpoint retrieves a specific bucket.

### HTTP Request

`GET api.openbasin.io/datastore/bucket`

### Query Parameters

Parameter | Description
--------- | -----------
id | The ID of the bucket to retrieve

# Documents

Documents are wrapper objects for raw data to hold the metadata of the data. They are stored within buckets and conform to a specified standard

### Properties

`token: number` - The token index of the document within its bucket

`owners: address[]` - The set of owners of the document

`standard: string` - The standard id that the data conforms to

`bucket: string` - The bucket id that the document is stored in

`timestamp: date` - The date and time at which the document was minted

`data: JSON` - THe raw data of the document

## Mint Document

```javascript
import basin from './basin.js';

await basin.mint.document(
    ["${OWNER_ADDRESS}"],
    "public_profiles",
    "profile_v0",
    {
        "name": "Drillbit Taylor",
        "image": "https://tiny.one/emmet"
    }
);
```

```swift
import OpenBasin

Basin.mint
    .document(owners: ["${OWNER_ADDRESS}"],
              bucket: "public_profiles",
              standard: "profile_v0",
              data: [
                  "name": "Drillbit Taylor",
                  "image": "https://tiny.one/emmet"
              ]) { result in
                  switch result {
                  case .success(let response)
                      print(response)
                  case .error(let error)
                      print(error)
                  }
              }
```

```shell
curl "api.openbasin.io/datastore/document/mint" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}" \
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d owners: ["${OWNER_ADDRESS}"] \
    -d bucket: "public_profiles" \
    -d standard: "profile_v0" \
    -d data: {
        "name": "Drillbit Taylor",
        "image": "https://tiny.one/emmet"
    }
```

> The above command returns JSON structured like below:

```json
{
    "status": "success"
}
```

Creates a Document and stores on Open Basin

### HTTP Request

`POST api.openbasin.io/datastore/document/mint`

### Query Parameters

Parameter | Description
--------- | -----------
bucket | The id of the document's bucket
standard | The id of the document's standard
owners | The list of owners of the document
data | The raw JSON to store

<aside class="success">
Note — Document's raw data can be updated.
</aside>

## Update Document

```javascript
import basin from './basin.js';

await basin.mint.updateDocument(
    "public_profiles",
    0,
    "profile_v0",
    {
        "name": "Alamo Taylor",
        "image": "https://tiny.one/emmet"
    }
);
```

```swift
import OpenBasin

Basin.mint
    .updateDocument(bucket: "public_profiles",
                    document: 0,
                    standard: "profile_v0",
                    data: [
                        "name": "Alamo Taylor"
                        "image": "https://tiny.one/emmet"
                    ]) { result in
                        switch result {
                        case .success(let response)
                            print(response)
                        case .error(let error)
                            print(error)
                        }
                    }
```

```shell
curl "api.openbasin.io/datastore/document/update" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}" \
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d bucket: "public_profiles" \
    -d standard: "profile_v0" \
    -d document: "0"
    -d data: {
        "name": "Alamo Taylor",
        "image": "https://tiny.one/emmet"
    }
```

> The above command returns JSON structured like below:

```json
{
    "status": "success"
}
```

Updates the raw value of the document

### HTTP Request

`POST api.openbasin.io/datastore/document/update`

### Query Parameters

Parameter | Description
--------- | -----------
document | The token of the document
bucket | The id of the document's bucket
standard | The id of the document's standard
data | The raw JSON to replace the current data

## Get Owner Documents in Bucket

```javascript
import basin from './basin.js';

const documents = await basin.api.bucket("public_profiles").owner("${OWNER_ADDRESS}").documents();

console.log(documents)
```

```swift
import OpenBasin

Basin.api.bucket("public_profiles").owner("${OWNER_ADDRESS}").getDocuments { result in
    switch result {
    case .success(let documents)
        print(documents)
    case .error(let error)
        print(error)
    }
}
```

```shell
curl "api.openbasin.io/datastore/bucket/owner/documents" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}" \
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d owner: "${OWNER_ADDRESS}" \
    -d bucket: "public_profiles"
```

> The above command returns JSON structured like this:

```json
[
    "document": {
        "token": number,
        "bucket": address,
        "standard": string,
        "owners": address[],
        "timestamp": date,
        "data": JSON
    },
    "document_token": number
]
```

Retrieves all documents in a bucket owner by a specific address

### HTTP Request

`GET api.openbasin.io/datastore/bucket/owner/document`

### Query Parameters

Parameter | Description
--------- | -----------
owner | The address of the owner
bucket | The id of the specified bucket

## Get Specific Document

```javascript
import basin from './basin.js';

const document = await basin.api.bucket("public_profiles").document(0).get();

console.log(document)
```

```swift
import OpenBasin

Basin.api.bucket("public_profiles").document(0).getDocument { result in
    switch result {
    case .success(let document)
        print(document)
    case .error(let error)
        print(error)
    }
}
```

```shell
curl "api.openbasin.io/datastore/document" \
    -H Authorization: "Bearer ${API_KEY}" \
    -d provider_id: "${PROVIDER_ID}"\
    -d provider_address: "${PROVIDER_ADDRESS}" \
    -d token: "0" \
    -d bucket: "public_profiles"
```

> The above command returns JSON structured like this:

```json
{
    "token": number,
    "bucket": address,
    "standard": string,
    "owners": address[],
    "timestamp": date,
    "data": JSON
}
```

Updates the raw value of the document

### HTTP Request

`GET api.openbasin.io/datastore/document`

### Query Parameters

Parameter | Description
--------- | -----------
token | The token of the document
bucket | The id of the document's bucket