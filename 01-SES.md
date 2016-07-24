# SES Configure

SES, or Amazon Simple Email Service, enables you to send and receive email using a reliable and scalable email platform. 

Before we get started, you may read the documentaion here: https://aws.amazon.com/documentation/ses/

In this section, we will first configure SES in AWS Console.

Since SES is not available in Asia Pacific (Tokyo), the region we selected before, we choose US-East-1 (**US East (N. Virginia)**) here.

Open the link: https://console.aws.amazon.com/ses/home?region=us-east-1

## Verify a domain

Open Identity Management - [Domains](https://console.aws.amazon.com/ses/home?region=us-east-1#verified-senders-domain:),

The domain we will used in the example is **mail-aws-test.xiaoxing.us**.

Also, you need to set [DOMAIN]'s A record to [Elastic IP], and MX record to [DOMAIN].

Click **Verify a new domain**, enter the domain you want to use, tick DKIM checkout, and click Verify. Then a list of DNS records will be generated for you. You need to add them to your DNS management panel. For more instrucation please refer to your domain provider.

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

Run the following commands line by line:

    sudo apt-get remove sendmail
    sudo apt-get install postfix
    # Note: choose "Internet site" if prompted.

After you have installed postfix, use the editor you like, such as nano, vim or emacs, to edit **/etc/postfix/main.cf** file. For example here, we use nano.

    sudo nano /etc/postfix/main.cf 

Add the following lines, please be noted that if you are not using US-East-1 (**US East (N. Virginia)**) region, you will need to replace [email-smtp.us-east-1.amazonaws.com] with the smtp server you use.

    relayhost = [email-smtp.us-east-1.amazonaws.com]:465
    smtp_sasl_auth_enable = yes
    smtp_sasl_security_options = noanonymous
    smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
    smtp_use_tls = yes
    smtp_tls_security_level = encrypt
    smtp_tls_note_starttls_offer = yes

Save and close it.

Run 

    /etc/init.d/postfix reload
    
Edit or create **/etc/postfix/sasl_passwd** file. Add the following line to the file, replacing USERNAME and PASSWORD with your SMTP user name and password.

    [email-smtp.us-east-1.amazonaws.com]:465 USERNAME:PASSWORD
    ses-smtp-prod-335357831.us-east-1.elb.amazonaws.com:465 USERNAME:PASSWORD

Run

    sudo postmap hash:/etc/postfix/sasl_passwd

to create a hashmap database file containing your SMTP credentials.

Protect your database using

    sudo chown root:root /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
    sudo chmod 0600 /etc/postfix/sasl_passwd /etc/postfix/sasl_passwd.db
    
Then run

    sudo postconf -e 'smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt'

to tell Postfix where to find the CA certificate (needed to verify the Amazon SES server certificate).

After all, restart postfix to apply settings:

    sudo postfix stop
    sudo postfix start

Reference: http://docs.aws.amazon.com/ses/latest/DeveloperGuide/postfix.html

Send a test email by typing the following at a command line, pressing Enter after each line. Note that you must replace from@example.com with your "From" email address, which you must have previously verified with Amazon SES. Replace to@example.com with your "To" address. The "To" address must also be verified. Also note that the final line is a single period.

    sendmail -f from@example.com to@example.com

    From: from@example.com

    Subject: Test

    This email was sent through Amazon SES!

    .

## Request a Sending Limit Increase

Your Amazon SES account has "sandbox" access in region US East (N. Virginia). With sandbox access you can only send email to the Amazon SES mailbox simulator and to email addresses or domains that you have verified. To be moved out of the sandbox, please request a sending limit increase. 

## Have Problems?

Visit http://docs.aws.amazon.com/ses/latest/DeveloperGuide/smtp-issues.html and see if you can find a solution.
