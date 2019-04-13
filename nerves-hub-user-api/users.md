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
The password to use for the new account
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



