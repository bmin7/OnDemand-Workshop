# OnDemand-Workshop Guide

Welcome to Panther's OnDemand Workshop Guide. This sheet will provide resources throughout your workshop experience allowing you to work at your own pace and dig into additional resources available to you. Each Hands-On Lesson is outlined below with step by step instructions and code-snippets to expedite your experience. Additionally, there are links to documentation, key phrases, and resources you'll leverage during this workshop. For any additional support you need, please visit www.panther.com 

## PreRequisites 

Before getting started, please complete the prerequisities below: 

**Request a [Panther Trial](https://panther.com/free-trial/)**
- *Note that this may take up to 24 Hours to Provision*

**Install Panther Analysis Tool Prerequisites for local development in Lesson 3**
1. Install [Pip3 and Python3](https://medium.com/swlh/installing-python-and-pip-on-mac-72b7639a58) to your local Machine 
2. [Create](https://docs.github.com/en/get-started/signing-up-for-github/signing-up-for-a-new-github-account) a Github account (If you already have one, skip this step)
3. Install [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) to your local machine

## Lesson 1 - Writing Your First Panther Detection from Scratch

**Part 1 - Create New Detection in Panther UI**
1. In the Panther Console - Navigate to Build > Detections > Create New
2. Give it a unique name "Brandon's Failed AWS Login Rule" (Use your own name or initials)
3. Set Severity to "Medium" and Log Types "AWS.CloudTrail"

**Part 2 - Create Unit Test**
1. Select "Functions and Tests" in the tab below
2. Scroll down and select the "Create Test" button
3. Delete the brackets populated. Copy and paste the sample event below into your console:

**CloudTrail logging IAM Event Log**
```
{
	"additionalEventData": {
		"LoginTo": "https://console.aws.amazon.com/console/",
		"MFAUsed": "No",
		"MobileVersion": "No"
	},
	"awsRegion": "us-east-1",
	"eventID": "1",
	"eventName": "ConsoleLogin",
	"eventSource": "signin.amazonaws.com",
	"eventTime": "2019-01-01T00:00:00Z",
	"eventType": "AwsConsoleSignIn",
	"eventVersion": "1.05",
	"p_event_time": "2021-06-04 09:59:53.650807",
	"p_log_type": "AWS.CloudTrail",
	"p_parse_time": "2021-06-04 10:02:33.650807",
	"recipientAccountId": "123456789012",
	"requestParameters": null,
	"responseElements": {
		"ConsoleLogin": "Failure"
	},
	"sourceIPAddress": "111.111.111.111",
	"userAgent": "Mozilla",
	"userIdentity": {
		"accountId": "123456789012",
		"arn": "arn:aws:iam::123456789012:user/tester",
		"principalId": "1111",
		"type": "IAMUser",
		"userName": "tester"
	}
}
```

**Part 3 - Writing your detection code**

1. Identity the fields within the event log that correspond to a failed login. This is the Console Login field nested under the Response Element
2. In order to grab the nested field, we need to use the [deep_get function](https://docs.panther.com/writing-detections/globals#deep_get). This is located within the a [Panther Helper Library](https://docs.panther.com/writing-detections/globals) called panther_base_helpers.
3. Import deep_get function from the panther_base_helpers library ```from panther_base_helpers import deep_get``` into your detection
4. All detections require a "rule" function. To add yours write ```def rule(event)```
5. Using event.get and deep_get to grab attributes from the event log, write a return statement that is TRUE when a console login attempt fails

**Part 4 - Testing Your Detection**

1. Run a Test on your new detection to check for syntax errors and proper logic
2. If it passes, you've completed your first detection. 




