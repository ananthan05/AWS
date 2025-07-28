## Overview

This lab demonstrates AWS monitoring and logging by analyzing CloudTrail event history, creating a CloudTrail trail with CloudWatch logging, and setting up an SNS topic for alerts. It includes configuring EventBridge rules to track resource changes, creating CloudWatch metric filters and alarms for log patterns, and using CloudWatch Logs Insights to query and analyze CloudTrail logs.

## Task 1: Creating a CloudTrail trail with CloudWatch Logs enabled

### Analyze the information available in the CloudTrail event history

Open cloudtrail and access `event history` from side pane

<img width="1893" height="836" alt="image" src="https://github.com/user-attachments/assets/06df633e-7524-43b1-a0ac-040a54d5b594" />

From the Read-only dropdown menu under the Event history section heading, choose Event source.

<img width="992" height="574" alt="image" src="https://github.com/user-attachments/assets/7baaa5fb-e833-4b35-b138-f2eb23c27037" />

<img width="1612" height="658" alt="image" src="https://github.com/user-attachments/assets/bcc2cfbc-2931-4265-ba9e-0112802359ae" />

now search this `cloudformation.amazonaws.com` and select it when it appears on the search bar

Choose the most recent CreateStack event

<img width="1151" height="487" alt="image" src="https://github.com/user-attachments/assets/f28535e2-68bc-410d-9279-0d719af377fc" />

The CreateStack event occurred when you started this lab. The stack created resources in the account. Notice that the record includes details such as the userIdentity for the person who made the API call, eventTime, and awsRegion. Other essential audit record details are also provided.

### Create a CloudTrail trail with CloudWatch Logs enabled

Select Trials from the side navigation pane

<img width="1855" height="775" alt="image" src="https://github.com/user-attachments/assets/3af73418-209e-46d1-88ee-382dc04a4506" />

Select `Create Trail`

