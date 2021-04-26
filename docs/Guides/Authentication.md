# Authentication

For general information on how to authenticate on publiq APIs, see the [global Authentication documentation](https://publiq.stoplight.io/docs/authentication/docs/Introduction.md).

## Entry API

Most `GET` endpoints on Entry API v3 are public and don't require authentication.

Other methods like `POST`, `PUT`, `PATCH` and `DELETE` require authentication using either a [Client Access Token](https://publiq.stoplight.io/docs/authentication/docs/Authentication-methods/Client-access-token.md) **or** a [User Access Token](https://publiq.stoplight.io/docs/authentication/docs/Authentication-methods/User-access-token.md).

## Search API

Search API v3 uses no real authentication, but requires [Client Identification](https://publiq.stoplight.io/docs/authentication/docs/Authentication-methods/Client-identification.md) for customization and support reasons.


