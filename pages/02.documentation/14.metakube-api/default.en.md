---
title: 'MetaKube API'
taxonomy:
    tag:
        - metakube
        - api
        - serviceaccounts
---

MetaKube provides a REST API that can be used to automate the management of MetaKube clusters.

## API Documentation

You can find the full API documentation in the MetaKube Dashboard under [API Docs](https://metakube.syseleven.de/rest-api). Note that the executable examples in the API docs use your personal API access token. For integrations into other tools you should use a token from an API Account.

We also provider a [Swagger](https://swagger.io/resources/open-api/) OpenAPI specification at [https://metakube.syseleven.de/api/swagger.json](https://metakube.syseleven.de/api/swagger.json).

## Location

All resources of the REST API are located under `https://metakube.syseleven.de/api`.

## Authentication

In order to authenticate with the REST API programmatically, you can use MetaKube API Accounts. API Accounts live on the project level and can either have full admin permissions (Editor) or read only permissions (Viewer).

To create an API Account, create one in the interface:

![Create API Account](metakube-api-account.png)

And then create a Token for this API Account:

![Create Token](metakube-api-token.png)

This Token can then be used as a `Bearer` token in the `Authentication` header of the API request.

### Example

To get a list of clusters in the project where the API Account was created in, call:

```bash
curl 'https://metakube.syseleven.de/api/v1/projects/<PROJECT_ID>/clusters' -H 'authorization: Bearer <YOUR_TOKEN>' -H 'accept: application/json'
```
