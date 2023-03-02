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

Detection-as-Code starts with the basics of creating a detection with Python code logic. In order to create a base-level of what we'll learn in this workshop, we'll begin by creating our first Python-based detection in the Panther Console. This will touch on several of the terms below: 

- [Deep_Get](https://docs.panther.com/writing-detections#using-global-helper-functions)
- [Helper Functions](https://docs.panther.com/writing-detections/globals)
- [Unit Tests](https://docs.panther.com/writing-detections/testing)


**Part 1 - Create New Detection in the Panther Console**
1. In the Panther Console - Navigate to _Build > Detections > Create New_
2. Give it a unique name _"Brandon's Failed AWS Console Login Rule"_ (Use your own name or initials)
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

**Recap of Detection-as-Code**
1. Code Reuse - With pre-built helpers and libraries, code reuse makes writing detections extremely quick.
2. Testing - Unit Tests in product create a fast way to get direct feedback on detection code logic.


## Lesson 2 - Tune a Panther Provided Detection

Panther provides over 400+ detections out-of-the-box that can be customized to any environment with either python or rule filters. Using pre-defined Python functions from Panther, we'll be able to easily tune a detection in minutes. Terms we'll use in this section include: 

-[Packs](https://docs.panther.com/writing-detections/detection-packs)
-[Panther Analysis Repository](https://github.com/panther-labs/panther-analysis)
-[Rule Filters](https://docs.panther.com/writing-detections/rules/rule-filters)


**Part 1 - Clone and Edit a Panther Managed Detection from a Pack**

1. In the Panther Console - Navigate to _Build > Packs > Panther Core AWS Pack_
2. Select the detection called "AWS GuardDuty High Severity Finding"
3. Select Clone & Edit on the Top Right
- Note if you're using your own production instance for this workshop, you can not clone a detection more than once.

**Part 2 - Create Detection and add Unit Test*

1. Name the detection a unique name with your initials - Sample "AWS GuardDuty High Severity Finding - Brandon". All other fields should already be pre-populated.
2. Select Functions & Tests
3. Scroll down and populate test with log if not already done

**CloudTrail GuardDuty Log**
```
{
"accountId": "123456789012",
"arn": "arn:aws:guardduty:us-west-2:123456789012:detector/111111bbbbbbbbbb5555555551111111/finding/90b82273685661b9318f078d0851fe9a",
"createdAt": "2020-02-14T18:12:22.316Z",
"description": "Principal AssumedRole:IAMRole attempted to add a highly permissive policy to themselves.",
"id": "eeb88ab56556eb7771b266670dddee5a",
"partition": "aws",
"region": "us-east-1",
"schemaVersion": "2.0",
"service": {
	"action": {
		"actionType": "AWS_API_CALL",
		"awsApiCallAction": {
			"affectedResources": {
				"AWS::IAM::Role": "arn:aws:iam::123456789012:role/IAMRole"
			},
			"api": "PutRolePolicy",
			"callerType": "Domain",
			"domainDetails": {
				"domain": "cloudformation.amazonaws.com"
			},
			"serviceName": "iam.amazonaws.com"
		}
	},
	"additionalInfo": {},
	"archived": false,
	"count": 1,
	"detectorId": "111111bbbbbbbbbb5555555551111111",
	"eventFirstSeen": "2020-02-14T17:59:17Z",
	"eventLastSeen": "2020-02-14T17:59:17Z",
	"evidence": null,
	"resourceRole": "TARGET",
	"serviceName": "guardduty"
},
"severity": 8,
"title": "Principal AssumedRole:IAMRole attempted to add a policy to themselves that is highly permissive.",
"type": "PrivilegeEscalation:IAMUser/AdministrativePermissions",
"updatedAt": "2020-02-14T18:12:22.316Z"
}
```


**Part 3 - Tune Detection with Severity Function**
1. Capture all guardduty detections as alerts in Panther, but tune out the lower end ones. 
2. Modify the rule function to alert on events from severity 1 to 10
3. To reduce noise of this detection, use the severity function to create dynamic categorization of alerts
4. Use an IF statement to send severity 5 and below alerts to "INFO" level and 8 and above to "HIGH". For any other severity, return "MEDIUM"


## Lesson 3 - Using Local Developer Tools to Write, Test, and Deploy Detection
Use the Panther Analysis Tool (PAT) with local developer tools to write and test new detections. 


**Terms we'll reference**
- [Panther Analysis Tool](https://docs.panther.com/panther-developer-workflows/panther-analysis-tool#overview)
- [API Key](https://docs.panther.com/panther-developer-workflows/api#how-to-use-panthers-api)


**Exercise 3 Steps**
1. Install Prerequisites on local Machine (Pip, Python3, Git)
2. Install Panther Analysis Tool 
```pip install panther_analysis_tool```
3. Verify proper version (for those of you that have it already, you don't have to update your version)
```panther_analysis_tool --version```
4. Fork off Panther Analysis Tool to local 
```git clone https://github.com/panther-labs/panther-analysis.git```
5. Create API Token in Panther Console - Select the gear on the top right > API Tokens > Create New Token
6. Check permissions for Read Panther Settings Info, Bulk Upload, Manage Policies, Manage Rules, Manage Schedule Queries, View Log Sources, Manage Log Sources
7. Use Check-Connection to verify API setup is successful (This is only on Panther Analysis Tool 0.15.1 and up)
```panther_analysis_tool check-connection --api-host DOMAIN --api-token TOKEN```
7. Create new directory and copy a .py and .yml file
8. Modify .py file and .yml file
9. Test the rule
```panther_analysis_tool test --path <path to rule directory>```
10. Once verified, upload the rule
```panther_analysis_tool upload --path <path to rule> --api-host DOMAIN --api-token TOKEN```
11. Check Panther Console for changes



## Addtional Resources
**Helpful Links**
- [All Available Rule Functions](https://github.com/panther-labs/panther-analysis/blob/master/templates/example_rule.py)
- [What is Deep_Get?](https://docs.panther.com/writing-detections/globals#deep_get)
- [What are Packs?](https://docs.panther.com/writing-detections/detection-packs)
- [Panther Analysis Tool](https://docs.panther.com/panther-developer-workflows/panther-analysis-tool#overview)
- [Lookup Tables](https://docs.panther.com/enrichment/lookup-tables)
- [Unit Tests](https://docs.panther.com/writing-detections/testing#mocks)
- [Panther Analysis Tool](https://docs.panther.com/panther-developer-workflows/panther-analysis-tool#overview)
- [API Key](https://docs.panther.com/panther-developer-workflows/api#how-to-use-panthers-api)

**Support**
-[Support Email](support@panther.com)

