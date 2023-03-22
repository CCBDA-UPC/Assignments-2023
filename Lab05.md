# Lab session 5: Deploy a custom web app using additional cloud services

This hands-on session is based on
the [AWS Elastic Beanstalk tutorial](https://docs.aws.amazon.com/gettingstarted/latest/deploy/overview.html). Here, we
are going to build a small web application using [Django](https://www.djangoproject.com/) which is a modern framework
that uses Python to create web applications.

## AWS IaaS: Elastic Beanstalk

Elastic Beanstalk is a high-level deployment tool that helps you get an app from your desktop to the web in a matter of
minutes. Elastic Beanstalk handles the details of your hosting environment—capacity provisioning, load balancing,
scaling, and application health monitoring—so you don't have to.

Elastic Beanstalk supports apps developed in Python as well as many other programming languages. It also admits multiple
configurations for each platform. A platform configuration defines the infrastructure and software stack to be used for
a given environment.

Elastic Beanstalk provisions a set of AWS resources for the application that you deploy: Amazon EC2 instances, alarms, a
load balancer, security groups, and more.

The software stack that runs your application depends on the platform configuration type. For example, Elastic Beanstalk
supports several configurations for Python: Python 3.x, Python 2.x.

You can interact with Elastic Beanstalk by using the **AWS Management Console**, the **AWS Command Line Interface (AWS
CLI)**, or the **Elastic Beanstalk CLI**: a high-level CLI designed explicitly for Elastic Beanstalk.

For this tutorial, we'll use the `AWS Management Console` in conjunction with `Elastic Beanstalk CLI`.

## Deploying an example Web App Using Elastic Beanstalk

We are going to assume that you are working on a new subject on Cloud Computing that isn't ready for students to enroll
yet, but in the meantime, you plan to deploy a small placeholder app that collects contact information from the website
visitors who sign up to hear more. The signup app will help you reach potential students who might take part in a
private beta test of the laboratory sessions.

### The Signup App

The app will allow your future students to submit contact information and express interest in a preview of the new
subject on Cloud Computing that you're developing.

To make the app look good, we use [Bootstrap](https://getbootstrap.com/), a mobile-first front-end framework that
started as a Twitter project.

### AWS DynamoDB

We are going to use **Amazon DynamoDB**, a NoSQL database service, to store the contact information that users submit.
DynamoDB is a schema-less database, so you need to specify only a primary key attribute. Let us use the email field as a
key for each register.

### AWS Simple Notification Service (SNS)

We want to know when customers submit a form, therefore we are going to use **AWS Simple Notification Service** (AWS SNS), a message pushing service that can deliver notifications over various protocols. For our web app, we are going to push notifications to an email address.

### AWS CloudFront CDN

A content delivery network or content distribution network (CDN) is a geographically distributed network of proxy servers that disseminate a service spatially, as close to end-users as possible, to provide high availability, low latency, and high performance.

The information that flows every day on the Internet can be classified as "static" and "dynamic" content. The "dynamic" part is the one that changes depending on the user's input. It is distributed by, for instance, PaaS servers with load balancers. The "static" part does not change based on the user's input and it can be moved as close to the end user as possible to improve the "user experience".

Nowadays, CDNs serve a substantial portion of the "static" content of the Internet: text, graphics, scripts, downloadable media files (documents, software products, videos, etc.), live streaming media, on-demand streaming media, social networks and so much more.

Content owners pay CDN operators to deliver the content that they produce to their end users. In turn, a CDN pays ISPs (Internet Service Providers), carriers, and network operators for hosting its servers in their data centers.

**AWS CloudFront CDN** is a global CDN service that securely delivers static content with low latency and high transfer speeds. CloudFront CDN works seamlessly with other AWS services including **AWS Shield** for DDoS mitigation, **AWS S3**, **Elastic Load Balancing** or **AWS EC2** as origins for your applications, and **AWS Lambda** to run custom code close to final viewers.


# Tasks for Lab session #4

## Prerequisites

You need to have `AWS CLI` and `AWS EB CLI` installed and configured. Please
complete [Getting Started in the Cloud (with AWS)](https://github.com/CCBDA-UPC/Cloud-Computing-QuickStart/blob/master/Quick-Start-AWS.md)
before beginning work on this assignment.

* [Task 5.1: Download the code for the Web App](#Task51)
* [Task 5.2: Create a DynamoDB Table](#Task52)
* [Task 5.3: Test the web app locally](#Task53)
* [Task 5.4: Create the AWS Beanstalk environment and deploy a sample web app](#Task54)
* [Task 5.5: Configure Elastic Beanstalk CLI and deploy the target web app](#Task55)

<a name="Task51"/>

## Task 5.1: Download the code for the Web App

You are going to make a few changes to the base Python code. Therefore, download the repository on your local disk drive
as a *[zip file](eb-signup.zip)*. Unzip the file and change the name of the folder to *eb-django-express-signup*.

Prepare a new **private** repository in GitHub named `eb-django-express-signup` to commit all the changes to your code.
Invite `angeltoribio-UPC-BCN` to your new remote private repository as a collaborator.

**Do not mix** the repository containing the course answers with the repository that holds the changes to your web app.

<a name="Task52"/>

## Task 5.2: Create a DynamoDB Table

Our signup app uses a DynamoDB table to store the contact information that users submit.

#### To create a DynamoDB table

Go to the course "AWS Academy Learner Lab", open the modules and open the "Learner Lab". Click the button "Start Lab",
wait until the environment is up and then click "AWS" at the top of the window and open the AWS Console.

1. At the console search for "DynamoDB".

3. Go to Tables and **Create table**.

4. For Table name, type **gsg-signup-table**.

5. For the `Partition key`, type `email`. Choose **Create**.

<a name="Task53"/>

## Task 5.3: Test the web app locally

Once you are inside the directory of the project issue the following commands to setup the configuration of the project
using the process environment variables:

```
_$ export DEBUG=True
_$ export STARTUP_SIGNUP_TABLE=gsg-signup-table
_$ export AWS_REGION=us-east-1
_$ export AWS_ACCESS_KEY_ID=<YOUR-ACCESS-KEY-ID>
_$ export AWS_SECRET_ACCESS_KEY=<YOUR-SECRET-ACCESS-KEY>
_$ export AWS_SESSION_TOKEN=<YOUR-AWS-SESSION-TOKEN>
```

To obtain the values, at your CLI type the following command that will provide the necessary values

````bash
ddd_v1_w_3cWf_628331@runweb75472:~$ cat $HOME/.aws/credentials
[default]
aws_access_key_id = <YOUR-ACCESS-KEY-ID>
aws_secret_access_key = <YOUR-SECRET-ACCESS-KEY>
aws_session_token = <YOUR-AWS-SESSION-TOKEN>
````

**DO NOT EVER PUSH AWS CREDENTIALS TO YOUR PRIVATE REPOSITORY !!!**

Next, create a **new Python 3.8 virtual environment** specially for this web app and install the packages required to
run it. (**MS-Windows OS** users read the note at the end of this section)

Check the contents of the file **requirements.txt** that the web application declares as the set of Python packages, and
its version, that it requires to be executed successfully.

The package `boto3` is a library that hides de AWS REST API to the programmer and manages the communication between the
web app and all the AWS services. Check [**Boto 3 Documentation
**](https://boto3.readthedocs.io/en/latest/reference/services/index.html) for more details.

Please, note the different prompt **(eb-virt)_$** vs. **_$** when you are inside and outside of the new Python virtual
environment.

```
_$ virtualenv -p python3 ../eb-virt
_$ source ../eb-virt/bin/activate
(eb-virt)_$ pip install -r requirements.txt
```

You will now need to run a local testing server.

```
(eb-virt)~$ python manage.py runserver
System check identified no issues (0 silenced).
January 04, 2023 - 18:57:49
Django version 4.1.7, using settings 'eb-django-express-signup.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
(eb-virt)_$ deactivate
```

Check that you have configured the access to DynamoDB correctly by interacting with the web app through your
browser [http://127.0.0.1:8000/](http://127.0.0.1:8000/).

Go to the DynamoDB table browser tab and verify that the **gsg-signup-table** table contains the new records that the
web app
should have created. If all the above works correctly, you are almost ready to transfer the web app to AWS Beanstalk.

**NOTE I**: Make sure that you understand that we are using two types of environments: the *process environment*,
holding the variables used to configure the web application and the *Python environment* to keep only the packages that
the web app is using.

Every time that you open a new process, the process environment variables need to be re-instantiated either manually or
by some automatism such as adding them to your .bashrc setup file, PyCharm environment setup, Elastic Beanstalk
environment setup, or another suitable method.

We are creating a new Python virtual environment locally only to keep the packages that the web app uses. Having a small
Python environment implies a faster web app startup and avoids, as much as possible, any hidden dependencies and
ambiguities.

That Python virtual environment is re-created remotely by Elastic Beanstalk through the use of the file
*requirements.txt* and other configuration that you are going to set up later.

**NOTE II**: PyCharm provides a way to store and provide the environment variables for each execution. Besides, using
PyCharm **you will be able to debug your code easily**. See that you can include the environment variables at the
configuration of running Django.

<p align="center"><img src="./images/Lab04-pycharm-config.png" alt="AWS service" title="AWS service" width="550"/></p>

<a name="Task55"/>

## Task 5.5: Create the AWS Beanstalk environment and deploy the sample web app

### Prepare some configuration for AWS Beanstalk

At the repository, you already have a `requirements.txt` file that lets AWS Beanstalk know which Python modules your web
app needs. As you advance in this hands-on, you are going to install more Python modules, and you need to
update `requirements.txt`. Please note that you first need to switch to the virtual environment to update the file.

```
_$ source ../eb-virt/bin/activate
(eb-virt)_$ pip freeze > requirements.txt
(eb-virt)_$ deactivate
```

Beanstalk environment uses the following command to install the exact version of packages that our web app needs.

```
(eb-virt)_$ pip install -r requirements.txt
```

Another important set of files are the ones at the `.ebextensions` directory. The files in that directory include
instructions for AWS Beanstalk to start the web app and set up the timezone, amongst other required setups.

### Launch your new Elastic Beanstalk environment

Go to the course "AWS Academy Learner Lab", open the modules and open the "Learner Lab". Click the button "Start Lab",
wait until the environment is up and then click "AWS" at the top of the window and open the AWS Console.

1. At the console search for "Elastic Beanstalk" and click on **Create a new environment** and then select *Web server environment*.

2. Enter an *Application name*.

3. For *Platform*, keep *Managed platform* selected, choose *Python* and  **Python 3.8**.

4. For *Application code*, select **Upload your code** and select **Local file** choosing the zip file that you've
   previously downloaded. Then click **Configure more options**.

5. In the next screen, find the box named **Software** click *Edit* and find the *Environment properties* where you'll
   need to add your environment variables.

- DEBUG=True
- STARTUP_SIGNUP_TABLE=gsg-signup-table
- AWS_REGION=eu-west-1
- AWS_ACCESS_KEY_ID=<YOUR-ACCESS-KEY-ID>
- AWS_SECRET_ACCESS_KEY=<YOUR-SECRET-ACCESS-KEY>
- AWS_SESSION_TOKEN=<YOUR-AWS-SESSION-TOKEN>
- click **Save**.

6. Back in the boxes screen find the one named **Security** click *Edit* and

- select *LabRole* as the **Service role**
- *LabInstanceProfile* as the **IAM instance profile**
- click **Save**.
- if it asks for **Presets** select *Single Instance*

7. Back in the boxes screen find the one named **Notifications** and type your e-mail address to receive notifications
   regarding the environment that you are launching. Click **Save**.

8. Review all the settings and make sure all of them are set up correcty or you'll get a creation error. Click **Create app**.

In just a few minutes, Elastic Beanstalk provisions the networking, storage, compute and monitoring infrastructure
required to run a scalable web application in AWS.

Once you see the following status, you can click on the URL field under the environment name.

<p align="center"><img src="./images/Lab04-8.png " alt="OK" title="OK"/></p>

A new tab will open showing the application working in the cloud.

Once the site is up and running, at any time, you can deploy a new version of your application code to the cloud
environment.

Good job! We are almost there. You can now "Terminate environment" at the "Actions" dropdown menu.

<a name="Task56"/>

## Task 5.6: Configure Elastic Beanstalk CLI and deploy the target web app

At this point, we have the sample web app deployed. AWS EB CLI can, alternatively, help us to transfer and install our
web app to the cloud.

If you have configured "aws" command line interface you probably have a `$HOME/.aws/config` file that contains the
credentials of your root account. Please create a new AWS user that has only the necessary set of permissions to run
ElasticBeanstalk CLI and set the eb CLI up accordingly.

You can find more information on  [**eb
** command line interface](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-getting-started.html).

On macOS you can use

``` 
_$ brew install awsebcli
```

Go the the AWS Lab terminal and type
``` bash
_$ cat $HOME/.aws/credentials
```

Go to your terminal window and write:

```
_$ eb init -i
Select a default region
...
1) us-east-1 : US East (N. Virginia)
...
(default is 3): 1

Select an application to use
...
2) [ Create new Application ]
(default is 2): 2

Enter Application Name
(default is "eb-django-express-signup"):
Application eb-django-express-signup has been created.

Select a platform.
...
9) Python
...
(make a selection): 9

Select a platform branch.
1) Python 3.8 running on 64bit Amazon Linux 2
...
(default is 1): 1
Do you wish to continue with CodeCommit? (y/N): n
Do you want to set up SSH for your instances?
(Y/n): n
```

That has initialized the container and now you will be creating an environment for the application:

Running eb init creates a configuration file at `.elasticbeanstalk/config.yml`. You can edit it if necessary.

```
branch-defaults:
  master:
    environment: null
    group_suffix: null
global:
  application_name: eb-django-express-signup
  branch: null
  default_ec2_keyname: null
  default_platform: Python 3.8 running on 64bit Amazon Linux 2
  default_region: us-east-1
  include_git_submodules: true
  instance_profile: null
  platform_name: null
  platform_version: null
  profile: eb-cli
  repository: null
  sc: git
  workspace_type: Application
```

To create the resources required to run the application and upload the application code we need to type the following
command, using the parameter `--service-role` to grant the role's permissions to the EC2s running the code.

Be careful to use the environment variables enclosed in quotes as shown below

```
_$ eb create -ip LabInstanceProfile --service-role LabRole  --elb-type application --envvars "DEBUG=True,STARTUP_SIGNUP_TABLE=gsg-signup-table,AWS_REGION=eu-west-1,AWS_ACCESS_KEY_ID=<YOURS>,AWS_SECRET_ACCESS_KEY=<YOURS>"

Enter Environment Name
(default is eb-django-express-signup-dev): ebsignupEnv
Enter DNS CNAME prefix
(default is ebsignupEnv): 

Would you like to enable Spot Fleet requests for this environment? (y/N): n
Creating application version archive "app-2f25-230320_194513023230".
Uploading eb-django-express-signup/app-2f25-230320_194513023230.zip to S3. This may take a while.
Upload Complete.
Environment details for: ebsignupEnv
  Application name: eb-django-express-signup
  Region: us-east-1
  Deployed Version: app-2f25-230320_194513023230
  Environment ID: e-t9nvm9citv
  Platform: arn:aws:elasticbeanstalk:us-east-1::platform/Python 3.8 running on 64bit Amazon Linux 2/3.5.0
  Tier: WebServer-Standard-1.0
  CNAME: ebsignupEnv.us-east-1.elasticbeanstalk.com
  Updated: 2023-03-20 18:45:19.025000+00:00
Printing Status:
2023-03-20 18:45:17    INFO    createEnvironment is starting.
2023-03-20 18:45:19    INFO    Using elasticbeanstalk-us-east-1-339019491866 as Amazon S3 storage bucket for environment data.
2023-03-20 18:45:41    INFO    Created security group named: sg-0e9197860be76bdf2
2023-03-20 18:45:56    INFO    Created load balancer named: awseb-e-t-AWSEBLoa-1QZB3U1W7SEFN
2023-03-20 18:45:56    INFO    Created security group named: awseb-e-t9nvm9citv-stack-AWSEBSecurityGroup-Z122HW8D2JO9
2023-03-20 18:45:56    INFO    Created Auto Scaling launch configuration named: awseb-e-t9nvm9citv-stack-AWSEBAutoScalingLaunchConfiguration-CHo7xVD2AwyD
2023-03-20 18:47:00    INFO    Created Auto Scaling group named: awseb-e-t9nvm9citv-stack-AWSEBAutoScalingGroup-60AFMBNTMR2I
2023-03-20 18:47:00    INFO    Waiting for EC2 instances to launch. This may take a few minutes.
2023-03-20 18:47:15    INFO    Created Auto Scaling group policy named: arn:aws:autoscaling:us-east-1:339019491866:scalingPolicy:c8feb6af-0e11-4dda-882c-dfe19a9247f2:autoScalingGroupName/awseb-e-t9nvm9citv-stack-AWSEBAutoScalingGroup-60AFMBNTMR2I:policyName/awseb-e-t9nvm9citv-stack-AWSEBAutoScalingScaleUpPolicy-Mxg8ZpnnO1dj
2023-03-20 18:47:16    INFO    Created Auto Scaling group policy named: arn:aws:autoscaling:us-east-1:339019491866:scalingPolicy:c6edfcd6-63b7-4a94-8239-ef9358844e52:autoScalingGroupName/awseb-e-t9nvm9citv-stack-AWSEBAutoScalingGroup-60AFMBNTMR2I:policyName/awseb-e-t9nvm9citv-stack-AWSEBAutoScalingScaleDownPolicy-5xdkNI0FZgVv
2023-03-20 18:47:16    INFO    Created CloudWatch alarm named: awseb-e-t9nvm9citv-stack-AWSEBCloudwatchAlarmLow-1V4KANZFKXE5O
2023-03-20 18:47:16    INFO    Created CloudWatch alarm named: awseb-e-t9nvm9citv-stack-AWSEBCloudwatchAlarmHigh-1I7A41E16UKAJ
2023-03-20 18:47:28    INFO    Instance deployment successfully generated a 'Procfile'.
2023-03-20 18:47:31    INFO    Instance deployment completed successfully.
2023-03-20 18:48:34    INFO    Successfully launched environment: ebsignupEnv



```

Please, wait until you see the last message stating that the environment is successfully launched and
use `http://eb-django-ccbda.eu-west-1.elasticbeanstalk.com/` to access the project.

<p align="center"><img src="./images/Lab04-14.png " alt="Sample web app" title="Sample web app"/></p>

Please, note that issuing the above command the application code has been uploaded. See the line stating

```
Creating application version archive "app-190310_224408".
```

Do a little research on the CLI params and create the environment with a single instance, with no load-balancer.

### Test the Web App

To open a new browser with your application, type:

```
_$ eb open
```

You can check your ElasticBeanstalk that will console will show changes, as seen below.

<p align="center"><img src="./images/Lab04-11.png " alt="Sample web app" title="Sample web app"/></p>

Interact with the web app and check that the new records inserted are stored in DynamoDB.

### Troubleshoot Deployment Issues

If you followed all the steps, opened the URL, and obtained no app, there is a deployment problem. To troubleshoot a
deployment issue, you may need to use the logs that are provided by Elastic Beanstalk.

You can check the logs of the Elastic BeanStalk environment using `eb logs --all`. Once they have been retrieved you'll
be able to find them at the `.elasticbeanstalk` folder.

You can check that the values are correct using `eb printenv`.

```
_$ eb printenv
Environment Variables:
     AWS_ACCESS_KEY_ID = *****
     AWS_REGION = eu-west-1
     AWS_SECRET_ACCESS_KEY = ********<YOURS>*********
     DEBUG = true
     STARTUP_SIGNUP_TABLE = gsg-signup-table

```

Of course, you would try to catch such an error in development. However, if an error does get through to production, or
you want to update your app, Elastic Beanstalk makes it fast and easy to redeploy. Just modify your code, commit the
changes to your **LOCAL** repository, and issue "deploy" again.

```
_$ eb deploy
Creating application version archive "app-b2f2-180205_205630".
Uploading eb-django-express-signup/app-b2f2-180205_205630.zip to S3. This may take a while.
Upload Complete.
INFO: Environment update is starting.
INFO: Deploying new version to instance(s).
INFO: New application version was deployed to running EC2 instances.
INFO: Environment update completed successfully.
```

Since ElasticBeanstalk infrastructure maintenante is part of AWS responsibilities, that includes updating the operating
system, web server, application server, etc. Such updates may interfere with your application, therefore you can decide
when it is the best moment to use the following command that updates the environment to the most recent platform
version.

```
_$ eb upgrade
```

### One final step

Before ending this session, please go to your Elastic Beanstalk console, unfold the **Actions** button and **Save
Configuration**. It will be useful for you to continue with the same environment in the next Lab session.

<p align="center"><img src="./images/Lab04-12.png " alt="Save configuration" title="Save configuration"/></p>

Go to your EC2 console and check the EC2 instance that AWS uses for the Elastic Beanstalk environment. Terminate the
instance. Check what happens in your EB console. Wait a couple of minutes and check again your EC2 console.

**Q45a: What has happened? Why do you think that has happened?** Add your responses to `README.md`.

<p align="center"><img src="./images/Lab04-13.png " alt="EC instances" title="EC instances"/></p>

Now, to save expenses, you can terminate your environment, this time from the EB console.

**Q45b: What has happened? Why do you think that has happened?** Check both EC2 and EB consoles. Add your responses
to `README.md`.

**Q45c: Can you terminate the application using the command line? What is the command? if it exists.**

**Q45d: What parameters have you added to the `eb create` command to create your environment? Explain why you have
selected each parameter.**

**Q46: How long have you been working on this session? What have been the main difficulties you have faced and how have
you solved them?** Add your answers to `README.md`.

# How to submit this assignment:

Use the **private** repo named *https://github.com/CCBDA-UPC/2022-4-xx*. It needs to have, at least, two
files `README.md` with your responses to the above questions and `authors.json` with both members email addresses:

```json5
{
  "authors": [
    "FIRSTNAME1.LASTNAME1@estudiantat.upc.edu",
    "FIRSTNAME2.LASTNAME2@estudiantat.upc.edu"
  ]
}
```

1. Create some screen captures of your:

- DyanmoDB table with the data of the new leads.
- Make sure you have written your responses to the above questions in `README.md`.

2. Add any comment that you consider necessary at the end of the 'README.md' file

Make sure that you have updated your local GitHub repository (using the `git`commands `add`, `commit` and `push`) with
all the files generated during this session.

**Before the deadline**, all team members shall push their responses to their private
*https://github.com/CCBDA-UPC/2022-4-xx* repository.



#  Pre-lab homework

In the previous session, you configured an AWS role `gsg-signup-role` to be used by the EB to run the web app.

When you run the web app locally, if you are using the master account credentials that have been granted access to all resources you might find access problems when deploying the web app later on EB.

To locally run the web app using the `gsg-signup-role` you need to create a new IAM account that will be used for your programs. You can attach several roles to the account either using the console or programmatically as seen below.

```
import boto3

"""
Usage :
    session = role_arn_to_session(
        RoleArn='arn:aws:iam::YOUR-ACCOUNT-ID:role/example-role',
        RoleSessionName='ExampleSessionName')
    client = session.client('sqs')
"""
def role_arn_to_session(**args):
    client = boto3.client('sts')
    response = client.assume_role(**args)
    return boto3.Session(
        aws_access_key_id=response['Credentials']['AccessKeyId'],
        aws_secret_access_key=response['Credentials']['SecretAccessKey'],
        aws_session_token=response['Credentials']['SessionToken'])
```
For this laboratory session we are going to create a new IAM user, with only programmatic access, and we are going to attach the `gsg-signup-policy` using the console. You can explore using the above code for your projects.

1. Open the IAM console [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/), click on the **"Users"** left menu option and then **"Add user"**. We are going to use "lab_sessions" as "User name", select only Programmatic access and click **"Next:Permissions"**

<p align="center"><img src="./images/Lab05-13.png " alt="Confirmed" title="Confirmed"/></p>

2. Select "Attach existing policies directly", search "gsg-.." and select "gsg-signup-policy". Click **"Next: Review"**

<p align="center"><img src="./images/Lab05-14.png " alt="Confirmed" title="Confirmed"/></p>

3. Review the data and click **"Create user"**

<p align="center"><img src="./images/Lab05-15.png " alt="Confirmed" title="Confirmed"/></p>

4. Copy and save the Access key ID and the secret from the newly created user.

Run `aws configure` and use the new Access Key ID and secret as you did for the [Quickstart](https://github.com/CCBDA-UPC/Cloud-Computing-QuickStart/blob/master/Quick-Start-AWS.md#install-and-configure-aws-cli-and-eb-cli).

When you try to deploy your web app to EB you are going to see that the user has not enough permission to deploy the code and manage Elastic beanstalk. Therefore you need to add four extra policies:

- AdministratorAccess-AWSElasticBeanstalk - AWS Managed policy
- AWSElasticBeanstalkManagedUpdatesCustomerRolePolicy - AWS Managed policy
- AWSCodeDeployRole - AWS Managed policy
- AWSCodeDeployFullAccess - AWS Managed policy

Now test your local programs with the new identity that has much-restricted permissions, as restricted as necessary to interact with the resources used (DynamoDB and SNS) and be able to deploy mand manage the EB to run the web app.

#  Tasks for Lab session #5

* [Task 5.1: Use AWS Simple Notification Service in your web app](#Tasks51)
* [Task 5.2: Create a new option to retrieve the list of leads](#Tasks52)
* [Task 5.3: Improve the web app transfer of information](#Tasks53)
* [Task 5.4: Deliver static content using a Content Delivery Network](#Tasks54)


<a name="Tasks51" />

## Task 5.1: Use AWS Simple Notification Service in your web app


### Create a AWS SNS Topic

Our signup web app wants to notify you each time a user signs up. When the data from the signup form is written to the DynamoDB table, the app will send you an AWS SNS notification.

First, you need to create an AWS SNS topic, which is a stream for notifications, and then you need to create a subscription that tells AWS SNS where and how to send the notifications.

**To set up AWS SNS notifications**

Open the AWS SNS console at [https://console.aws.amazon.com/sns/v2/home](https://console.aws.amazon.com/sns/v2/home).

- Choose **Create topic**.
- For Topic name, type *gsg-signup-notifications*. Choose **Standard** type and **Create topic**.
- Choose  **Create subscription**.
- For **Protocol**, choose *Email*. For **Endpoint**, enter *your email address*. Choose **Create Subscription**.

To confirm the subscription, AWS SNS sends you an email named *AWS Notification — Subscription Confirmation*. Open the link in the email to confirm your subscription.

Do not forget that before testing the new functionality you need to have the AWS SNS subscription approved.

<p align="center"><img src="./images/Lab05-2.png " alt="Confirmed" title="Confirmed"/></p>

Add the *unique identifier* for the AWS SNS topic to the configuration environment of your local deployment.

```bash
_$ export NEW_SIGNUP_TOPIC="arn:aws:sns:eu-west-1:YOUR-ACCOUNT-ID:gsg-signup-notifications"
```

Before you forget, you can also add a new variable to the environment of the Elastic Beanstalk deployment.

### Modify the web app to send messages

Open the files *form/models.py* and *form/views.py* read and understand what the code does.

Add the code below to *form/models.py* as a new operation of the model *Leads()*.

```python
def send_notification(self, email):
    sns = boto3.client('sns', region_name=AWS_REGION)
    try:
        sns.publish(
            TopicArn=NEW_SIGNUP_TOPIC,
            Message='New signup: %s' % email,
            Subject='New signup',
        )
        logger.error('SNS message sent.')

    except Exception as e:
        logger.error(
            'Error sending AWS SNS message: ' + (e.fmt if hasattr(e, 'fmt') else '') + ','.join(e.args))
```

You have probably noticed that there is a Python variable that needs to be instantiated. Scroll up that file and add *NEW_SIGNUP_TOPIC* next to the other two environment variables, as shown below:

```python
STARTUP_SIGNUP_TABLE = os.environ['STARTUP_SIGNUP_TABLE']
AWS_REGION = os.environ['AWS_REGION']
NEW_SIGNUP_TOPIC = os.environ['NEW_SIGNUP_TOPIC']
```

Go to *form/views.py* and modify the signup view: if the lead has been correctly inserted in our DynamoDB table we can send the notification.

```python
def signup(request):
    leads = Leads()
    status = leads.insert_lead(request.POST['name'], request.POST['email'], request.POST['previewAccess'])
    if status == 200:
        leads.send_notification(request.POST['email'])
    return HttpResponse('', status=status)
```

Close the file and execute the Django web app locally.

Now you can see that the new record appears inserted but when you try to add a new lead appears an error *User: ... is not authorized to perform: SNS:Publish on resource*:

```bash
(eb-virt)_$ python manage.py runserver
"GET / HTTP/1.1" 200 7456
New item added to database.
Error sending AWS SNS message: An error occurred (AuthorizationError) when calling the Publish operation:
  User: arn:aws:iam::YOUR-USER-NUMBER:root is not authorized to perform: SNS:Publish on resource: arn:aws:sns:eu-west-1:YOUR-ACCOUNT-ID:gsg-signup-notifications
"POST /signup HTTP/1.1" 200 0
```

To fix that error we need to grant access to the IAM profile that we created in the previous session. Go to [https://console.aws.amazon.com/iam](https://console.aws.amazon.com/iam) and check the JSON contents of the *gsg-signup-policy*: it only grants access to DynamoDB  to put an item:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "dynamodb:PutItem",
            "Resource": "*"
        }
    ]
}
```

We can change the Action property to a list where we grant the sns:Publish action to the list of allowed actions for *gsg-signup-policy*.

```json
"Action":["sns:Publish","dynamodb:PutItem"]
```
Instead of editing the JSON policy definition by hand you can use the visual editor and add check marks to the actions of each resource that you want your web app to use.

<p align="center"><img src="./images/Lab05-5.png " alt="IAM" title="IAM"/></p>

Once you have stored the changes in the IAM policy, you can post a new record. This time you see no error and you receive a notification in your e-mail.

```bash
Existing item updated to database.
SNS message sent.
"POST /signup HTTP/1.1" 409 0
```

Now that the web app is working in your computer, commit the changes. Deploy the new version to your Elastic beanstalk environment and test that it works correctly.

**Q51: Has everything gone alright?** Add your answers to the `README.md` file in the responses repository.

<a name="Tasks52" />

## Task 5.2: Create a new option to retrieve the list of leads

Edit the file *form/urls.py* to add the new URL and associate it to the new view *search*.

```python
urlpatterns = [
    # ex: /
    path('', views.home, name='home'),
    # ex: /signup
    path('signup', views.signup, name='signup'),
    # ex: /search
    path('search', views.search, name='search'),
]
```

To create the controller for the new view edit *form/views.py* and include the following code:

```python
from collections import Counter

def search(request):
    domain = request.GET.get('domain')
    preview = request.GET.get('preview')
    leads = Leads()
    items = leads.get_leads(domain, preview)
    if domain or preview:
        return render(request, 'search.html', {'items': items})
    else:
        domain_count = Counter()
        domain_count.update([item['email'].split('@')[1] for item in items])
        return render(request, 'search.html', {'domains': sorted(domain_count.items())})
```

The search view gets two parameters:
- preview: (*values are Yes/No*) lists the leads that are interested, or not, in a preview.
- domain: (*value is the part right after the @ of an e-mail address*) will list only the leads from that domain.

Reading the code, we understand that the search view retrieves the value of the parameters, gets the complete list of leads and then:

- if any parameter is set, the program just lists all the records matching the search.
- if both parameters are empty the program extracts the domain from each e-mail address and counts how many addresses belong to each domain.

To access the records stored at the NoSQL table *gsg-signup-table* you need to add a method *get_leads* to the model *Leads()* file *form/models.py*. The [Scan](https://docs.aws.amazon.com/amazondynamodb/latest/APIReference/API_Scan.html) operation allows us to filter values from the table.

```python
def get_leads(self, domain, preview):
    try:
        dynamodb = boto3.resource('dynamodb', region_name=AWS_REGION)
        table = dynamodb.Table('gsg-signup-table')
    except Exception as e:
        logger.error(
            'Error connecting to database table: ' + (e.fmt if hasattr(e, 'fmt') else '') + ','.join(e.args))
        return None
    expression_attribute_values = {}
    FilterExpression = []
    if preview:
        expression_attribute_values[':p'] = preview
        FilterExpression.append('preview = :p')
    if domain:
        expression_attribute_values[':d'] = '@' + domain
        FilterExpression.append('contains(email, :d)')
    if expression_attribute_values and FilterExpression:
        response = table.scan(
            FilterExpression=' and '.join(FilterExpression),
            ExpressionAttributeValues=expression_attribute_values,
        )
    else:
        response = table.scan(
            ReturnConsumedCapacity='TOTAL',
        )
    if response['ResponseMetadata']['HTTPStatusCode'] == 200:
        return response['Items']
    logger.error('Unknown error retrieving items from database.')
    return None
```

A final step is to move the file *extra-file/search.html* to *form/templates/search.html*. That file receives the data from the view controller and creates the HTML to show the results.

Save the changes and, before committing them, check that everything works fine by typing *http://127.0.0.1:8000/search* in your browser.

<p align="center"><img src="./images/Lab05-6.fw.png " alt="Search" title="Search"/></p>

To add the new option to the menu bar, simply edit the file *form/templates/generic.html*, go to line 28 and add the second navbar as shown below. Save the file and, with no further delay, check that you have it added in the version that runs in your computer.

```html
 <div class="collapse navbar-collapse" id="navbarResponsive">
    <ul class="navbar-nav">
        <li class="nav-item active"><a class="nav-link active" href="{% url 'form:home' %}">Home</a></li>
        <li class="nav-item"><a class="nav-link" href="#">About</a></li>
        <li class="nav-item"><a class="nav-link" href="#">Blog</a></li>
        <li class="nav-item"><a class="nav-link" href="#">Press</a></li>
    </ul>
    <ul class="nav navbar-nav ml-auto">
        <li class="nav-item"><a class="nav-link" href="{% url 'form:search' %}">Admin search</a></li>
    </ul>
</div>
```

<p align="center"><img src="./images/Lab05-7.png " alt="Search" title="Search"/></p>

If the web app works correctly in your computer commit the changes and deploy the new version in the cloud. Change whatever is necessary to make it work.

**Q52: Has everything gone alright? What have you changed?** Add your answers to the `README.md` file in the responses repository.

<a name="Tasks53" />

## Task 5.3: Improve the web app transfer of information (optional)

You can work on this section locally in order to save expenses; you can terminate your environment from the EB console.

If you analyze the new function added, probably a wise thing to do will be to optimize the data transfer from the DynamoDB table: imagine that instead of a few records in your NoSQL table you have millions of records. Transferring millions of records to your web app just to count how many e-mail addresses match a domain doesn't seem to be a great idea.

DynamoDB is a NoSQL database and does not allow aggregation SQL queries. You are encouraged to improve the above code to obtain a more efficient way of counting the e-mail addresses for each domain. Try to optimize the transfer of information as well as the web app processing. Maybe you need to change the way that the records are stored.

Test the changes locally, commit them to your GitHub repository.


**Q53: Describe the strategy used to fulfill the requirements of this section. What have you changed in the code and the configuration of the different resources used by the web app? What are the tradeoffs of your solution?** Add your responses to `README.md`.

<a name="Tasks54" />

## Task 5.4: Deliver static content using a Content Delivery Network


### The static content in our web app

If you check line 11 of the file *form/templates/generic.html* you will see that, instead of loading in our server Bootstrap 4 CSS, we are already using a CDN to retrieve the CSS and send it to the final users. Bootstrap uses *maxcdn.bootstrapcdn.com* as their CDN distribution point.

```html
    <!-- https://www.bootstrapcdn.com/ -->
    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet"
          integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
    <!-- Bootstrap Lumen theme -->
    <link href="https://maxcdn.bootstrapcdn.com/bootswatch/4.0.0-beta.3/lumen/bootstrap.min.css" rel="stylesheet"
          integrity="sha384-lBO0+E/aIJhpRIYjP6dJ1mNYgo3hhUBPcF74XRfOM27g7WmDuitolvnUENdDG4QI" crossorigin="anonymous">
    <!-- Bootstrap fonts -->
    <link href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet"
          integrity="sha384-wvfXpqpZZVQGK6TAh5PVlGOfQNHSoD2xbE+QkPxCAFlNEevoEH3Sl0sibVcOQVnN" crossorigin="anonymous">
```

We can now add our CSS code to customize the look and feel of our web app even more. In that same file, add the following line just before closing the **head** HTML tag:

```html
    <link href="{% static 'custom.css' %}" rel="stylesheet">
</head>
```

If you check the contents of the file *static/custom.css* you will see that it includes some images, also available in the same folder. If you save the modifications to *form/templates/generic.html* and review your web app, http://127.0.0.1:8000, you will see that it appears slightly different.

### Upload your static content to AWS S3 and grant object permissions

All the distributed static content overloads our server with requests. Moving it to a CDN will reduce our server's load and, at the same time, the users will experience a much lower latency while using our web app. We only have static files in this app, but a typical web app distributes hundreds of pieces of static content.

To configure our CDN, we are going to follow the steps at ["Getting Started with CloudFront CDN"](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/GettingStarted.html). Check that document if you need extra details.

 Review the QuickStart hands-on [Getting Started in the Cloud with AWS](../../../Cloud-Computing-QuickStart/blob/master/Quick-Start-AWS.md) and create a new bucket in 'eu-west-1' region to deposit the web app static content. Let us name this bucket **eb-django-express-signup-YOUR-ID** (YOUR-ID can be your AWS account number or any other distinctive string because you will not be allowed to create two buckets with the same name, regardless the owner).
 
 AWS has recently set some restrictions when creating an S3 bucket with public access. Make sure that you uncheck all the following options before uploading files. You can later check them back.
 
 <p align="center"><img src="./images/Lab06-S3-public-access.png" alt="S3 public access" title="S3 public access"/></p>
 
 <p align="center"><img src="./images/Lab05-8.png " alt="S3 bucket" title="S3 bucket"/></p>

 This time add the files manually and grant them public read permission.
 
  <p align="center"><img src="./images/Lab05-9.png " alt="S3 bucket" title="S3 bucket"/></p>

You can also use AWS CLI to sync the contents of your static folder with that bucket. [Synchronize with your S3 bucket](https://docs.aws.amazon.com/cli/latest/reference/s3/sync.html) using the following command:

```bash
_$ aws s3 sync --acl public-read ./static s3://eb-django-express-signup-YOUR-ID
upload: ./static/custom.css to s3://eb-django-express-signup-YOUR-ID/custom.css
upload: ./static/CCBDA-Square.png to s3://eb-django-express-signup-YOUR-ID/CCBDA-Square.png
upload: ./static/startup-bg.png to s3://eb-django-express-signup-YOUR-ID/startup-bg.png
```

If you explore in your S3 console you will see that there is a URL available to retrieve the files. Verify that you can access the contents of that URL, making the file public if it was not already.

```
https://s3-eu-west-1.amazonaws.com/eb-django-express-signup-YOUR-ID/CCBDA-Square.png
```
<p align="center"><img src="./images/Lab05-12.png " alt="S3 address" title="S3 address"/></p>



### Create a CloudFront CDN Web Distribution

Following the steps at ["Getting Started with CloudFront CDN"](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/GettingStarted.html) we end up having to wait until the files are distributed. It takes five minutes or more, be patient. Once the first distribution is set up, whenever you resync your static contents it will take much less.

 <p align="center"><img src="./images/Lab05-10.png " alt="CloudFront CDN distribution" title="CloudFront CDN distribution"/></p>

### Change the code and test your links

The HTML code of our web app has only one direct access to a static file; the images referenced (using a relative route) through the CSS stylesheet. We just need to change *form/templates/generic.html* and our web app is now retrieving all static content from our CDN distribution.

Consider that we are now borrowing a CloudFront URL (RANDOM-ID-FROM-CLOUDFRONT.cloudfront.net) but usually, in the setup, we will use a URL from our domain, something like *static.mydomain.com* to map the CDN distribution.

```html
    <link href="//RANDOM-ID-FROM-CLOUDFRONT.cloudfront.net/custom.css" rel="stylesheet">
```

**Q54: Take a couple of screenshots of you S3 and CloudFront consoles to demonstrate that everything worked all right.** Commit the changes on your web app, deploy them on Elastic beanstalk and check that it also works fine from there: **use Google Chrome and check the origin of the files that you are loading (attach a screen shot similar to the one below)**:

 <p align="center"><img src="./images/Lab05-11.png " alt="Files loaded" title="Files loaded"/></p>

**Q55: How long have you been working on this session (including the optional part)? What have been the main difficulties that you have faced and how have you solved them?** Add your answers to `README.md`.

 Add all these files to your repository and comment what you think is relevant in your session's *README.md*.

### Django support for CDN

Having to go through the code of a web app to locate all the static files is a not only tedious task but also prone to errors. Since Django Framework distinguishes the static content from the dynamic content, it supports the smooth integration of a CDN to distribute it. Try configuring this feature if you are curious and have time.

First of all, you need to add the following package to your environment:

```bash
(eb-virt)_$ pip install django-storages
```

Then modify `eb-django-express-signup\eb-django-express-signup\settings.py` by adding 'storages' as an installed application and tell Django to use the new storage schema as well as the name of your bucket and the name of the CloudFront domain.

```python
INSTALLED_APPS = [
...
    'storages',
...
]

...

CLOUD_FRONT = os.environ['CLOUD_FRONT'] == 'True'

if CLOUD_FRONT:
    DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    AWS_STORAGE_BUCKET_NAME = 'eb-django-express-signup-YOUR-ID'
    AWS_S3_CUSTOM_DOMAIN = 'RANDOM-ID-FROM-CLOUDFRONT.cloudfront.net'

```

Having done that you should be able to keep all static files declared the way Django expects to and, at the same time, access them using a CDN.

```html
    <link href="{% static 'custom.css' %}" rel="stylesheet">
</head>
```

This should be the last step on the deployment of the web app and you can activate it only if the variable DEBUG is set to False.

Django can also assume the synchronization of the static files to the CDN by means of the maintenace command `python manage.py collectstatic`.

# How to submit this assignment:

Use the **private** repo named *https://github.com/CCBDA-UPC/2022-5-xx*. It needs to have, at least, two files `README.md` with your responses to the above questions and `authors.json` with both members email addresses:

```json5
{
  "authors": [
    "FIRSTNAME1.LASTNAME1@estudiantat.upc.edu",
    "FIRSTNAME2.LASTNAME2@estudiantat.upc.edu"
  ]
}
```
Commit the `README.md` file to your **responses repository** and commit all changes to the **web app repository**. Do not mix the repository containing the course answers with the repository that holds the changes to your web app.

Make sure that you have updated your local GitHub repository (using the `git`commands `add`, `commit` and `push`) with all the files generated during this session. 

**Before the deadline**, all team members shall push their responses to their private *https://github.com/CCBDA-UPC/2022-5-xx* repository.
