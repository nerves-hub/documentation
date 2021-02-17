---
description: ''
---

# Provisioning in Elixir

The quickstart firmware is useful for getting started with NervesKeys. This section describes one way of provisioning NervesKeys programmatically. More sophisticated and automated ways are certainly possible.

## Prerequisites

To provision a NervesKey, make sure that you have the following:

1. A Device CA certificate and its private certificate \(this is also referred to

   as a signing certificate\)

2. A serial number for your device
3. A name for the device

The signing certificate and serial number are very important. After the provisioning process, they are locked down and cannot be changed without replacing the NervesKey. The device name is purely informational unless you choose to use it in your software.

NervesKeys support an auxillary set of certificates that identify the device. These are writable after the provisioning process. Since they're writable, they can be provisioned and updated at any time. As such, they're not programmed in the first-time provisioning process.

### Creating a Device CA certificate

If you have not already created a Device CA certificate, you will need to create one now. For simplicity, the private key is handled in the clear. Depending on your manufacturing or provisioning environment, you may want to store the private key in a hardware security module \(HSM\) or keep it encrypted.

As mentioned earlier, the NervesKey requires that the Device CA certificate adhere to the [ATECC Compressed Certificate Definition](https://www.microchip.com/wwwAppNotes/AppNotes.aspx?appnote=en591852) constraints so that it can be stored. By far the easiest way to create compliant certificates is to use the `nerves_key` `mix` commandline helpers. If you don't already have an Elixir Project that has `nerves_key` as a dependency, create one now, and run `mix deps.get` to pull down the helpers. Then run the following commands:

```bash
$ mix nerves_key.signer create nerveskey_prod_signer1
Created signing cert, nerveskey_prod_signer1.cert and private key, nerveskey_prod_signer1.key.

Please store nerveskey_prod_signer1.key in a safe place.

nerveskey_prod_signer1.cert is ready to be uploaded to the servers that need
to authenticate devices signed by the private key.
```

There is no magic in the compressible certificates. They're just limited in what they can contain. You can inspect them with `openssl x509 -in nerveskey_prod_signer1.cert -text`.

Check with your IoT service on how the signer certificate is used. If it's only used for first-time device registration, then the signer certificate may not need a long expiration time. You may also be interested in creating more than one signing certificate if you have more than one manufacturing facility.

### Manufacturer serial numbers

The NervesKey saves the manufacturing serial number in the one-time programmable memory on the ATECC508A/608A and also in the device's X.509 certificate. The device's X.509 certificate is signed, so cloud servers can trust the manufacturer serial number.

At this point, you're the manufacturer. Decide how you'd like your serial numbers to look. Whatever you pick, it must fit in 16-bytes. Representing the serial number is commonly done in ASCII. If you don't want to deal with this, do what we do \(Base32-encode a globally unique identify burned into the ATECC508A/608A\). `NervesKey.default_info/1` will create this default for you:

```elixir
iex> {:ok, i2c} = ATECC508A.Transport.I2C.init([])
{:ok, {ATECC508A.Transport.I2C, {#Reference<0.879310498.269090821.27261>, 96}}}
iex> NervesKey.default_info(i2c)
%NervesKey.ProvisioningInfo{
  board_name: "NervesKey",
  manufacturer_sn: "AER245UNQOY4T3Q"
}
```

## Provisioning

Now that you have a Device CA certificate, the Device CA's private key, and a manufacturer serial number, you can provision a NervesKey or the standalone ATECC508A/608A. Usually there's some custom manufacturing software that performs this step. We'll provision at the iex prompt.

Use `sftp` to copy the Device CA certificate and private key to your device. We'll put them `/tmp` so that they disappear on reboot:

```bash
$ sftp nerves.local
Connected to nerves.local.
sftp> cd /tmp
sftp> put nerveskey_prod_signer1.*
Uploading nerveskey_prod_signer1.cert to /tmp/nerveskey_prod_signer1.cert
nerveskey_prod_signer1.cert                                              100%  636    78.3KB/s   00:00
Uploading nerveskey_prod_signer1.key to /tmp/nerveskey_prod_signer1.key
nerveskey_prod_signer1.key                                               100%  228    78.3KB/s   00:00
sftp> exit
```

Next, go to the IEx prompt on the device and run the following:

```elixir
# Customize these or use `NervesKey.default_info/1` for defaults
cert_name="nerveskey_prod_signer1"

# These lines should be copy/paste
signer_cert = File.read!("/tmp/#{cert_name}.cert") |> X509.Certificate.from_pem!();true
signer_key = File.read!("/tmp/#{cert_name}.key") |> X509.PrivateKey.from_pem!();true

{:ok, i2c} = ATECC508A.Transport.I2C.init([])
provision_info = NervesKey.default_info(i2c)

# Double-check what you typed above before running this
NervesKey.provision(i2c, provision_info, signer_cert, signer_key)
```

If the last line returns `:ok` after about 2 seconds, then celebrate. You successfully programmed a NervesKey. You can't program it again. If you try, you'll get an error.

## Provisioning an auxiliary certificate

Auxiliary certificates are provisioned very similarly to the first time device programming. You will need to copy the Device CA certificate and private key to the device as before. This is likely a different certificate and private key, though.

The following assumes that you have already run the first time provisioning steps above.

```elixir
# Customize these
cert_name="nerveskey_prod_signer1"

# These lines should be copy/paste
signer_cert = File.read!("/tmp/#{cert_name}.cert") |> X509.Certificate.from_pem!();true
signer_key = File.read!("/tmp/#{cert_name}.key") |> X509.PrivateKey.from_pem!();true

{:ok, i2c} = ATECC508A.Transport.I2C.init([])
NervesKey.provision_aux_certificates(i2c, signer_cert, signer_key)
```

See the `NervesKey.clear_aux_certificates/1` function for erasing the auxiliary certificate.

## Settings

The `NervesKey.put_settings/2` and `NervesKey.get_settings/1` APIs let you store and retrieve a map. Since the storage is limited and relatively slow, this is intended for settings that rarely change or may be tightly coupled with certificates already being stored in the NervesKey.

Internally, `NervesKey` calls `:erlang.term_to_binary` to convert the map to raw bytes and then writes it to the ATECC508A/608A's persistent storage. Not that the keys in the map take up space as well.

