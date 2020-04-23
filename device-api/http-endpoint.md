# HTTP Endpoint

{% api-method method="get" host="https://device.nerves-hub.org" path="/device/update" %}
{% api-method-summary %}
Update
{% endapi-method-summary %}

{% api-method-description %}
This endpoint allows you to poll for firmware updates.
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-headers %}
{% api-method-parameter name="X-NervesHub-uuid" type="string" required=true %}
The running firmware's UUID
{% endapi-method-parameter %}

{% api-method-parameter name="X-NervesHub-vcs\_identifier" type="string" required=false %}
A version control system tag or identifier on the running firmware if any
{% endapi-method-parameter %}

{% api-method-parameter name="X-NervesHub-version" type="string" required=true %}
The running firmware's version number
{% endapi-method-parameter %}

{% api-method-parameter name="X-NervesHub-product" type="string" required=false %}
The product name that's encoded into the running firmware
{% endapi-method-parameter %}

{% api-method-parameter name="X-NervesHub-platform" type="string" required=false %}
The running firmware's platform field
{% endapi-method-parameter %}

{% api-method-parameter name="X-NervesHub-description" type="string" required=false %}
The running firmware's description field
{% endapi-method-parameter %}

{% api-method-parameter name="X-NervesHub-author" type="string" required=false %}
The author metadata in the running firmware
{% endapi-method-parameter %}

{% api-method-parameter name="X-NervesHub-architecture" type="string" required=true %}
The device's architecture.
{% endapi-method-parameter %}
{% endapi-method-headers %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
A firmware update is available.
{% endapi-method-response-example-description %}

```javascript
{
    "update_available": true,
    "deployment_id": 12,
    "firmware_url": "Some URL",
    "firmware_meta": {
      "uuid": "12345-6789-0129435",
      "architecture": "arm",
      "platform": "rpi0",
      "product": "MyProduct",
      "version": "1.1.10",
      "author": "Ron Swanson",
      "description": "baconator 3000",
      "vcs_identifier": "some_version_control_sha",
      "misc": "random data"
    }
}
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

