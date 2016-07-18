# EC2 Init

We use AWS EC2 to run mailman bundle.

## Login 

First, login to [EC2 Management Console](https://ap-northeast-1.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-1#).

For better speed in China, we choose ap-northeast-1 (Tokyo, Japan) node.

## Key Pairs

Create or import your SSH key pair [here](https://ap-northeast-1.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-1#KeyPairs:sort=keyName)

If you already have one ssh key pairs (used to log into a Linux machine), you can import it here。

!(img/00-00-01.png)

If not, just click **Create Key Pair** to create one. Here it will download one .pem file, the private key,

If you are running a macOS or Linux machine, place it here:

    ~/.ssh/

## Create

Second, create a EC2 vm for mailman [here](https://ap-northeast-1.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-1#LaunchInstanceWizard:)

### Step 1: Choose one AMI suitable for you

In this instance, we choose
    
    Ubuntu Server 14.04 LTS (HVM), SSD Volume Type - ami-a21529cc

img/00-01.png

### Step 2: Choose an Instance Type. Since micro instances are eligible for the AWS free usage tier, we choose it.

    Currently selected: t2.micro (Variable ECUs, 1 vCPUs, 2.5 GHz, Intel Xeon Family, 1 GiB memory, EBS only)

img/00-02.png

Click Next: Configure Instance Details

### Step 3: Configure Instance Details

We only need one instance in this example.

img/00-03.png

### Step 4: Add Storage

Free tier eligible customers can get up to 30 GB of EBS General Purpose (SSD). So we have 30 GB here.

img/00-04.png

### Step 5: Tag Instance

We don't need to change it now -- maybe later.


### Step 6: Configure Security Group

Add some rules.
- Allow SSH traffic from your IP address
- Allow all ICMP traffic from your IP address, to enable ping requests
- Allow HTTPS and HTTP traffic

img/00-06.png

### Step 7: Review Instance Launch

Review your settings here.

### Step 8: Key Pairs

Choose the keypair we imported or created before.

## Elastic IPs

An Elastic IP address is a static IP address designed for dynamic cloud computing. An Elastic IP address is associated with your AWS account. With an Elastic IP address, you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account.

Read more [here](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html).

After you read the docs, go [here](https://ap-northeast-1.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-1#Addresses:sort=publicIp).

### Allocate New Address

Click **Allocate New Address**, then system will allocate one IP address for you. Log it as [Elastic-IP]

After you get the ip, choose the ip, click **Actions** - **Associate Address**。

img/00-07.png

Click the text bot of Instance, choose the EC2 VM we created before. Then **Associate**.

## Connect to instance

https://ap-northeast-1.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-1#Instances

Click **Connect** to get instructions.

### Init the system

Type the following commands, line by line.

    sudo apt-get update # update package info
    sudo apt-get upgrade # upgrade the system
    sudo apt-get install python3-pip python-pip galternatives language-pack-en git # install some essencial packages

To set the hostname, 
- Edit /etc/hostname , make the name change, save the file.
    
    sudo echo "[hostname]" > /etc/hostname

- You should also make the same changes in /etc/hosts file.
- Run sudo /etc/init.d/hostname restart or sudo service hostname restart.

In this example [hostname] has been set to mail-aws-test.xiaoxing.us

Reboot to let the system up-to-date:

    sudo reboot
    
### Optional: Install zsh and oh-my-zsh

The Z shell (zsh) is a Unix shell that can be used as an interactive login shell and as a powerful command interpreter for shell scripting. Zsh can be thought of as an extended Bourne shell with a large number of improvements, including some features of bash, ksh, and tcsh.

First, you need to set a password for current user

    sudo passwd
    
Run

    sudo apt-get install zsh
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
