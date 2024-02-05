## Table of Contents

  - [Regions](#regions)
  - [Identity and Access Management](#identity-and-access-management)
  - [AWS CLI](#aws-cli)
    - [Set up](#set-up)
    - [Configuration](#configuration)
  - [EC2](#ec2)
    - [Find instance type](#find-instance-type)
    - [Find image](#find-image)
    - [Run commands at launch](#run-commands-at-launch)
    - [EC2 Image Builder](#ec2-image-builder)
  - [Use same IP address](#use-same-ip-address)
  - [Mount EBS](#mount-ebs)
  - [S3 commands](#s3-commands)

## Regions

| Name                       | Region         |
| -------------------------- | -------------- |
| Asia Pacific (Tokyo)       | ap-northeast-1 |
| Asia Pacific (Sydney)      | ap-southeast-2 |
| US East (Ohio)             | us-east-2      |
| US East (N. Virginia)      | us-east-1      |
| US West (N. California)    | us-west-1      |
| US West (Oregon)           | us-west-2      |
| Asia Pacific (Hong Kong)   | ap-east-1      |
| Asia Pacific (Mumbai)      | ap-south-1     |
| Asia Pacific (Osaka-Local) | ap-northeast-3 |
| Asia Pacific (Seoul)       | ap-northeast-2 |
| Asia Pacific (Singapore)   | ap-southeast-1 |
| Canada (Central)           | ca-central-1   |
| China (Beijing)            | cn-north-1     |
| China (Ningxia)            | cn-northwest-1 |
| EU (Frankfurt)             | eu-central-1   |
| EU (Ireland)               | eu-west-1      |
| EU (London)                | eu-west-2      |
| EU (Paris)                 | eu-west-3      |
| EU (Stockholm)             | eu-north-1     |
| Middle East (Bahrain)      | me-south-1     |
| South America (Sao Paulo)  | sa-east-1      |
| AWS GovCloud (US-East)     | us-gov-east-1  |
| AWS GovCloud (US-West)     | us-gov-west-1  |

## Identity and Access Management

With AWS Identity and Access Management (IAM), you can specify who or what can
access services and resources in AWS, centrally manage fine-grained
permissions, and analyse access to refine permissions across AWS. With IAM, you
define who can access what by specifying fine-grained permissions. IAM then
enforces those permissions for every request. **Access is denied by default and
is granted only when permissions specify an "Allow".**

Before [getting started with
IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started.html),
create an administrative user so that you don't use the root user for everyday
tasks. For your daily administrative tasks, [grant administrative
access](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
to an administrative user in AWS IAM Identity Center (successor to AWS Single
Sign-On). IAM Identity Center requires AWS Organizations. After you create an
organisation, you cannot join your account to another organisation until you
delete its current organisation.

AWS Organisations provides the following benefits:

* Enables single payer and centralised cost tracking
* Lets you create and invite other AWS accounts
* Allows you to apply policy-based controls
* Helps you simplify organisation-wide management of AWS services

Your [identity
source](https://docs.aws.amazon.com/singlesignon/latest/userguide/get-started-choose-identity-source.html)
in IAM Identity Center defines where your users and groups are managed. You can
choose one of the following as your identity source:

* Identity Center directory - When you enable IAM Identity Center for the first
  time, it is automatically configured with an Identity Center directory as
  your default identity source. This is where you create your users and groups,
  and assign their level of access to your AWS accounts and applications.
* Active Directory - Choose this option if you want to continue managing users
  in either your AWS Managed Microsoft AD directory using AWS Directory Service
  or your self-managed directory in Active Directory (AD).
* External identity provider - Choose this option if you want to manage users
  in an external identity provider (IdP) such as Okta or Azure Active Directory.

AWS IAM Identity Center expands the capabilities of AWS Identity and Access
Management (IAM) to provide a central place that brings together administration
of users and their access to AWS accounts and cloud applications. Although the
service name AWS Single Sign-On has been retired, the term single sign-on is
still used throughout this guide to describe the authentication scheme that
allows users to sign in one time to access multiple applications and websites.

With IAM Identity Center you can manage sign-in security for your workforce by
creating or connecting your users and groups to AWS in one place. With
multi-account permissions you can assign your workforce identities access to
AWS accounts. You can use application assignments to assign your users access
to software as a service (SaaS) applications. With a single click, IAM Identity
Center enabled application admins can assign access to your workforce users,
and can also use application assignments to assign your users access to
software as a service (SaaS) applications.

* [Identity and Access Management (IAM)](https://console.aws.amazon.com/iam/home#/users)
* [Creating an IAM User in Your AWS
Account](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html)

## AWS CLI

AWS CLI is a universal command line environment for AWS and can be
[installed](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
by simply downloading a zip file.

```console
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

aws --version
```
```
aws-cli/2.15.17 Python/3.11.6 Linux/5.10.0-22-amd64 exe/x86_64.debian.11 prompt/off
```

Or installed [using Conda](https://anaconda.org/conda-forge/awscli).

```bash
# install Mamba first
conda install -c conda-forge mamba
mamba install -c conda-forge awscli
```

### Set up

See [Set up the AWS
CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html)
for the quick start guide.

The AWS CLI stores your configuration and credential information in a profile
(a collection of settings) in the `credentials` and `config` files. The files
are stored in your home directory under the `.aws` folder.

For general use, the `aws configure` or `aws configure sso` commands are the
fastest way to set up your AWS CLI installation.

Alternatively you can [manually edit the credential and config
files](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html#getting-started-quickstart-new-command).

If you are a new user of IAM Identity Center, the basic workflow to get started
using the service is:

1. Sign in to the web console and navigate to the IAM Identity Center console.

2. Select the directory you use for storing the identities of your users and
   groups from the IAM Identity Center console. IAM Identity Center provides
   you a directory by default that you can use to configure user access. If you
   prefer to use another identity source, you can connect your active directory
   or an external identity provider.

3. For organisation instances, assign user access to AWS accounts by selecting
   the accounts in your organisation, and then selecting users or groups from
   your directory and the permissions you want to grant them.

4. Give users access to applications by:

    a. Set up customer managed SAML 2.0 applications by either electing one of
    the pre-integrated applications from the application catalog or adding your
    own SAML 2.0 application.

    b. Configure the application properties.

    c. Assign the users access to the application. We recommend that you assign
    user access through group membership rather than by adding individual user
    permissions. With groups you can grant or deny permissions to groups of
    users, instead of applying those permissions to each individual. If a user
    moves to a different organization, you simply move that user to a different
    group. The user then automatically receives the permissions that are needed
    for the new organization.

5. If you are using the default IAM Identity Center directory, tell your users
   how to sign in to the AWS access portal. New users in IAM Identity Center
   must activate their user credentials before they can be used to sign in to
   the AWS access portal. For more information, see Sign in to the AWS access
   portal in the AWS Sign-In User Guide

Read [Configuring the AWS
CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html).

First setup [IAM](https://aws.amazon.com/iam/) by reading [Getting set up with
IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-set-up.html) and
[Getting started with
IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started.html).

* Create a new user in the IAM console.
* Click on the newly created user and under Security credentials, click Create
assess key and select CLI for the use case.
* Copy the access key and the secret access key; keep them private.

### Configuration

Run `aws configure` and enter the information.

```console
aws configure
```
```
AWS Access Key ID [None]:
AWS Secret Access Key [None]:
Default region name [None]:
Default output format [None]:
```

This information is stored in plain text in `~/.aws/credentials` and
`.aws/config`.

Below are the corresponding environment variables.

```bash
export AWS_ACCESS_KEY_ID="<id>"
export AWS_SECRET_ACCESS_KEY="<key>"
export AWS_DEFAULT_REGION="<region>"
```

Test with
[GetCallerIdentity](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetCallerIdentity.html).

```console
aws sts get-caller-identity
```

## EC2

* Read [Start EC2 instance using AWS
CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-ec2-instances.html).
* First set your [IAM
permissions](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-policies-for-amazon-ec2.html)
to allow for Amazon EC2 access.
* [Create key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-key-pairs.html)
* [Set up security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-security-groups.html)

### Find instance type

[Find an Amazon EC2 instance
type](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-discovery.html)
using AWS CLI and
[describe-instance-types](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instance-types.html).

```console
aws ec2 describe-instance-types \
  --output text \
  --filters "Name=current-generation,Values=true" "Name=memory-info.size-in-mib,Values=65536" --query "InstanceTypes[*].[InstanceType]" | sort | head
```
```
c5a.8xlarge
c6a.8xlarge
c6g.8xlarge
c6gd.8xlarge
c6gn.8xlarge
c6i.8xlarge
c6id.8xlarge
c6in.8xlarge
c7g.8xlarge
c7gd.8xlarge
```

Display details for the specified instance type.

```console
aws ec2 describe-instance-types --instance-types c5a.8xlarge
```

### Find image

`aws` can be used to find an appropriate image ID; see [Find an AMI using the
AWS
CLI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/finding-an-ami.html#finding-an-ami-aws-cli).
This requires your AWS access credentials.

The `describe-images` subcommand supports filtering parameters and should be
used or else a long list of image IDs will be returned. Use the `--owners`
parameter to display public AMIs owned by Amazon. Use `aws ec2 describe-images
help` to look up filter names; values support wildcards.

```console
aws ec2 describe-images \
   --owners self amazon \
   --filters "Name=architecture,Values=x86_64" "Name=name,Values=*ubuntu*22.04*server*" > ec2_images.json
```

Once a suitable `--image-id` has been found, use the `run-instances` subcommand
to start an EC2 instance.

```bash
aws ec2 run-instances \
  --image-id ami-0f0bcf5cc461416dc \
  --count 1 \
  --instance-type t2.micro \
  --key-name my_key \
  --security-group-ids my_group
```

Use the `describe-instances` subcommand to find your running instance.

```bash
aws ec2 describe-instances | less
```

Supply filters and a query to find the `InstanceId`, which is required for
terminating the instance.

```bash
aws ec2 describe-instances \
  --filters "Name=instance-type,Values=t2.micro" \
  --query "Reservations[].Instances[].InstanceId"
```

Use the `terminate-instances` subcommand to terminate the running instance.

```bash
aws ec2 terminate-instances --instance-ids i-05fa8e69bfa6faf98
```

### Run commands at launch

[Run commands on your Linux instance at
launch](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/user-data.html).

When you launch an instance in Amazon EC2, you have the option of passing user
data to the instance that can be used to perform common automated configuration
tasks and even run scripts after the instance starts. You can pass two types of
user data to Amazon EC2: shell scripts and cloud-init directives. You can also
pass this data into the launch instance wizard as plain text, as a file (this
is useful for launching instances using the command line tools), or as
base64-encoded text (for API calls).

To specify user data when you launch your instance, use the run-instances
command with the --user-data parameter. With run-instances, the AWS CLI
performs base64 encoding of the user data for you.

The following example shows how to specify a script using a text file. Be sure
to use the `file://` prefix to specify the file.

```bash
aws ec2 run-instances \
  --image-id ami-0e2bf1ada70fd3f33 \
  --count 1 \
  --instance-type t2.micro \
  --key-name my_key \
  --security-group-ids my_group \
  --user-data file://my_script.txt
```

### EC2 Image Builder

An Image pipeline is an automation configuration for building Amazon Machine
Images (AMIs) or Docker images on AWS. The configuration steps include:

1. Create recipe - Base image and build/test components - A recipe is a
   document that defines components to be applied to the base image to produce
   the desired configuration for the output image.
2. Define infrastructure configuration (optional) - Instance, VPC, IAM role,
   and other settings - Specify the infrastructure configurations for the
   instances that will run in your AWS account.
3. Define distribution settings (optional) - Regions for output AMI and Docker
   image distribution - Enter the AWS Regions for output AMI and Docker image
   distribution, and the AWS accounts that can launch the AMIs.

## Use same IP address

* Note that this is not free: https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html#using-instance-addressing-eips-allocating

## Mount EBS

Use the `lsblk` command to view your available disk devices and their mount
points (if applicable) to help you determine the correct device name to use.

```bash
lsblk
NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0     7:0    0 33.3M  1 loop /snap/amazon-ssm-agent/3552
loop1     7:1    0 55.5M  1 loop /snap/core18/1997
loop2     7:2    0 70.4M  1 loop /snap/lxd/19647
loop3     7:3    0 32.3M  1 loop /snap/snapd/11588
loop4     7:4    0 55.5M  1 loop /snap/core18/2074
loop5     7:5    0 32.3M  1 loop /snap/snapd/12398
loop6     7:6    0 70.3M  1 loop /snap/lxd/21029
xvda    202:0    0    8G  0 disk
└─xvda1 202:1    0    8G  0 part /
xvdb    202:16   0  100G  0 disk
```

Use the `file -s` command to get information about a specific device, such as
its file system type. If the output shows simply data, as in the following
example output, there is no file system on the device

```bash
sudo file -s /dev/xvdb
/dev/xvdb: data
```

Use the `mkfs -t` command to create a file system on the volume.

```bash
sudo mkfs -t xfs /dev/xvdb
```

```bash
sudo mkdir /data
sudo mount /dev/xvdb /data
```

See
<https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-using-volumes.html>
for more information.

## S3 commands

[aws s3](https://docs.aws.amazon.com/cli/latest/reference/s3/index.html)
commands include:

* `cp`
* `ls`
* `mb`
* `mv`
* `presign`
* `rb`
* `rm`
* `sync`
* `website`
