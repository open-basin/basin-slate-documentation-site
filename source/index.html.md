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

> To authorize, use this code:

```shell
# With shell, you can just pass the correct headers and
# parameters with each request.
curl "openbasin.io/*" \
  -H Authorization: "Bearer ${API_KEY}"
  -d provider_id="${PROVIDER_ID}" \
  -d provider_address="${PROVIDER_ADDRESS}"
```

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

<aside class="notice">For more information or clarity, join our <a href="https://discord.com/invite/M3NdGXgS">Discord server</a> to chat with us.</aside>

# Authentication

## Request Permissions

## Remove Permissions

## Check Permissions

# Standards

## Mint Standards

## Get Standards

```shell
# With shell, you can just pass the correct headers and
# parameters with each request.
curl "openbasin.io/*" \
  -H Authorization: "Bearer ${API_KEY}"
  -d provider_id="${PROVIDER_ID}" \
  -d provider_address="${PROVIDER_ADDRESS}"
```

```javascript

```

```swift

```

> The above command returns JSON structured like this:

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

No extra.

<aside class="success">
Note — these data standards will never change.
</aside>

## Get a Specific Standard

```shell
# With shell, you can just pass the correct headers and
# parameters with each request.
curl "openbasin.io/*" \
  -H Authorization: "Bearer ${API_KEY}"
  -d provider_id="${PROVIDER_ID}" \
  -d provider_address="${PROVIDER_ADDRESS}"
  -d id="${STANDARD_ID}"
```

```javascript

```

```swift

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

This endpoint retrieves a specific kitten.

### HTTP Request

`GET api.openbasin.io/datastore/standard`

### URL Parameters

Parameter | Description
--------- | -----------
id | The ID of the standard to retrieve


<aside class="warning">This documentation site is a work in progress, please reach out to the <code>&lt;maintainers&gt;</code> for more information.</aside>

# Buckets

## Mint Buckets

## Read Buckets

# Data

## Mint Data

## Read Data