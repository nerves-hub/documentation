---
description: This document describes the endpoints used for managing users on NervesHub.
---

# Users

{% api-method method="post" host="https://api.nerves-hub.org" path="/users/register" %}
{% api-method-summary %}
Register
{% endapi-method-summary %}

{% api-method-description %}
This endpoint allows you to register for a new account.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="password" type="string" required=true %}
The password for the new account
{% endapi-method-parameter %}

{% api-method-parameter name="username" type="string" required=true %}
The username of the account
{% endapi-method-parameter %}

{% api-method-parameter name="email" type="string" required=true %}
The email address used for the account
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Account successfully created
{% endapi-method-response-example-description %}

```javascript
{
    "username": "user1234",
    "email": "user@domain.com"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=422 %}
{% api-method-response-example-description %}
Email already in use
{% endapi-method-response-example-description %}

```javascript
{
    "errors": "email already taken"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://api.nerves-hub.org" path="/users/auth" %}
{% api-method-summary %}
Authenticate
{% endapi-method-summary %}

{% api-method-description %}
Validate authentication of your account with your email and password.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="password" type="string" required=true %}
The password of the account
{% endapi-method-parameter %}

{% api-method-parameter name="email" type="string" required=true %}
The email address of the account
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Correct email and password
{% endapi-method-response-example-description %}

```
{
    "username": "user1234",
    "email": "user@domain.com"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=500 %}
{% api-method-response-example-description %}
Invalid email or password
{% endapi-method-response-example-description %}

```
{
    "errors": "authentication_failed"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://api.nerves-hub.org" path="/users/sign" %}
{% api-method-summary %}
Sign certificate
{% endapi-method-summary %}

{% api-method-description %}
Authenticate using your account email and password and then sign a user certificate signing request to generate a valid user certificate. Almost all endpoints in the User API are protected using client SSL using the certificate returned from this request.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-body-parameters %}
{% api-method-parameter name="csr" type="string" required=true %}
A PEM encoded user certificate signing request
{% endapi-method-parameter %}

{% api-method-parameter name="email" type="string" required=true %}
The email address of the account
{% endapi-method-parameter %}

{% api-method-parameter name="password" type="string" required=true %}
The password of the account
{% endapi-method-parameter %}

{% api-method-parameter name="description" type="string" required=true %}
A description to use for the certificate
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
The certificate was signed
{% endapi-method-response-example-description %}

```javascript
{
  "cert": "--BEGIN CERTIFICATE--"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=500 %}
{% api-method-response-example-description %}
Invalid email or password
{% endapi-method-response-example-description %}

```javascript
{
    "errors": "authentication_failed"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="get" host="https://api.nerves-hub.org" path="/users/me" %}
{% api-method-summary %}
Show user
{% endapi-method-summary %}

{% api-method-description %}
Return information about the user for a provided user certificate.  
  
Requires client side SSL user certificate.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Found user from valid user certificate
{% endapi-method-response-example-description %}

```javascript
{
    "username": "user1234",
    "email": "user@domain.com"
}
```
{% endapi-method-response-example %}

{% api-method-response-example httpCode=500 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    "errors": "reason"
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

