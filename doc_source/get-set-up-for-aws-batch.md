# Setting Up with AWS Batch<a name="get-set-up-for-aws-batch"></a>

If you've already signed up for Amazon Web Services \(AWS\) and have been using Amazon Elastic Compute Cloud \(Amazon EC2\) or Amazon Elastic Container Service \(Amazon ECS\), you are close to being able to use AWS Batch\. The setup process for these services is very similar, as AWS Batch uses Amazon ECS container instances in its compute environments\. To use the AWS CLI with AWS Batch , you must use a version of the AWS CLI that supports the latest AWS Batch features\. If you do not see support for an AWS Batch feature in the AWS CLI, you should upgrade to the latest version\. For more information, see [http://aws\.amazon\.com/cli/](http://aws.amazon.com/cli/)\.

**Note**  
Because AWS Batch uses components of Amazon EC2, you use the Amazon EC2 console for many of these steps\.

Complete the following tasks to get set up for AWS Batch\. If you have already completed any of these steps, you may skip them and move on to installing the AWS CLI\.

1. [Sign Up for AWS](#sign-up-for-aws)

1. [Create an IAM User](#create-an-iam-user)

1. [Create IAM Roles for your Compute Environments and Container Instances](#create-an-iam-role)

1. [Create a Key Pair](#create-a-key-pair)

1. [Create a Virtual Private Cloud](#create-a-vpc)

1. [Create a Security Group](#create-a-base-security-group)

1. [Install the AWS CLI](#install_aws_cli)

## Sign Up for AWS<a name="sign-up-for-aws"></a>

When you sign up for AWS, your AWS account is automatically signed up for all services, including Amazon EC2 and AWS Batch\. You are charged only for the services that you use\.

If you have an AWS account already, skip to the next task\. If you don't have an AWS account, use the following procedure to create one\.

**To create an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

Note your AWS account number, because you'll need it for the next task\.

## Create an IAM User<a name="create-an-iam-user"></a>

Services in AWS, such as Amazon EC2 and AWS Batch, require that you provide credentials when you access them, so that the service can determine whether you have permission to access its resources\. The console requires your password\. You can create access keys for your AWS account to access the command line interface or API\. However, we don't recommend that you access AWS using the credentials for your AWS account; we recommend that you use AWS Identity and Access Management \(IAM\) instead\. Create an IAM user, and then add the user to an IAM group with administrative permissions or grant this user administrative permissions\. You can then access AWS using a special URL and the IAM user's credentials\.

If you signed up for AWS but have not created an IAM user for yourself, you can create one using the IAM console\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add user**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \-job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM Entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

To sign in as this new IAM user, sign out of the AWS console, then use the following URL, where *your\_aws\_account\_id* is your AWS account number without the hyphens \(for example, if your AWS account number is `1234-5678-9012`, your AWS account ID is `123456789012`\):

```
https://your_aws_account_id.signin.aws.amazon.com/console/
```

Enter the IAM user name and password that you just created\. When you're signed in, the navigation bar displays "*your\_user\_name* @ *your\_aws\_account\_id*"\.

If you don't want the URL for your sign\-in page to contain your AWS account ID, you can create an account alias\. From the IAM dashboard, choose **Create Account Alias** and enter an alias, such as your company name\. To sign in after you create an account alias, use the following URL:

```
https://your_account_alias.signin.aws.amazon.com/console/
```

To verify the sign\-in link for IAM users for your account, open the IAM console and check under **IAM users sign\-in link** on the dashboard\.

For more information about IAM, see the [AWS Identity and Access Management User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)\.

## Create IAM Roles for your Compute Environments and Container Instances<a name="create-an-iam-role"></a>

Your AWS Batch compute environments and container instances require AWS account credentials to make calls to other AWS APIs on your behalf\. You must create an IAM role that provides these credentials to your compute environments and container instances, then associate that role with your compute environments\.

**Note**  
The AWS Batch compute environment and container instance roles are automatically created for you in the console first\-run experience, so if you intend to use the AWS Batch console, you can move ahead to the next section\. If you plan to use the AWS CLI instead, complete the procedures in [AWS Batch Service IAM Role](service_IAM_role.md) and [Amazon ECS Instance Role](instance_IAM_role.md) before creating your first compute environment\.

## Create a Key Pair<a name="create-a-key-pair"></a>

AWS uses public\-key cryptography to secure the login information for your instance\. A Linux instance, such as an AWS Batch compute environment container instance, has no password to use for SSH access; you use a key pair to log in to your instance securely\. You specify the name of the key pair when you create your compute environment, then provide the private key when you log in using SSH\. 

If you haven't created a key pair already, you can create one using the Amazon EC2 console\. Note that if you plan to launch instances in multiple regions, you'll need to create a key pair in each region\. For more information about regions, see [Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To create a key pair**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select a region for the key pair\. You can select any region that's available to you, regardless of your location: however, key pairs are specific to a region\. For example, if you plan to launch an instance in the US West \(Oregon\) region, you must create a key pair for the instance in the same region\.

1. In the navigation pane, choose **Key Pairs**, **Create Key Pair**\.

1. In the **Create Key Pair** dialog box, for **Key pair name**, enter a name for the new key pair , and choose **Create**\. Choose a name that is easy for you to remember, such as your IAM user name, followed by `-key-pair`, plus the region name\. For example, *me*\-key\-pair\-*uswest2*\.

1. The private key file is automatically downloaded by your browser\. The base file name is the name you specified as the name of your key pair, and the file name extension is `.pem`\. Save the private key file in a safe place\.
**Important**  
This is the only chance for you to save the private key file\. You'll need to provide the name of your key pair when you launch an instance and the corresponding private key each time you connect to the instance\.

1. If you will use an SSH client on a Mac or Linux computer to connect to your Linux instance, use the following command to set the permissions of your private key file so that only you can read it\.

   ```
   $ chmod 400 your_user_name-key-pair-region_name.pem
   ```

For more information, see [Amazon EC2 Key Pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To connect to your instance using your key pair**  
To connect to your Linux instance from a computer running Mac or Linux, specify the `.pem` file to your SSH client with the `-i` option and the path to your private key\. To connect to your Linux instance from a computer running Windows, you can use either MindTerm or PuTTY\. If you plan to use PuTTY, you'll need to install it and use the following procedure to convert the `.pem` file to a `.ppk` file\.<a name="prepare-for-putty"></a>

**\(Optional\) To prepare to connect to a Linux instance from Windows using PuTTY**

1. Download and install PuTTY from [ http://www\.chiark\.greenend\.org\.uk/\~sgtatham/putty/](http://www.chiark.greenend.org.uk/~sgtatham/putty/)\. Be sure to install the entire suite\.

1. Start PuTTYgen \(for example, from the **Start** menu, choose **All Programs, PuTTY, and PuTTYgen**\)\.

1. Under **Type of key to generate**, choose **SSH\-2 RSA**\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/puttygen-key-type.png)

1. Choose **Load**\. By default, PuTTYgen displays only files with the extension `.ppk`\. To locate your `.pem` file, choose the option to display files of all types\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/batch/latest/userguide/images/puttygen-load-key.png)

1. Select the private key file that you created in the previous procedure and choose **Open**\. Choose **OK** to dismiss the confirmation dialog box\.

1. Choose **Save private key**\. PuTTYgen displays a warning about saving the key without a passphrase\. Choose **Yes**\.

1. Specify the same name for the key that you used for the key pair\. PuTTY automatically adds the `.ppk` file extension\.

## Create a Virtual Private Cloud<a name="create-a-vpc"></a>

Amazon Virtual Private Cloud \(Amazon VPC\) enables you to launch AWS resources into a virtual network that you've defined\. We strongly suggest that you launch your container instances in a VPC\. 

If you have a default VPC, you also can skip this section and move to the next task, [Create a Security Group](#create-a-base-security-group)\. To determine whether you have a default VPC, see [Supported Platforms in the Amazon EC2 Console](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-platforms.html#console-updates) in the *Amazon EC2 User Guide for Linux Instances*\. Otherwise, you can create a nondefault VPC in your account using the steps below\.

**Important**  
If your account supports EC2\-Classic in a region, then you do not have a default VPC in that region\.

**To create a nondefault VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. From the navigation bar, select a region for the VPC\. VPCs are specific to a region, so you should select the same region in which you created your key pair\.

1. On the VPC dashboard, choose **Start VPC Wizard**\.

1. On the **Step 1: Select a VPC Configuration** page, ensure that **VPC with a Single Public Subnet** is selected, and choose **Select**\.

1. On the **Step 2: VPC with a Single Public Subnet** page, enter a friendly name for your VPC for **VPC name**\. Leave the other default configuration settings, and choose **Create VPC**\. On the confirmation page, choose **OK**\.

For more information about Amazon VPC, see [What is Amazon VPC?](https://docs.aws.amazon.com/vpc/latest/userguide/) in the *Amazon VPC User Guide*\.

## Create a Security Group<a name="create-a-base-security-group"></a>

Security groups act as a firewall for associated compute environment container instances, controlling both inbound and outbound traffic at the container instance level\. You can add rules to a security group that enable you to connect to your container instance from your IP address using SSH\. You can also add rules that allow inbound and outbound HTTP and HTTPS access from anywhere\. Add any rules to open ports that are required by your tasks\.

Note that if you plan to launch container instances in multiple regions, you need to create a security group in each region\. For more information, see [Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**Note**  
You need the public IP address of your local computer, which you can get using a service\. For example, we provide the following service: [http://checkip\.amazonaws\.com/](http://checkip.amazonaws.com/) or [https://checkip\.amazonaws\.com/](https://checkip.amazonaws.com/)\. To locate another service that provides your IP address, use the search phrase "what is my IP address\." If you are connecting through an Internet service provider \(ISP\) or from behind a firewall without a static IP address, you need to find out the range of IP addresses used by client computers\.

**To create a security group with least privilege**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. From the navigation bar, select a region for the security group\. Security groups are specific to a region, so you should select the same region in which you created your key pair\.

1. In the navigation pane, choose **Security Groups**, **Create Security Group**\.

1. Enter a name for the new security group and a description\. Choose a name that is easy for you to remember, such as your IAM user name, followed by \_SG\_, plus the region name\. For example, *me*\_SG\_*useast1*\.

1. In the **VPC** list, ensure that your default VPC is selected; it's marked with an asterisk \(\*\)\.
**Note**  
If your account supports EC2\-Classic, select the VPC that you created in the previous task\.

1. AWS Batch container instances do not require any inbound ports to be open\. However, you might want to add an SSH rule so you can log into the container instance and examine the containers in jobs with Docker commands\. You can also add rules for HTTP if you want your container instance to host a job that runs a web server\. Complete the following steps to add these optional security group rules\.

   On the **Inbound** tab, create the following rules and choose **Create**:
   + Choose **Add Rule**\. For **Type**, choose **HTTP**\. For **Source**, choose **Anywhere** \(`0.0.0.0/0`\)\.
   + Choose **Add Rule**\. For **Type**, choose **SSH**\. For **Source**, ensure that **Custom IP** is selected, and specify the public IP address of your computer or network in CIDR notation\. To specify an individual IP address in CIDR notation, add the routing prefix `/32`\. For example, if your IP address is `203.0.113.25`, specify `203.0.113.25/32`\. If your company allocates addresses from a range, specify the entire range, such as `203.0.113.0/24`\.
**Note**  
For security reasons, we don't recommend that you allow SSH access from all IP addresses \(`0.0.0.0/0`\) to your instance, except for testing purposes and only for a short time\.

## Install the AWS CLI<a name="install_aws_cli"></a>

To use the AWS CLI with AWS Batch, install the latest AWS CLI version\. For information about installing the AWS CLI or upgrading it to the latest version, see [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) in the *AWS Command Line Interface User Guide*\.