Trail name: Enter `MyLabCloudTrail

Storage location: Choose Create a new S3 bucket, and accept the default bucket name

Log file SSE-KMS encryption: Clear the check box (to disable this option).

CloudWatch Logs: Select Enabled.

Log group: Choose New, and accept the default log group name.

IAM Role: Choose Existing.

Role name: Choose `LabCloudTrailRole`.

<img width="1249" height="745" alt="image" src="https://github.com/user-attachments/assets/808ce1eb-8eab-48ef-add5-1627bcd0796d" />

Now click next

On the choose log events page, configure the following:

Event type: Keep Management events selected

API activity: Keep Read and Write selected.

<img width="1169" height="730" alt="image" src="https://github.com/user-attachments/assets/ad6cc360-c75e-4190-9da7-7d5531796b4b" />

click next

This is where you would complete the process to create the trail. However, the user that you are logged in as does not have the necessary permissions to create a CloudTrail with CloudWatch Logs enabled.

So choose cancel

Analyze the existing CloudTrail trail.

A trail named LabCloudTrail already exists. It is configured with the same settings that you chose in the previous step

## Task 2: Creating an SNS topic and subscribing to it

In this task, you will create an SNS topic and subscribe your email address to the topic. The topic will be used in later tasks to deliver email alerts to you about important activity that occurs in the AWS account.

search for and choose Simple Notification Service to open the Amazon SNS console.

<img width="1066" height="524" alt="image" src="https://github.com/user-attachments/assets/bdcc51b7-9658-4f9b-9d0b-969146ff47aa" />

Select topics

Type: Choose Standard.

Name: Enter MySNSTopic

Expand the Access policy - optional section.

Specify who can publish messages to the topic: Choose Everyone.

Specify who can subscribe to this topic: Choose Everyone.

Click Create Topic

<img width="1402" height="593" alt="image" src="https://github.com/user-attachments/assets/bef237d1-584c-4ab8-85e0-3a3df8656f45" />

<img width="1580" height="686" alt="image" src="https://github.com/user-attachments/assets/b53f5114-0f28-4ccb-be69-a20a48bd1908" />

To create an email subscription to the SNS topic, choose Create subscription

<img width="1456" height="663" alt="image" src="https://github.com/user-attachments/assets/3d23d39f-4881-4ab0-b700-b42834f612a3" />

Topic ARN:The Amazon Resource Number (ARN) of the topic that you just created is already filled in.

Protocol: Choose Email.

Endpoint: Enter an email address where you can receive emails during this lab.

### Check your email and confirm the subscription.

Check your email for a message from AWS Notifications.In the email body, choose the Confirm subscription link.A webpage opens and displays a message that the subscription was successfully confirmed.

## Task 3: Creating an EventBridge rule to monitor security groups

In this task, you will create an EventBridge rule. The rule will notice whenever inbound rule changes are made to a new or existing security group in the same Region in your AWS account. Whenever the rule conditions are met, the rule will publish a message to the SNS topic that you created.

### Create a rule to monitor changes to EC2 security groups.

Open `Amazon EventBridge`

Choose Create rule

Name:  `MonitorSecurityGroups`

Event bus: default

Rule type: Rule with an event pattern.

<img width="1153" height="704" alt="image" src="https://github.com/user-attachments/assets/77a79cb0-3b02-4590-b0de-ca9ad408d34a" />

In the Build event pattern screen, enter the following details:

Event source: AWS events or EventBridge partner events

Leave the Sample event - optional default settings

Under Event pattern, choose Custom patterns (JSON editor)

Copy and paste the following code into the Enter the event JSON field

```bash
{
  "source": ["aws.ec2"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventSource": ["ec2.amazonaws.com"],
    "eventName": ["AuthorizeSecurityGroupIngress", "ModifyNetworkInterfaceAttribute"]
  }
}
```

<img width="1154" height="619" alt="image" src="https://github.com/user-attachments/assets/57f2dde3-7502-4d76-9b87-09feecaf0ccc" />

Select targets section, configure the following for Target 1:

Target types: AWS service

Select a target: Choose SNS topic.

Topic: Choose MySNSTopic.

Permissions:  UnCheck  Use execution role (recommended)

<img width="1173" height="559" alt="image" src="https://github.com/user-attachments/assets/2cfd3251-0e42-4714-92fe-cde2be32f851" />

Expand  Additional settings

For Configure target input, choose Input transformer.

<img width="1205" height="649" alt="image" src="https://github.com/user-attachments/assets/362e67e9-a767-4e7e-939f-2b58b6e63ad5" />

Choose Configure input transformer.

Scroll down to the Target input transformer section.

In the Input path field (first box), copy and paste the following code:

```
{"name":"$.detail.requestParameters.groupId","source":"$.detail.eventName","time":"$.time","value":"$.detail"}
```

In the Template field, copy and paste the following text:

```
"The <source> API call was made against the <name> security group on <time> with the following details:"
" <value> "
```

<img width="1003" height="853" alt="image" src="https://github.com/user-attachments/assets/a71704b2-6b74-4e46-a90d-09d8fbb4c3b3" />



The Input path you are setting defines four variables: name, source, time, and value. For each variable, a value is set by referencing data contained in the JSON structure of CloudTrail events that match the event pattern that you also defined. The Input template that you are setting defined the information that will be passed to the target, which in this case is an SNS topic. Notice that the template includes the names of the four variables defined in the Input path.

Choose Confirm then choose Next.

In the Configure tags screen choose Next.

At the Review and create screen, scroll to the botton and choose Create rule.

### Testing the EventBridge rule

Select EC2,In the navigation pane, choose Instances.

Select the check box for LabInstance.This instance was created for you when you started the lab.

<img width="1433" height="834" alt="image" src="https://github.com/user-attachments/assets/42dca63a-2cac-4242-9ff1-09655650617f" />

In the lower pane, choose the Security tab.Under Security groups, choose the link for the security group name that contains LabSecurityGroup.

Details for this security group display.

<img width="1585" height="693" alt="image" src="https://github.com/user-attachments/assets/84dc7b39-9fef-4301-8041-d1bb65defe0b" />

On the Inbound rules tab, choose Edit inbound rules.

Choose Add rule, and configure the following:

Type: Choose SSH.

Source: Choose Anywhere-IPv4.

Choose Save rules.

 <img width="1811" height="361" alt="image" src="https://github.com/user-attachments/assets/b1fa78d0-9d7e-481d-b900-eb278e8e0bb7" />

Check the CloudTrail event history.

Navigate to the CloudTrail console.

In the navigation pane, choose Event history

<img width="1586" height="544" alt="image" src="https://github.com/user-attachments/assets/49aa9201-c04c-4511-8c54-c0bd69dee8b9" />

Choose the AuthorizeSecurityGroupIngress link. In the Event record make sure the fromPort and toPort show 22 and not 80

<img width="195" height="83" alt="image" src="https://github.com/user-attachments/assets/f2ac8fa3-e413-414b-8749-55500eb39ee9" />

In the Event record section, notice that details of this event match some of the details that you set in the EventBridge rule that you created a moment ago. Specifically, the "eventSource": "ec2.amazonaws.com" and "eventName": "AuthorizeSecurityGroupIngress" name-value pairs in the event match the event pattern that you defined in the rule. Therefore, this event should result in a message being published to the SNS topic that you created

### Check the inbox of the email address that you subscribed to the SNS topic.

You should have received a message from AWS Notifications indicating that an AuthorizeSecurityGroupIngress API call was made. The API call occurred when you modified the security group.

## Task 4: Creating a CloudWatch alarm based on a metrics filter

### Create a CloudWatch metric filter

Open Cloudwatch.In the navigation pane, expand  Logs, and then choose Log groups.

Select the check box for CloudTrailLogGroup

<img width="1566" height="676" alt="image" src="https://github.com/user-attachments/assets/f301a289-0ecc-4dc7-bc13-6b924e38617f" />

Choose Actions > Create metric filter, and then configure the following

Filter pattern: Copy and paste the following code:

```{ ($.eventName = ConsoleLogin) && ($.errorMessage = "Failed authentication") }```

Choose Next.

Filter name: Enter ConsoleLoginErrors

Metric namespace: Enter CloudTrailMetrics

Metric name: Enter ConsoleLoginFailureCount

Metric value: Enter 1

Click Next.Choose Create metric filter.

### Create a CloudWatch alarm based on the metric filter.

On the Metric filters tab, select the check box to the right of the ConsoleLoginErrors metric filter that you just created.

Choose Create alarm.

<img width="1510" height="703" alt="image" src="https://github.com/user-attachments/assets/b5eaad8a-47b5-4730-ae49-aa22eeec4912" />

Under Conditions tab change these

Whenever ConsoleLoginFailureCount is: Choose Greater/Equal.

than...: Enter 3

<img width="1495" height="564" alt="image" src="https://github.com/user-attachments/assets/ee7e20a0-839f-4886-a759-91afc25aea7a" />

On the Configure actions page, configure the following:

Select an SNS topic: Choose Select an existing topic.

Send a notification to...: Choose MySNSTopic.

<img width="1493" height="566" alt="image" src="https://github.com/user-attachments/assets/22e603c4-57df-4a2e-8d9a-edc0b4a72f85" />

Choose Next.

On the Add name and description page, configure the following:

Alarm name: Enter FailedLogins

<img width="1091" height="338" alt="image" src="https://github.com/user-attachments/assets/426dd7b1-7d80-43d8-a9f3-e28787b67a74" />

Choose Next. Create an alarm

### Test the CloudWatch alarm by attempting to log in to the console with incorrect credentials at least three times.

Open IAM and select users tab 

<img width="1340" height="625" alt="image" src="https://github.com/user-attachments/assets/19ff6127-5b7d-4905-9ddf-eb657071bda2" />

Select the link for the test user name.

Select the Security credentials tab, and then copy the Console sign-in link.

<img width="1510" height="640" alt="image" src="https://github.com/user-attachments/assets/57ba942a-82c5-47e8-bba4-d38683387cab" />

Enter incorrect credentials. Repeat this at least three times:

 - IAM user name: Enter test

 - Password: test

Choose Sign in.

Now close all tabs and re-open the lab

Now navigate to the CloudWatch console.

In the navigation pane, expand  Metrics, and then choose All metrics.

In the Metrics section, under Custom namespaces, choose CloudTrailMetrics.

<img width="1270" height="648" alt="image" src="https://github.com/user-attachments/assets/74f8d6d7-eaf0-42f9-b829-1d9b193f5f0a" />

Now select Metrics with no dimensions and ConsoleLoginFailureCount.

In the graph area at the top of the page, a small blue dot should appear. The dot indicates that a login failure was detected.

### Check the alarm status and details in the CloudWatch console.

In the navigation pane, expand  Alarms, and then choose All alarms.

The State for the FailedLogins alarm should be In alarm.

<img width="1523" height="308" alt="image" src="https://github.com/user-attachments/assets/824d59c0-7c77-4736-ba6a-45edeee52f2a" />

## Task 5: Querying CloudTrail logs by using CloudWatch Logs Insights

CloudWatch Logs Insights enables you to interactively search and analyze your log data in Amazon CloudWatch Logs. You can perform queries to help you more efficiently and effectively respond to operational issues.

### Run a CloudWatch Logs Insights query.

In the navigation pane, choose Logs Insights.

From the Selection criteria dropdown menu under the Logs Insights section heading, select CloudTrailLogGroup.

Delete the existing content from the query field, and then copy and paste the following code into the query field:

```
filter eventSource="signin.amazonaws.com" and eventName="ConsoleLogin" and responseElements.ConsoleLogin="Failure"
| stats count(*) as Total_Count by sourceIPAddress as Source_IP, errorMessage as Reason, awsRegion as AWS_Region, userIdentity.arn as IAM_Arn
```

Select Run query

You will get an output like this

<img width="1489" height="350" alt="image" src="https://github.com/user-attachments/assets/11119cb6-a84b-449e-8c64-0db9f1cfacd2" />

Now submit your work

