# Configure [SES](https://aws.amazon.com/ses/)

In the SES console select the `SMTP Settings` option from the left hand menu. Click the blue `Create My SMTP Credentials` button proceed through the creation steps, taking note of the Access Key ID and the Secret Access Key that will be generated for the SMTP user.

By default the SES service will be sandboxed by AWS. This means that only certain AWS emails are valid unless explicity verified. You will need to configure the SES service to work with your target domains before emailing will be fully functional.
