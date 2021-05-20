# M2M Authentication in Plandek's Nextgen Dashboards API

- [Introduction to Machine-to-Machine authentication](#introduction-to-machine-to-machine-authentication)
- [Using a M2M User](#using-a-m2m-user)
- [Using the GraphQL Playground](#using-the-graphql-playground)

## Introduction to Machine-to-Machine authentication

M2M is intended for non-interactive applications, using the client credentials grant flow from OAuth 2.0.

From the Auth0 instructions: <https://auth0.com/blog/using-m2m-authorization/> we can see:

![Client Credentials Grant Flow](https://cdn.auth0.com/docs/media/articles/api-auth/client-credentials-grant.png)

> 1. The client makes a request to the authorization server sending the client ID, the client secret, along with the audience and other claims claims.
> 2. The authorization server validates the request, and, if successful, sends a response with an access token.
> 3. The client can now use the access token to request the protected resource from the resource server.

In other words: the user sends a request to Auth0 with the `client_id` and `client_secret` of their specific M2M user, and Auth0 responds with an `access_token` that can be used in the Nextgen Dashboards API as an `Authorization` header.

## Getting an M2M User from Plandek

Plandek support will present with the `client_id` and `client_secret` of a specific M2M user which will have access to the client's data.

## Using a M2M User

The authentication flow for an M2M User follows OAuth 2.0 Client Credential flow. The process is as follows:

1. The user needs to [acquire an `access_token` from Auth0's servers](#acquiring-an-access-token)
2. The user sends the requests to Nextgen Dashboards API with an `Authentication` header with the `access_token` as a `Bearer` token.

### Acquiring an access token

The user sends a request to Auth0's servers with the following information:

- `url`: it is always `https://ada.eu.auth0.com/oauth/token`
- data (can be in json or as form params):
  - `audience`: it is always `https://ada.plandek.com/v1/`
  - `grant_type`: it is always `client_credentials`
  - `client_id`: Client ID of the Auth0 Application for this M2M User.
  - `client_secret`: Client Secret of the Auth0 Application for this M2M User.

Here is an example of a curl request:

```sh
curl --request POST \
  --url https://ada.eu.auth0.com/oauth/token \
  --header 'content-type: application/json' \
  --data '{"client_id":"MY_CLIENT_ID","client_secret":"MY_CLIENT_SECRET","audience":"https://ada.plandek.com/v1/","grant_type":"client_credentials"}'
```
The response will be something like

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIblahblahblahblahblahblahblah",
  "token_type": "Bearer"
}
```

### Calling Nextgen Dashboards API with the access token

The Nextgen Dashboards API is a GraphQL API that requires an access token in an `Authentication: Bearer MY_ACCESS_TOKEN` header.

#### Endpoint to test the token

There is a GET endpoint that will return the decoded user with their permissions, which can be used for testing that the access_token is correct. The URL to be used is: https://api.plandek.com/permissions

Here is an example of a curl request to this endpoint.

```sh
curl --request GET \
  --url https://api.plandek.com/permissions \
  --header 'authorization: Bearer MY_ACCESS_TOKEN'
```

A successful response will look like

```json
{
  "user": {
    "id": "z6zRycU123143mSQDNczqkqCs@clients",
    "authType": "M2M",
    "email": "some-m2m-email@example.org",
    "permittedClaims": ["read:stuff"],
    "prohibitedClaims": [],
    "internalUser": true,
    "userType": null,
    "connectionType": "m2m-z6zRycU123143mSQDNczqkqCs",
    "createdAt": null
  }
}
```

A failed response will look like:

```json
{"errors":{"name":"JsonWebTokenError","message":"invalid signature"}}
```

#### GraphQL requests

Here is an example of a GraphQL request as a curl:

```sh
curl 'https://api.plandek.com/graphql' \
  -H 'Connection: keep-alive' \
  -H 'accept: */*' \
  -H 'Authorization: Bearer MY_ACCESS_TOKEN'\
  -H 'content-type: application/json' \
  --data-raw '{"variables":{"clientKey": "some-client"},"query":"query ($clientKey: KeyString!){\n  client(clientKey: $clientKey) {\n    ...Client\n    __typename\n  }\n}\n\nfragment Client on Client {\n  id\n  clientKey\n  name\n  status\n  __typename\n}\n"}' \
  --compressed
```

And the response would be something like:

```json
{
  "data": {
    "client": {
      "id": "some-client",
      "clientKey": "some-client",
      "name": "Some Client",
      "status": "READY",
      "__typename": "Client"
    }
  }
}
```

## Using the GraphQL Playground

Visit <https://api.plandek.com/> to access the Playground. See [Use GraphQL Playground docs](./use-graphql-playground.md). 
