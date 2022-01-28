# Logging System Check failure events to Centralized Logging System.

** Describe Solutions here **

System status checks monitors the AWS system on which your instance runs. These checks detects underlying problems with your instance that required AWS involvement to repair. When systems check fails, you can chose to wait for AWS to fix the issue and usually that is reboot of your instance which bring instance up on another server. Customer can stay on top of these events and fix his/her instance proactively. You can do that by configuring CloudWatch (CW) alarm on the required instance. In case of failure of health of your instance, CW alarm will be triggered which you can configure to send notification to SNS and then you can chose to trigger lambda to reboot the instance as well.

This solution is not about fixing your instance in such failures but log such activities into centralized logging system for compliance and audit purpose. In this solution, system status check alarm will notify the SNS which will trigger lambda in the same account which will collect the payload of SNS event and push it any ETL logging solution (graylog / ELK) that you might have. This solution is also designed for multi-accounts setup where you would have Centralized logging account. In this situation, you will deploy this stack in your logging account and will provide OrgID as parameter while deploying the CloudFormation stack. If you don't supply OrgID while deploying stack, solution will only be deployed for the current account. This solution has provision to update stack to add OrgID later to enable the solution for whole AWS Org.

In this artifact, you will get lambda code along with CF template, that will collect the notifications and send them to your choice of logging system. Lambda code is provided here (escode.zip)

** 1) Please upload lambda code (escose.zip) to S3 of your choice in your account.  **

** 2) Create new Cf stack with either CloudFormationTemplateES.yaml

** 3) Enter the parameter  **

* a. AWSOrgID if you want to deploy this solution for all accounts under AWS Org
* b. Open Search/ Elastic Search Server
* c. Open Search/ Elastic Search Server port
* d. Open Search/ Elastic Search User
* e. Open Search/ Elastic Search Password
* f. S3 Bucket name (Location of lambda code. Ref to pre-requisite)
* g. S3 Key (Folder under S3 where code is uploaded)
* h. Security Group (Drop down list to select existing SG that will give Lambda access to Elastic Search Server. Default port for ES server is 443)
* i. Subnet where Lambda would be deployed to get access to Open Search/ Elastic SearchElastic Search Server

** 4) Deploy the stack and check resources been created as part of stack.  Please note down SNS topic that is created. **

** 5) Now you can create CW alarm on any EC2 instance in the same account or any account from an org and put alarm action to SNS topic that was noted down in step 4. Here is the sample CLI to create one. **

aws cloudwatch put-metric-alarm --alarm-name StatusCheckFailed-Alarm-for-i-0e03d45b83eb527f2  --metric-name StatusCheckFailed --namespace AWS/EC2 --statistic Maximum --dimensions Name=InstanceId,Value=i-0e03d45b83eb527f2  --unit Count --period 300 --evaluation-periods 2 --threshold 1 --comparison-operator GreaterThanOrEqualToThreshold --alarm-actions arn:aws:sns:us-west-2:123456789003:Logging-Stack1-SNSTopic-SQX8JK3ND61Q

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.
