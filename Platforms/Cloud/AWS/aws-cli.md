---
title: "AWS CLI"
draft: false
---
- [AWS Command Line Interface (AWS CLI)](#aws-command-line-interface-aws-cli)
  - [AWS CLI versions](#aws-cli-versions)
- [AWS CLI V2 - Installation and Configuration](#aws-cli-v2---installation-and-configuration)
  - [Container Based Envirnonment - Docker](#container-based-envirnonment---docker)
    - [Share host files, credentials, environment variables, and configuration](#share-host-files-credentials-environment-variables-and-configuration)
  - [Linux Envirnonment](#linux-envirnonment)
- [AWS CLI V2 Configuration](#aws-cli-v2-configuration)
    - [aws configure](#aws-configure)
- [AWS CLI V1 - Installation and Configuration](#aws-cli-v1---installation-and-configuration)
- [AWS SDK](#aws-sdk)
  - [AWS SDK for Python (Boto3)](#aws-sdk-for-python-boto3)
    - [Boto3 Configuration](#boto3-configuration)
      - [Boto3 example](#boto3-example)
    - [Using Boto3](#using-boto3)
- [Reference Links](#reference-links)

# AWS Command Line Interface (AWS CLI)

- It is an open source tool that enables you to interact with AWS services using commands in your command-line shell.
- It provides direct access to the public APIs of AWS services.
- Using service's capabilities with the AWS CLI shell scripts can be developed to manage your resources.
- In addition to the low-level, API-equivalent commands, several AWS services provide customizations for the AWS CLI. Customizations can include higher-level commands that simplify using a service with a complex API.

## AWS CLI versions

AWS CLI is available in two versions

- Version 2.x – The current, generally available release of the AWS CLI that is intended for use in production environments.
- Version 1.x – The previous version of the AWS CLI that is available for backwards compatibility.

# AWS CLI V2 - Installation and Configuration

AWS CLI v2 provides pre-built binaries for Windows, Linux, and macOS. You no longer need to have Python installed in order to use the AWS CLI. You don’t have to worry about compatible Python versions, virtual environments, or conflicting python packages. On Windows we provide an MSI installer and on macOS we provide a .pkg installer.

## Container Based Envirnonment - Docker

AWS CLI can be installed on Docker using the official AWS CLI version 2 Docker image. Official Docker images provide isolation, portability, and security that AWS directly supports and maintains and is hosted on DockerHub in the `amazon/aws-cli` repository

Prerequisites

- Docker must be installed

To run the AWS CLI version 2 Docker image, use the docker run command.

```
docker pull amazon/aws-cli:latest
docker run --rm -it amazon/aws-cli <command>
docker run --rm -it amazon/aws-cli:latest <command>
docker run --rm -it amazon/aws-cli:<major.minor.patch> <command>
```

Each time you run this command, Docker spins up a container of your downloaded amazon/aws-cli image, and executes your aws command. By default, the Docker image uses the latest version of the AWS CLI version 2.

Because the latest Docker image is downloaded to your computer only the first time you use the docker run command, you need to manually pull an updated image.

- --rm – Specifies to clean up the container after the command exits.
- -it – Specifies to open a pseudo-TTY with stdin. If you are running scripts, -it is not needed. If you are experiencing errors with your scripts, omit -it from your Docker call.
- latest - Docker image tagged as latest, It is used by the default
- `<major.minor.patch>` – Defines a specific version of the AWS CLI version 2 for the Docker image.

```
$ docker run --rm -it amazon/aws-cli --version
aws-cli/2.1.29 Python/3.7.3 Linux/4.9.184-linuxkit botocore/2.0.0dev10
```

### Share host files, credentials, environment variables, and configuration

Because the AWS CLI version 2 is run in a container, by default the CLI can't access the host file system, which includes configuration and credentials. To share the host file system, credentials, and configuration to the container, mount the host system’s ~/.aws directory to the container at /root/.aws with the -v flag to the docker run command. This allows the AWS CLI version 2 running in the container to locate host file information.

```
# Linux 
$ docker run --rm -it -v ~/.aws:/root/.aws amazon/aws-cli <command>

# Windows
$ docker run --rm -it -v %userprofile%\.aws:/root/.aws amazon/aws-cli <command>
```

```bash
# Passing  specific system's environment variable ENVVAR_NAME
$ docker run --rm -it -v ~/.aws:/root/.aws -e ENVVAR_NAME amazon/aws-cli s3 ls

# Downloading the S3 object s3://aws-cli-docker-demo/hello to your local file system by mounting the current working directory to the container's /aws directory.
$ docker run --rm -it -v ~/.aws:/root/.aws -v $(pwd):/aws amazon/aws-cli s3 cp s3://aws-cli-docker-demo/hello .
```

## Linux Envirnonment

The AWS CLI version 2 has no dependencies on other Python packages. It has a self-contained, embedded copy of Python included in the installer.

Prerequisites

- You must be able to extract or "unzip" the downloaded package.
- The AWS CLI version 2 uses glibc, groff, and less. These are included by default in most major distributions of Linux.

``` bash

# For a specific version of the AWS CLI
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-2.0.30.zip" -o "awscliv2.zip"
$ curl https://awscli.amazonaws.com/awscli-exe-linux-x86_64-2.0.30.zip.sig -o awscliv2.sig 

# For latest version of the AWS CLI
$ curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Download the AWS CLI signature file
$ curl https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip.sig -o awscliv2.sig 

# Verify the signature, passing both the downloaded
$ gpg --verify awscliv2.sig awscliv2.zip

$ unzip awscliv2.zip
$ sudo ./aws/install

# To install files and create a symbolic link in a different directory 
$ ./aws/install -i /usr/local/aws-cli -b /usr/local/bin

$ aws --version

# Update AWS CLI after downloading the latest version
$ sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update

# Uninstall the AWS CLI version 2 - Remove symbolic link and the installation directory
$ which aws && sudo rm $(which aws)/aws
$ which aws && sudo rm $(which aws)/aws_completer
$ sudo rm -rf /usr/local/aws-cli
```

By default the install program installs files to /usr/local/aws-cli, and a symbolic link is created in /usr/local/bin.

- --install-dir or -i – This option specifies the directory to copy all of the files to
- --bin-dir or -b – This option specifies that the main aws program in the install directory is symbolically linked to the file aws in the specified path.

# AWS CLI V2 Configuration

> Note: AWS requires that all incoming requests are cryptographically signed. The AWS CLI does this for you. The "signature" includes a date/time stamp. Therefore, you must ensure that your computer's date and time are set correctly. If you don't, and the date/time in the signature is too far off of the date/time recognized by the AWS service, AWS rejects the request.

### aws configure

the aws configure command is the fastest way to set up your AWS CLI installation and prompts for 4 pieces of information.

```bash
$ aws configure
AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-2
Default output format [None]: json
```

The AWS CLI stores this information in a profile (a collection of settings) named default in the credentials file. By default, the information in this profile is used when you run an AWS CLI command that doesn't explicitly specify a profile to use.

- Access keys consist of an access key ID and secret access key and are used to sign programmatic requests that you make to AWS.
- you can create them from the AWS Management Console and you must also have permissions to perform the required IAM actions.
- You must specify an AWS Region when using the AWS CLI, either explicitly or by setting a default Region.
- The Default output format specifies how the results are formatted. Supported formats are json, yaml, yaml-stream, text, table (formatted using characters +|- to form the cell borders)
- A collection of settings is called a profile. You can multiple additional named profiles with varying credentials and settings but will need to explicitly specify them with the command else default will be referred

  ```
  # To create a aws cli profile
  # aws configure --profile produser

  $ aws s3 ls --profile produser

  # sets the region in the profile named integ.
  $ aws configure set region us-west-2 --profile integ

  # Import CSV credentials generated from the AWS web console.
  $ aws configure import --csv file://credentials.csv

  # To list all configuration data
  aws configure list

  # To list all your profile names
  $ aws configure list-profiles
  ```

# AWS CLI V1 - Installation and Configuration

The AWS CLI version 1 is built using the Python SDK, and therefore requires you to install a compatible version of Python. major new features that are introduced in the AWS CLI version 2 might not be backported to the AWS CLI version 1. To use those features, you must install the AWS CLI version 2.

Python version support matrix

| AWS CLI version             | Supported Python version                   |
| :-------------------------- | :----------------------------------------- |
| Versions starting 7/19/2021 | Python 3.6+                                |
| 1.19.0 – current            | Python 2.7+, Python 3.6+                   |
| 1.17 – 1.18.x               | Python 2.7+, Python 3.4+                   |
| 1.0 – 1.16.x                | Python 2.6 and older, Python 3.3 and older |

For version history, see the [AWS CLI version 1 Changelog](https://github.com/aws/aws-cli/blob/develop/CHANGELOG.rst) on GitHub.

# AWS SDK

SDKs take the complexity out of coding by providing language-specific APIs for AWS services. Following programming languages are available for developing application on AWS

- JavaScript
- Python
- PHP
- .NET
- Ruby
- Java
- Go
- Node.js
- C++

## AWS SDK for Python (Boto3)

The SDK is composed of two key Python packages: Botocore (the library providing the low-level functionality shared between the Python SDK and the AWS CLI) and Boto3 (the package implementing the Python SDK itself).

- To use Boto3, you first need to install it and its dependencies.
- Before using Boto3, you need to set up authentication credentials for your AWS account using either the IAM Console or the AWS CLI.( using `aws configure`)

### Boto3 Configuration

Boto3 looks at various configuration locations until it finds configuration values. Boto3 adheres to the following lookup order when searching through sources for configuration values:

- A Config object that's created and passed as the config parameter when creating a client
- Environment variables
- The ~/.aws/config file

> **Note**: Configurations are not wholly atomic. This means configuration values set in your AWS config file can be singularly overwritten by setting a specific environment variable or through the use of a Config object.

#### Boto3 example

```python
import boto3
from botocore.config import Config

# Using proxies
proxy_definitions = {
    'http': 'http://proxy.amazon.com:6502',
    'https': 'https://proxy.amazon.org:2010'
}

my_config = Config(
    region_name = 'us-west-2',
    signature_version = 'v4',
    retries = {
        'max_attempts': 10,
        'mode': 'standard'
    }
    'proxies': proxy_definitions
    'proxies_config': {              # Configuring Proxies
        'proxy_client_cert': '/path/of/certificate'
    }
)

client = boto3.client('kinesis', config=my_config)

/*
# providing credentials to Boto3 while creating client Object
client = boto3.client(
    's3',
    aws_access_key_id=ACCESS_KEY,
    aws_secret_access_key=SECRET_KEY,
    aws_session_token=SESSION_TOKEN
)

# providing credentials to Boto3 while session object
session = boto3.Session(
    aws_access_key_id=ACCESS_KEY,
    aws_secret_access_key=SECRET_KEY,
    aws_session_token=SESSION_TOKEN
)

*/
```

- To set these configuration options, create a Config object with the options you want, and then pass them into your client.
- Boto3 credentials can be configured in multiple ways. There are two types of configuration data in Boto3: credentials and non-credentials. Credentials include items such as aws_access_key_id, aws_secret_access_key, and aws_session_token. Non-credential configuration includes items such as which region to use or which addressing style to use for Amazon S3.
- The order in which Boto3 searches for credentials is:
  1. Passing credentials as parameters in the boto.client() method
  2. Passing credentials as parameters when creating a Session object
  3. Environment variables
  4. Shared credential file (~/.aws/credentials)
  5. AWS config file (~/.aws/config)
  6. Assume Role provider
  7. Boto2 config file (/etc/boto.cfg and ~/.boto)
  8. Instance metadata service on an Amazon EC2 instance that has an IAM role configured.

### Using Boto3

To use Boto3, you must first import it and indicate which service or services you're going to use:

```python
import boto3

# Let's use Amazon S3
s3 = boto3.resource('s3')
```

Now that you have an s3 resource, you can make send requests to the service. The following code uses the buckets collection to print out all bucket names:

```python
# Print out bucket names
for bucket in s3.buckets.all():
    print(bucket.name)
```

You can also upload and download binary data. For example, the following uploads a new file to S3, assuming that the bucket my-bucket already exists:

```python
# Upload a new file
data = open('test.jpg', 'rb')
s3.Bucket('my-bucket').put_object(Key='test.jpg', Body=data)
```

# Reference Links

- <https://aws.amazon.com/tools/>
- [Boto3 Dcoumentation](https://boto3.amazonaws.com/v1/documentation/api/latest/index.html)
