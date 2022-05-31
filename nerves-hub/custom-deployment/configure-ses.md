# Configure SES

In the SES console select the `SMTP Settings` option from the left hand menu. Click the blue `Create My SMTP Credentials` button proceed through the creation steps, taking note of the Access Key ID and the Secret Access Key that will be generated for the SMTP user.

Later, you'll use the SMTP credentials created here to designate how Nerves Hub services send transactional emails by adding them as parameters to SSM.

By default the SES service will be sandboxed by AWS. This means that you will only be able to deliver email to validated or pre-approved AWS addresses. [You may request to be moved out of the SES sandbox](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html?icmpid=docs_ses_console) in order to send email a wide variety of domains.

Whether or not you choose to remain in the sandbox, you will need validate at least one email address to use as your `from_email` address before you can send any email through SES. In the SES console selec the `Email Addresses` option from the left hand menu. On this screen there should be a blue button prompting you to "Verify a New Email Address". Click on this button to proceed through the validation process. Once your from email address has been validated, please note the address for later configuration use.
