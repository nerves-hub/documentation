# Phoenix Channel

NervesHub exposes a WebSocket interface which utilizes [Phoenix channels](https://hexdocs.pm/phoenix/channels.html) for long lived connections. NervesHub uses SSL peer verification so the device's certificate and applicable CA certificates must be included in the connection request. The device's SSL certificate is also used to determine organization and serial number.

The connection URI is `wss://device.nerves-hub.org/socket/websocket`. Once connected, you can then join any of the supported channel topics to start sending and receiving messages with NervesHub.

## Message Structure

NervesHub utilizes the Phoenix message structure for all WebSocket communications. In its raw form, the message is a simple list expected to be structured as `[join_ref, ref, topic, event, payload]` . \(See the [`Phoenix.Socket.Message` documetation ](https://hexdocs.pm/phoenix/Phoenix.Socket.Message.html)for more info on what each part of the message means\)

```javascript
# Example messages
["join_123", "ref-453", "some_topic", "update", "some_payload"]
[null, "another-ref", "diff_topic", "response", {"key": "val"}]
[null, null, "topic3", "wat", [1, 2, 3, 4]]
```

## Joining a Channel

To communicate with NervesHub, you must join a channel on a supported topic once the websocket has been connected. This requires sending a message with the `phx_join` event to the desired topic:

```javascript
['arbitrary_join_ref', 'ref1', 'devices', 'phx_join', {}]
```

## Supported Topics

NervesHub currently supports the following channel topics:

* `device` - The main topic a device should join for receiving updates and other device specific events.
* `console` - topic for the device to send IO requests to and from NervesHub for supporting remote console interaction. For Nerves, this is the topic used from remote IEx sessions.

## Server Events

The supported events in messages coming from the server \(NervesHub\) to the client:

### **device**

* `update`
  * Specifies that an update is available for the device
  * Payload fields:
    * `update_available` - Boolean stating update availability
    * `deployment_id` - ID of the deployment triggering the update
    * `firmware_url` - URL where the firmware file can be downloaded. **Note**: this has a default TTL of 10 minutes. Using the URL after that time will fail and a new update request will need to be sent for a new URL
    * `firmware_meta` - Contains a map of the various metadata elements for the firmware
      * `uuid`
      * `architecture` 
      * `platform` 
      * `product` 
      * `version` 
      * `author` 
      * `description` 
      * `vcs_identifier` 
      * `misc`

```javascript
[
  null,
  "some-ref-1",
  "devices",
  "update",
  {
    "update_available": true,
    "deployment_id": 12,
    "firmware_url": "https://some-url.com",
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
]
```

* `reboot`
  * Request that device reboot. Typlically used for troubleshooting purposes
  * Payload is not used and can be ignored

```javascript
[null, "some-ref-1", "devices", "reboot", {}]
```

* `phx_err`
  * Error case, such as a channel process crashing, or when attempting to join an already joined channel
  * Payload fields:
    * `reason` - text of failure reason

```javascript
[null, "some-ref-1", "devices", "phx_err", {"reason": "some reason"}]
```

* `phx_close`
  * Channel was gracefully closed
  * Payload fields:
    * tbd

```javascript
[null, "some-ref-1", "devices", "phx_close", {}]
```

## Client Events

The supported event messages coming from the client to server \(NervesHub\)

### device

* `rebooting`
  * Tells the server that the device is rebooting
  * Payload is ignored server-side

```javascript
[null, "some-ref-1", "devices", "rebooting", {}]
```

* `fwup_progress`
  * Progress update during a firmware update
  * Payload fields:
    * `value` - percentage of update progress

```javascript
[
  null,
  "some-ref-1",
  "devices",
  "fwup_progress",
  {"value": 42}
]
```

* `status_update`
  * Current status of the device as it relates to a firmware update
  * Payload fields:
    * `status` - One of the supported statuses for a device
      * `idle` - waiting for an update
      * `fwup_error` - error occurred in the fwup process
      * `update_failed`
      * `update_rescheduled`
      * `unknown` - generic status for unhandled states

```javascript
[
  null,
  "some-ref-1",
  "devices",
  "status_update",
  {"state": "update_rescheduled"}
]
```

