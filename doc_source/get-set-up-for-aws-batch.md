# Setting up with AWS Batch<a name="get-set-up-for-aws-batch"></a>

If you've already signed up for Amazon Web Services \(AWS\) and are using Amazon Elastic Compute Cloud \(Amazon EC2\) or Amazon Elastic Container Service \(Amazon ECS\), you can soon use AWS Batch\. The setup process for these services is similar\. This is because AWS Batch uses Amazon ECS container instances in its compute environments\. To use the AWS CLI with AWS Batch , you must use a version of the AWS CLI that supports the latest AWS Batch features\. If you don't see support for an AWS Batch feature in the AWS CLI, upgrade to the latest version\. For more information, see [http://aws\.amazon\.com/cli/](http://aws.amazon.com/cli/)\.

**Note**  
Because AWS Batch uses components of Amazon EC2, you use the Amazon EC2 console for many of these steps\.

Complete the following tasks to get set up for AWS Batch\. If you already completed any of these steps, you can skip directly to installing the AWS CLI\.

1. [Sign up for AWS](#sign-up-for-aws)

1. [Create an IAM user](#create-an-iam-user)

1. [Create IAM roles for your compute environments and container instances](#create-an-iam-role)

1. [Create a key pair](#create-a-key-pair)

1. [Create a VPC](#create-a-vpc)

1. [Create a security group](#create-a-base-security-group)

1. [Install the AWS CLI](#install_aws_cli)

## Sign up for AWS<a name="sign-up-for-aws"></a>

When you sign up for AWS, your AWS account is automatically signed up for all the services, including Amazon EC2 and AWS Batch\. You're charged only for the services that you use\.

If you have an AWS account already, skip to the next task\. If you don't have an account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html#aws_tasks-that-require-root)\.

Note your AWS account number because you need it for the next task\.

## Create an IAM user<a name="create-an-iam-user"></a>

AWS services, such as Amazon EC2 and AWS Batch, require that you provide credentials when you access them\. That way, the service can determine whether you have permission to access its resources\. The console requires your password\. You can create access keys for your AWS account to access the command line interface or API\. However, we don't recommend that you access AWS using the credentials for your AWS account\. Instead, we recommend that you use AWS Identity and Access Management \(IAM\) and create an IAM user\. Then, add the user to an IAM group with administrative permissions or grant the user administrative permissions\. You can then access AWS using a special URL and the IAM user's credentials\.

If you signed up for AWS but didn't create an IAM user for yourself, you can create one using the IAM console\.

To create an administrator user, choose one of the following options\.


****  

| Choose one way to manage your administrator | To | By | You can also | 
| --- | --- | --- | --- | 
| In IAM Identity Center \(Recommended\) | Use short\-term credentials to access AWS\.This aligns with the security best practices\. For information about best practices, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-users-federation-idp) in the *IAM User Guide*\. | Following the instructions in [Getting started](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html) in the AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide\. | Configure programmatic access by [Configuring the AWS CLI to use AWS IAM Identity Center \(successor to AWS Single Sign\-On\)](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) in the AWS Command Line Interface User Guide\. | 
| In IAM \(Not recommended\) | Use long\-term credentials to access AWS\. | Following the instructions in [Creating your first IAM admin user and user group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the IAM User Guide\. | Configure programmatic access by [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 

To sign in as this new IAM user, sign out of the AWS console\. Then, use the following URL, where *your\_aws\_account\_id* is your AWS account number without the hyphens\. For example, if your AWS account number is `1234-5678-9012`, your AWS account ID is `123456789012`\.

```
https://your_aws_account_id.signin.aws.amazon.com/console/
```

Enter the IAM user name and password that you just created\. When you're signed in, the navigation bar displays "*your\_user\_name* @ *your\_aws\_account\_id*\."

If you don't want the URL for your sign\-in page to contain your AWS account ID, you can create an account alias\. From the IAM dashboard, choose **Create Account Alias** and enter an alias, such as your company name\. To sign in after you create an account alias, use the following URL:

```
https://your_account_alias.signin.aws.amazon.com/console/
```

To verify the sign\-in link for IAM users for your account, open the IAM console and check under **IAM users sign\-in link** on the dashboard\.

For more information about IAM, see the [AWS Identity and Access Management User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

## Create IAM roles for your compute environments and container instances<a name="create-an-iam-role"></a>

Your AWS Batch compute environments and container instances require AWS account credentials to make calls to other AWS APIs on your behalf\. Create an IAM role that provides these credentials to your compute environments and container instances, then associate that role with your compute environments\.

**Note**  
The AWS Batch compute environment and container instance roles are automatically created for you in the console first\-run experience\. So, if you intend to use the AWS Batch console, you can move ahead to the next section\. If you plan to use the AWS CLI instead, complete the procedures in [AWS Batch service IAM role](service_IAM_role.md) and [Amazon ECS instance role](instance_IAM_role.md) before creating your first compute environment\.

## Create a key pair<a name="create-a-key-pair"></a>

AWS uses public\-key cryptography to secure the login information for your instance\. A Linux instance, such as an AWS Batch compute environment container instance, has no password to use for SSH access\. You use a key pair to log in to your instance securely\. You specify the name of the key pair when you create your compute environment, then provide the private key when you log in using SSH\. 

If you didn't create a key pair already, you can create one using the Amazon EC2 console\. Note that, if you plan to launch instances in multiple AWS Regions, create a key pair in each Region\. For more information about Regions, see [Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To create a key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select an AWS Region for the key pair\. You can select any Region that's available to you, regardless of your location: however, key pairs are specific to a Region\. For example, if you plan to launch an instance in the US West \(Oregon\) Region, create a key pair for the instance in the same Region\.

1. In the navigation pane, choose **Key Pairs**, **Create Key Pair**\.

1. In the **Create Key Pair** dialog box, for **Key pair name**, enter a name for the new key pair , and choose **Create**\. Choose a name that you can remember, such as your IAM user name, followed by `-key-pair`, plus the Region name\. For example, *me*\-key\-pair\-*uswest2*\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name that you specified as the name of your key pair, and the file name extension is `.pem`\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\. You need to provide the name of your key pair when you launch an instance and the corresponding private key each time that you connect to the instance\.

1. If you use an SSH client on a Mac or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file\. That way, only you can read it\.

   ```
   $ chmod 400 your_user_name-key-pair-region_name.pem
   ```

For more information, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To connect to your instance using your key pair**  
To connect to your Linux instance from a computer running Mac or Linux, specify the `.pem` file to your SSH client with the `-i` option and the path to your private key\. To connect to your Linux instance from a computer running Windows, use either MindTerm or PuTTY\. If you plan to use PuTTY, install it and use the following procedure to convert the `.pem` file to a `.ppk` file\.<a name="prepare-for-putty"></a>

**\(Optional\) To prepare to connect to a Linux instance from Windows using PuTTY**

1. Download and install PuTTY from [ http://www\.chiark\.greenend\.org\.uk/\~sgtatham/putty/](http://www.chiark.greenend.org.uk/~sgtatham/putty/)\. Be sure to install the entire suite\.

1. Start PuTTYgen \(for example, from the **Start** menu, choose **All Programs, PuTTY, and PuTTYgen**\)\.

1. Under **Type of key to generate**, choose **RSA**\. If you're using an earlier version of PuTTYgen, choose **SSH\-2 RSA**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/puttygen-key-type.png)

1. Choose **Load**\. By default, PuTTYgen displays only files with the extension `.ppk`\. To locate your `.pem` file, choose the option to display files of all types\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/puttygen-load-key.png)

1. Select the private key file that you created in the previous procedure and choose **Open**\. Choose **OK** to dismiss the confirmation dialog box\.

1. Choose **Save private key**\. PuTTYgen displays a warning about saving the key without a passphrase\. Choose **Yes**\.

1. Specify the same name for the key that you used for the key pair\. PuTTY automatically adds the `.ppk` file extension\.

## Create a VPC<a name="create-a-vpc"></a>

With Amazon Virtual Private Cloud \(Amazon VPC\), you can launch AWS resources into a virtual network that you've defined\. We strongly recommend that you launch your container instances in a VPC\. 

If you have a default VPC, you also can skip this section and move to the next task [Create a security group](#create-a-base-security-group)\. To determine whether you have a default VPC, see [Supported Platforms in the Amazon EC2 Console](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-platforms.html#console-updates) in the *Amazon EC2 User Guide for Linux Instances*

For information about how to create an Amazon VPC, see [Create a VPC only](https://docs.aws.amazon.com/vpc/latest/userguide/working-with-vpcs.html#create-vpc-vpc-only) in the *Amazon VPC User Guide*\. Refer to the following table to determine what options to select\.


| Option | Value | 
| --- | --- | 
|  Resources to create  | VPC only | 
| Name |  Optionally provide a name for your VPC\.  | 
| IPv4 CIDR block |  IPv4 CIDR manual input The CIDR block size must have a size between /16 and /28\.  | 
|  IPv6 CIDR block  |  No IPv6 CIDR block  | 
|  Tenancy  |  Default  | 

For more information about Amazon VPC, see [What is Amazon VPC?](https://docs.aws.amazon.com/vpc/latest/userguide/) in the *Amazon VPC User Guide*\.

## Create a security group<a name="create-a-base-security-group"></a>

Security groups act as a firewall for associated compute environment container instances, controlling both inbound and outbound traffic at the container instance level\. A security group can be used only in the VPC for which it is created\.

You can add rules to a security group that enable you to connect to your container instance from your IP address using SSH\. You can also add rules that allow inbound and outbound HTTP and HTTPS access from anywhere\. Add any rules to open ports that are required by your tasks\. 

Note that if you plan to launch container instances in multiple Regions, you need to create a security group in each Region\. For more information, see [Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Note**  
You need the public IP address of your local computer, which you can get using a service\. For example, we provide the following service: [http://checkip\.amazonaws\.com/](http://checkip.amazonaws.com/) or [https://checkip\.amazonaws\.com/](https://checkip.amazonaws.com/)\. To locate another service that provides your IP address, use the search phrase "what is my IP address\." If you're connecting through an Internet service provider \(ISP\) or from behind a firewall without a static IP address, find out the range of IP addresses that are used by client computers\.

**To create a security group using the console**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**\.

1. Choose **Create security group**\.

1. Enter a name and description for the security group\. You cannot change the name and description of a security group after it is created\.

1. From **VPC**, choose the VPC\.

1. \(Optional\) By default, new security groups start with only an outbound rule that allows all traffic to leave the resource\. You must add rules to enable any inbound traffic or to restrict the outbound traffic\.

   AWS Batch container instances don't require any inbound ports to be open\. However, you might want to add an SSH rule\. That way, you can log into the container instance and examine the containers in jobs with Docker commands\. If you want your container instance to host a job that runs a web server, you can also add rules for HTTP\. Complete the following steps to add these optional security group rules\.

   On the **Inbound** tab, create the following rules and choose **Create**:
   + Choose **Add Rule**\. For **Type**, choose **HTTP**\. For **Source**, choose **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **Add Rule**\. For **Type**, choose **SSH**\. For **Source**, choose **Custom IP**, and specify the public IP address of your computer or network in Classless Inter\-Domain Routing \(CIDR\) notation\. If your company allocates addresses from a range, specify the entire range, such as `203.0.113.0/24`\. To specify an individual IP address in CIDR notation, choose **My IP**\. This adds the routing prefix `/32` to the public IP address\.
**Note**  
For security reasons, we don't recommend that you allow SSH access from all IP addresses \(`0.0.0.0/0`\) to your instance but only for testing purposes and only for a short time\.

1. You can add tags now, or you can add them later\. To add a tag, choose **Add new tag** and enter the tag key and value\.

1. Choose **Create security group**\.

To create a security group using the command line, see [create\-security\-group](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-security-group.html) \(AWS CLI\)

For more information about security groups, see [Work with security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html#working-with-security-groups)\.

## Install the AWS CLI<a name="install_aws_cli"></a>

To use the AWS CLI with AWS Batch, install the latest AWS CLI version\. For information about installing the AWS CLI or upgrading it to the latest version, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.