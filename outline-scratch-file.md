

Each of these sections would be a separate tutorial essentially. It can link
out to reference documentation and explanations but should be a straight-line
journey for setting up the particular thing it is about.

- Quickstart :: A fast guide to getting up and running with NervesHub on a device with the simplest possible setup. Starting at mix nerves.new and ending at successfully shipping a firmware update.
  - Create your project
  - Set up your NervesHub product (match app name for convenience)
    - Add a Shared Secret
  - Add new deps
  - Configure NervesHubLink
  - Build and deploy on a device
  - Check that device shows up in NervesHub
  - Install CLI
  - Create firmware signing keys
  - Upload firmware signing keys
    - Using CLI
    - Describe where to find them in the Web UI
  - Modify the Nerves project
  - Build and sign firmware
  - Upload firmware
    - Using CLI
    - Describe where to do it in the Web UI
  - Create Deployment and add firmware
  - (maybe add device to Deployment, should happen automatically)
  - Check that device updates


- Production setup with NervesKey :: A step-by-step path through setting up a NervesKey device and the NervesHub configuration.
  - The choice
    - Device Certificate method :: Import device certificates ahead of time. The allowlist approach. No CA used.
    - CA method :: Signer CA + Create devices ahead of time.
    - JITP method :: Signer CA + JITP CA for the product.
  - Install CLI
  - Create your project
  - Add new deps
  - Configure NervesHubLink
  - Build and deploy on a device with NervesKey
  - Provision NervesKey
  - Set up your NervesHub org and product :: Include note on app name vs product name
    - If CA or JITP: Upload Signer cert as CA
  - If not JITP: Create device on NervesHub
    - Using CLI :: mention that it is also possible from the UI
    - If Device Certificate method: Import Device Certificate
  - Check that device show up in NervesHub
  - Create firmware signing keys
    - Option 1: NervesHub CLI, with password protection
    - Option 2: fwup without password protection, easier to script and CI
  - Upload firmware signing keys
    - Using CLI :: mention it is also possible from the UI
  - Modify the Nerves project
  - Build and sign firmware
    - If using CLI signing keys: Use CLI
    - If using fwup signing keys: Use fwup CLI
  - Upload firmware
    - Using CLI, mention also possible with UI
  - Create Deployment with firmware
  - (maybe add device to Deployment, should happen automatically)
  - Check that device updates


- Other methods
  - LocalCert
  - Other security hardware :: Overview information about TPM 2.0, ARM TrustZone and other hardware options (essentially, they are possible through OpenSSL engine mechanisms)

Later tutorials

- Archives
- Console
- First Connect Code
- Support Scripts
- Extensions
  - Health
  - Geo

Reference documentation, explanations of various concepts:

- Signer CA and authentication
- mTLS
- Firmware signing keys
-

