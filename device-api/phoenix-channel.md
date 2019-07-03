# Phoenix Channel

NervesHub exposes a WebSocket interface which utilizes [Phoenix channels](https://hexdocs.pm/phoenix/channels.html) for long lived connections. NervesHub uses SSL peer verification so the device's certificate and applicable CA certificates must be included in the connection request. The device's SSL certificate is also used to determine organization and serial number.

The connection URI is [`wss://device.nerves-hub.org/socket/websocket`](wss://device.nerves-hub.org/socket/websocket). Once connected, you can then join any of the supported channel topics to start sending and receiving messages with NervesHub.

### Message Structure

NervesHub utilizes the Phoenix message structure for all WebSocket communications. In its raw form, the message is a simple list expected to be structured as `[join_ref, ref, topic, event, message]` . \(See the [`Phoenix.Socket.Message` documetation ](https://hexdocs.pm/phoenix/Phoenix.Socket.Message.html)for more info on what each part of the message means\)

```javascript
# Example messages
["join_123", "ref-453", "some_topic", "update", "some_message"]
[null, "another-ref", "diff_topic", "response", {"key": "val"}]
[null, null, "topic3", "wat", [1, 2, 3, 4]]
```

### Joining a Channel

To communicate with NervesHub, you must join a channel on a supported topic once the websocket has been connected. This requires sending a message with the `phx_join` event to the desired topic:

```javascript
['arbitrary_join_ref', 'ref1', 'devices', 'phx_join', {}]
```

### Supported Channels

NervesHub currently supports the following channel topics:

* `device` - The main topic a device should join for receiving updates and other device specific events.
* `console` - topic for the device to send IO requests to and from NervesHub for supporting remote console interaction. For Nerves, this is the topic used from remote IEx sessions.

#### device

Client -&gt; Server events

* `phx_join`
* `rebooting`
* `fwup_progress`

Server -&gt; Client events

* `update`
* `reboot`
* `phx_error`
* `phx_close`

