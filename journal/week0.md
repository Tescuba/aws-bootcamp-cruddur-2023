# Week 0 — Billing and Architecture

## Required Homework/Tasks

### Install and Verify AWS CLI

I was not able to use Gitpod or Github Codespaces due to browser issues
So I decided to use a local environment.

In order to prove that I am able to use the AWS CLI.
I am providing the instructions I used for my configuration of my local machine on windows.

I did the following steps to install AWS CLI

I installed the AWS CLI for Windows 10 via command in **Command Prompt**:

I followed the instructions on the [AWS CLI Install Documentation Page](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

![Installing AWS CLI](assets/Installing%20windows%20AWS%20CLI.png)

```
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
```

I attempted to run the command by typing in 'aws' but I recive an error

```
C:\Users|selam>aws
'aws' is not recognized as an internal or external command,
operable program or batch file.
```

I was able to resolve the error by closing command prompt, and opening it again.

![Proof of of Working AWS CLI](assets/Proof%20of%20working%20aWS%20CLI.png)


# Getting the AWS CLI Working
We'll be using the AWS CLI often in this bootcamp, so we'll proceed to installing this account.

## Install AWS CLI

* We are going to install the AWS CLI when our Gitpod environment launches.
* We are going to set AWS CLI to use partial autoprompt mode to make it easier to debug CLI commands.
* The bash commands we are using are the same as the [AWS CLI install Instruction](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

Update our ``` .gitpod.yml ``` to include the following taks

```
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT

```

To run thes commands indivsually to perform the install manually

* Go to [IAM Users Console](https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1) Tesfie01 create a new user
*  ``` Enable console access ``` for the user
*  Create a new ``` Admin ``` Group and apply ``` AdministratorAccess ```
*  Create the user and go find and click into the user
*  Click on ``` Security Credentials ``` and ``` Create Access Key ```
*  Choose AWS CLI Access
*  Download the CSV with the credentials

### Set Env Vars

To set these credentials for the current bash termials

```
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1

```

To tell Gitpod to remember these credentials if we relaunch our workspaces

```
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1

```

## To check that the AWS CLI is working and you are the expected user

```
aws sts get-caller-identity

```
You should see something like this:

```
{
  "UserID":"ABCDEFGHIGKLMENOPR",
  "Account": "xxxxxxxxxx",
  "Arn": "arn:aws:iam::xxxxxxxxxxxx:user/Tesfie"
}

```

### Recreate Logical Architectural Design

![Cruddur Logical Design](assets/Logical-Architecture-recreation-diagram.png)

[Lucid Charts Share Link](https://lucid.app/lucidchart/aec6d9f5-e6a3-42a2-8aaf-cb6a76a0fc63/edit?viewport_loc=-114%2C93%2C1707%2C753%2C0_0&invitationId=inv_85db70eb-26ed-45e7-91f2-634df1312b23)


# Enable Billing:

We need to trun on Billing Alerts to recieve alerts....

* In the Root Account go to the Billing Page.
* Under ``` Billing Preferences ``` Choose ``` Receive Billing Alerts ```
* Save Preferences

## Creating a Billing Alarm

### Crfeate SNS Topic

* We need an SNS topic before we create an alarm.
* The SNS topic is what will delivery us an alert when we get overbilled
* aws sns crfeate-topic

We will create a SNS Topic

``` aws sns create-topic --name billing-alarm ```

Which will return a TopicARN

```
aws sns subscribe \
  --topic-arn TopicARN \
  --protocol email \
  --notification-endpoint your@email.com

```
Check your email and confirm the subscription

### Create Alarm

* aws cloudwatch put-metric-alarm
* Create an Alarm via AWS CLI
* We need to update the configuration json script whith the TopicARN we generated earlier
* We are just a json file becuase --metrics is required for expressions and so its easier to us a JSON file.

```
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm-config.json

```


### To Create an AWS Budget

I created my own Budget for $10 because I cannot affor any kind of spend.
I did not create a second Budget because I was concerned of budget spending going over the 2 budget free limit.

Get an AWS Account ID
```
aws sts get-caller-identity --query Account --output text

```

* Supply an AWS Account ID
* Update the json files
* this is another case with AWS CLI it's just much easier to json files due to lots of nested json

```
aws budgets create-budget \
  --account-id AccountID \
  --budget file://aws/json/budget.json \
  --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json

```

![Image of The Budget Alarm I Created](assets/Budgeting%20Alarm.png)



### Homework Challenges .....

### Adding Security Components to the Logical Diagram .......

### Realtime Websockets ........

### References .......

