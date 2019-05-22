# Introduction

{% hint style="warning" %}
We're busy adding and updating the NervesHub documentation to make it easier to use NervesHub. In particular, the NervesHub web user interface is being reorganized so we're only lightly documenting it now. If something doesn't make sense, please let us know via a GitHub issue or by contacting us on the \#nerves\_hub channel on the Elixir lang Slack.
{% endhint %}

NervesHub is an open-source firmware update server that works well with Nerves-based devices. A managed version is available at [nerves-hub.org](https://nerves-hub.org) and it's possible to host your own.

NervesHub provides many of the features that you'd expect in a firmware update server. Fundamentally, devices connect to the server either by polling at a pre-configured interval or by joining a long-lived [Phoenix channel](https://hexdocs.pm/phoenix/channels.html). If a firmware update is available, NervesHub provides a URL to the device and the device can update immediately or when convenient.

NervesHub leverages best practices and libraries from the Elixir community to provide a service that scales from hobbyist use to large scale industrial deployments. Since all components are open-source, there is no lock in. Extensive regression tests enable you to extend NervesHub with confidence if necessary. Of course, we hope that you will contribute improvements back.

Security is a primary concern of NervesHub:

* Cryptographic signatures on firmware updates provide end-to-end authentication
* Client-side and server-side SSL provide link authentication and encryption
* Access control within organizations to firmware updates

NervesHub provides the tools for safe remote updates. While it's not possible for the firmware update server alone to ensure that firmware updates are always safe, NervesHub does provide lightweight verification of firmware and detects misbehaving devices.

