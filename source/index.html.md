---
title: Open Basin Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - javascript
  - swift

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

> Will use User Profile for examples:


In order to interact with data on Open Basin, owners must allow you to do so.

## Request Permissions

## Remove Permissions

## Check Permissions

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
await basin.api.mint.standard(
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

Basin.mint.standard(id: "",
                    name: "",
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
    print("")
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

### URL Parameters

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

Basin.api.standards.all.getStandards { result in
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
  -d provider_id="${PROVIDER_ID}" \
  -d provider_address="${PROVIDER_ADDRESS}"
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
curl "api.openbasin.io/*" \
  -H Authorization: "Bearer ${API_KEY}" \
  -d provider_id: "${PROVIDER_ID}" \ \
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

### URL Parameters

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

## Update Bucket

## Read Buckets

# Documents

Documents are wrapper objects for raw data to hold the metadata of the data. They are stored within buckets and conform to a specified standard

### Properties

`token: integer` - The token index of the document within its bucket

`owners: address[]` - The set of owners of the document

`standard: string` - The standard id that the data conforms to

`bucket: string` - The bucket id that the document is stored in

`timestamp: date` - The date and time at which the document was minted

`data: JSON` - THe raw data of the document

## Mint Document

## Update Document

## Read Document

<aside class="warning">This documentation site is a work in progress, please reach out to the <code>&lt;maintainers&gt;</code> for more information.</aside>