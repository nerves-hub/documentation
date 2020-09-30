---
description: >-
  Endpoints used for managing firmware signing keys on NervesHub through the
  User API.
---

# Firmware Signing Keys

For more information about firmware signing keys:

{% page-ref page="../nerves-hub/setup/firmware-signing-keys.md" %}

{% api-method method="get" host="https://api.nerves-hub.org" path="/orgs/:org\_name/keys" %}
{% api-method-summary %}
List
{% endapi-method-summary %}

{% api-method-description %}
List all public firmware signing keys for the organization.**Requires:** client side SSL user certificate  
**Requires:** organization role `read`
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="org\_name" type="string" required=true %}
The name of the organization
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    "data": [
        {
            "name": "test",
            "key": "h8L1FcfK8mUSnIvM0rffjoapItsI4fimIU9x9Jq2TdU="
        }
    ]
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

{% api-method method="post" host="https://api.nerves-hub.org" path="/orgs/:org\_name/keys" %}
{% api-method-summary %}
Insert
{% endapi-method-summary %}

{% api-method-description %}
Insert a new firmware signing public key for the organization**Requires:** client side SSL user certificate  
**Requires:** organization role `write`
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name=":org\_name" type="string" required=true %}
The name of the organization
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-body-parameters %}
{% api-method-parameter name="name" type="string" required=true %}
A friendly name to reference the firmware signing key
{% endapi-method-parameter %}

{% api-method-parameter name="key" type="string" required=true %}
The firmware signing public key
{% endapi-method-parameter %}
{% endapi-method-body-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```javascript
{
    "data": {
        "name": "test",
        "key": "h8L1FcfK8mUSnIvM0rffjoapItsI4fimIU9x9Jq2TdU="
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

