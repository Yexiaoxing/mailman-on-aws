# Mailman 3 User Manual

## Archiver -- HyperKitty

Open your [mailman3 domain]/archives, it is the HyperKitty interface. 

### Search

On the most top navbar, there is a search bar. You can search everything in all mailing lists hosted in your server. 

### Available Lists

You can see all available lists in the frontpage. Sorting via popularity, activity, name or time is also okay.

### Archive

Click one of the list available, and you will see the archiver page of the list. 

The most left sidebar is time picker. You can choose the time period you want to read mail from. There is also a **Download** button, you can download all messages in the list.

In the middle column, Activity Summary shows the post volume from the past 30 days. By clicking the manage button you will be navigated to the admin panel. **Start a new thread** allows you to start a new thread and sends to all users. **Manage subscription** makes you able to manage your subscription. *Note: you need to login before starting.*

Also you will see the most active posters here.

In the right column, all posts show. RECENTLY ACTIVE DISCUSSIONS, MOST POPULAR DISCUSSIONS and MOST ACTIVE DISCUSSIONS are shown here. 

#### Discussion

Click one of the discussion, you will see the full content of the whole discussion. You can reply or like the post here. Also you can add tags or favour the post.

## Manager -- Postorius

### Domains

http://[domain]/mailman3/domains/

Here you can create a new domain or delete a old one.

### Lists

#### Create a new list

http://[domain]/mailman3/lists/new/

Enter the list name (no space allowed),  choose the host created in ###Domains and enter the description. 

#### Manage a list

/mailman3/lists/

Choose the list you want to manage.

##### Subscription Requests

You can approve or reject subscription requests.

##### Held Messages

You can approve or reject an held message.

##### Members

To add other email addresses which can administer the list, put them in The list administrator email addresses field. This is so that users can report any problems easily. 

Here you can add or delete the owners, moderators and members of the list. Also you can export the member list.

##### Mass Subscribe / Removal

You give a batch of email address and they will subscribe or be removed from the list. 

##### Archivers

Choose the archivers you want to use.

##### Delete list

Permanately delete one list.

##### Settings

List settings

###### Identity

You can change the name or description of the list here.

###### Automatic Responses

Auto respond to the mails send to the list or special functional mailbox related to the list. e.g. list owner.

###### Alter Messages

Alter some information of the email. For example, convert HTML to plain text. 

###### Digest

Set the digest size threshold here.

##### Message Acceptance

Accept when a non-member posts to the list? Set it here.

##### Archiving

Do you want the archive shown to public? Set the archive policy here.

##### Subscription Policy

Confirm, Moderate, both or neither? Set the policy here.

### My Settings

In this menu you can set your identity or subscription settings.

#### Profiles

/mailman3/accounts/profile/

You will see your username, display name and email here.

#### Subscription Settings

Manage your subscription settings here. You can set it globally, address-based or subscription-based. For example, you want to conceal yourself from the subscriber list? Set the mode of delivery? Go here.

#### Subscription

You will see your subscriptions here. 

#### Add Email Address

Add another email address to your account here.

## Login

You can login here: /archives/accounts/login/

Use your account, or any third-party accounts the server manager allowed.
