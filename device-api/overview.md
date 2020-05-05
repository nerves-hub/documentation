# Overview

Devices connect to NervesHub via the Device API. The Device API provides two modes of operation:

* A persistent Phoenix channel
* An HTTPS endpoint

NervesHub authenticates device connections via client-side certifications. Depending on the device, either intermittent polling of the HTTPS endpoint or connecting via a Phoenix channel will be more appropriate. Phoenix channel connections convey update notifications more quickly and support additional features at the expense of the device having to maintain a long running TLS connection to NervesHub.

The [`nerves_hub_link`](https://github.com/nerves-hub/nerves_hub_link) and [`nerves_hub_link_http`](https://github.com/nerves-hub/nerves_hub_link_http) libraries implement the Device API and can download and install firmware updates.

