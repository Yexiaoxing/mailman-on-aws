# SES Configure

SES, or Amazon Simple Email Service, enables you to send and receive email using a reliable and scalable email platform. 

Before we get started, you may read the documentaion here: https://aws.amazon.com/documentation/ses/

In this section, we will first configure SES in AWS Console.

Since SES is not available in Asia Pacific (Tokyo), the region we selected before, we choose US-East-1 (**US East (N. Virginia)**) here.

Open the link: https://console.aws.amazon.com/ses/home?region=us-east-1

## Verify a domain

Open Identity Management - [Domains](https://console.aws.amazon.com/ses/home?region=us-east-1#verified-senders-domain:),

The domain we will used in the example is **mail-aws-test.xiaoxing.us**.

Click **Verify a new domain**, enter the domain you want to use, tick DKIM checkout, and click Verify. Then a list of DNS records will be generated for you. You need to add them to your DNS management panel. For more instrucation please refer to your domain provider.

img/01-01.png

It may take up to 72 hours to get the record. When you see a green **verified** in status, go to next step.

## Getting SMTP Credentials

You need to obtain your SMTP user name and password in order to send email via mailman. Your SMTP credentials are different from your AWS credentials. For more information, see Getting Your SMTP Credentials for Amazon SES.

- Sign in to the AWS Management Console and open the Amazon SES console at https://console.aws.amazon.com/ses.
- In the navigation pane, click **SMTP Settings**.

In this pane, you can see some setting, for example,

  Server Name:	email-smtp.us-east-1.amazonaws.com
  Port:	25, 465 or 587
  Use Transport Layer Security (TLS):	Yes

- In the content pane, click **Create My SMTP Credentials**.
- In the **Create User for SMTP dialog box**, you will see that an SMTP user name has been filled in for you. You can accept this suggested user name or enter a different one. To proceed, click **Create**.
- Click **Show User SMTP Credentials**. Your SMTP credentials will be displayed on the screen; copy them and store them in a safe place. You can also click **Download Credentials** to download a file that contains your credentials.

## Setting MAIL FROM Domain (Optional)

By default, messages that you send through Amazon SES use amazonses.com (or a subdomain of that) as the MAIL FROM domain. Sender Policy Framework (SPF) authentication successfully validates these messages because the default MAIL FROM domain matches the sending mail server. It is enough for most users. But if you want to set it as your domain, follow the step.

Open https://console.aws.amazon.com/ses/home?region=us-east-1#verified-sender-details:domain:[Domain Verified Before]

Replace [Domain Verified Before] with your domain. Scroll down, expand **MAIL FROM Domain**.

In the **Set MAIL FROM Domain** dialog box, type the name of the MAIL FROM domain that you want to use. Note that this must be a subdomain of the verified domain.

Then, you should publish the DNS records as required in the pop up window.

For more information, please click http://docs.aws.amazon.com/ses/latest/DeveloperGuide/mail-from-set.html

## Configure Postfix in EC2

Please follow http://docs.aws.amazon.com/ses/latest/DeveloperGuide/postfix.html to setup postfix in your EC2 server.

## Request a Sending Limit Increase

Your Amazon SES account has "sandbox" access in region US East (N. Virginia). With sandbox access you can only send email to the Amazon SES mailbox simulator and to email addresses or domains that you have verified. To be moved out of the sandbox, please request a sending limit increase. 
