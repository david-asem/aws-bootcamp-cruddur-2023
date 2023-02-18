# Week 0 — Billing and Architecture

## Getting the AWS CLI Working

I installed the aws cli with the commands below:

```sh
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

### Create a new User and Generate AWS Credentials
I created a new Admin user accout for myself by following the steps below:

- Go to your AWS console and create a new user
- `Enable console access` for the user
- Create a new `Admin` Group and apply `AdministratorAccess`
- Create the user and go find and click into the user
- Click on `Security Credentials` and `Create Access Key`
- Choose AWS CLI Access
- Download the CSV with the credentials

I needed to set these credentials for the bash terminal in my gitpod workspace
```
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1
```

Then I had to let gitpod remember these credentials so any time I returned to my workspace, gitpod will use the same credentials.
``
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
``

## Enable Billing 

I turned on Billing Alerts to recieve alerts...


- In my Root Account I did [Billing Page](https://console.aws.amazon.com/billing/)
- Under `Billing Preferences` I Chose `Receive Billing Alerts`
- I Saved the Preferences


## Creating a Billing Alarm

### Create SNS Topic

- I needed an SNS topic before I could create an alarm.

I'll create a SNS Topic
```sh
aws sns create-topic --name billing-alarm
```
which will return a TopicARN

I created a subscription to supply the TopicARN and my Email
```sh
aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint your@email.com
```

#### Create Alarm

- [aws cloudwatch put-metric-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)
- [Create an Alarm via AWS CLI](https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-estimatedcharges-alarm/)
- I updated the configuration json script with the TopicARN I generated earlier

```sh
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json
```

## Create an AWS Budget

[aws budgets create-budget](https://docs.aws.amazon.com/cli/latest/reference/budgets/create-budget.html)

I used the command below to get my AWS Account ID
```sh
aws sts get-caller-identity --query Account --output text
```

- Supply my AWS Account ID
- Update the json files

```sh
aws budgets create-budget \
    --account-id AccountID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```

## Recreated Logical Architectural Diagram

I recreated the logical architecture diagram for the Cruddur project.
![Cruddur Logical Design](/assets/logical-architecture.png)

[Lucid Charts Link](https://lucid.app/lucidchart/e221693a-21ad-4d1f-b3d5-244a7ec82928/edit?viewport_loc=142%2C-219%2C1820%2C901%2CR01soSDRiqq8&invitationId=inv_11ea1ea9-36cd-41c5-97ae-a7c7f9fdfc82)
