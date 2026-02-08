# Authentication

{% hint style="danger" %}
**This page is out of date.** HTTP Signatures are only used for distributed workflows. The engine uses JWT for access control.
{% endhint %}

***

### LTO API client libraries

It's recommended to use one of the LTO API client libraries if available. These libraries are configured to correctly sign requests.

{% content-ref url="../libraries/api-client-libraries.md" %}
[api-client-libraries.md](../libraries/api-client-libraries.md)
{% endcontent-ref %}

### HTTP Signature

All requests need to be signed following the [HTTP Signatures specification](https://tools.ietf.org/html/draft-cavage-http-signatures-10) using your ED25519 key set. Each request must have `Date` header. Request with a body like `POST` and `PUT` requests, must have a `Content-Type` and `Digest` header. The `(request-uri)` must also be part of the signature.

The `KeyId` must be the _**base58**_ encoded public ED25519 key. The `algorithm` may be either `ed25519` or `ed25519-sha256`. If you use the the SHA-256 version, the signature string needs to be hashed. The signature must be _**base64**_ encoded as required by the http signatures specifications.

### Digest

The HTTP signature string is only constructed from the HTTP headers, not the body. To make sure the body hasn't been manipulated, requests are required to have a `Digest` header as described in [RFC 3230](https://tools.ietf.org/html/rfc3230). The digest needs to be a SHA-256 hash.

_Note that the Digest header, is a different specification than HTTP Digest Authentication._

## Identities

In a process actors need to have an identity, before they can perform any action. Each identity has a signature and encryption key pair.

An identity can be give one of three authorization levels

* A **participant** is only able to participate in existing processes.
* A **user** is allowed to create new processes and participate in them.
* An **admin** can manage all users, scenarios and processes.

{% tabs %}
{% tab title="JSON" %}
```javascript
{
    "id": "1d25b6e0-e9d8-11e9-a36f-ff0ea1118e81",
    "signkey": "5LucyTBFqSeg8qg4e33uuLY93RZqSQZjmrtsUydUNYgg",
    "encryptkey": "CLpT61PqmYNpPH5CpJQnYKLpq4kaegjPSG4vY9rGtfm3",
    "authz": "participant"
}
```
{% endtab %}

{% tab title="JSON (full)" %}
```javascript
{
    "id": "1d25b6e0-e9d8-11e9-a36f-ff0ea1118e81",
    "signkeys": {
        "default": "5LucyTBFqSeg8qg4e33uuLY93RZqSQZjmrtsUydUNYgg"
    },
    "encryptkey": "CLpT61PqmYNpPH5CpJQnYKLpq4kaegjPSG4vY9rGtfm3",
    "authz": "participant"
}
```
{% endtab %}
{% endtabs %}

It's recommended to create key pairs using the LTO API library and only send the public keys to the service.

### Multiple sign keys

You may specify additional sign keys for an identity. The identity may use any of the sign keys to sign a request. This is typically only necessary for distributed workflows.

