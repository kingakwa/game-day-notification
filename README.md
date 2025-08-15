# NBA Game Day Notifications / Sports Alerts System

## **Project Overview**
This project is an alert system that sends real-time NBA game day score notifications to subscribed users via SMS/Email. It leverages **Amazon SNS**, **AWS Lambda and Python**, **Amazon EvenBridge** and **NBA APIs** to provide sports fans with up-to-date game information. The project demonstrates cloud computing principles and efficient notification mechanisms.

---

## **Features**
- Fetches live NBA game scores using an external API.
- Sends formatted score updates to subscribers via SMS/Email using Amazon SNS.
- Scheduled automation for regular updates using Amazon EventBridge.
- Designed with security in mind, following the principle of least privilege for IAM roles.

## **Prerequisites**
- Free account with subscription and API Key at [sportsdata.io](https://sportsdata.io/)
- Personal AWS account with basic understanding of AWS and Python

---

## **Technical Architecture**

<img width="1337" height="1001" alt="Image" src="https://github.com/user-attachments/assets/66c28732-c2ab-480b-a18c-f775ece9946f" />

---


## **Technologies**
- **Cloud Provider**: AWS
- **Core Services**: SNS(simple notification service), Lambda, EventBridge
- **External API**: NBA Game API (SportsData.io)
- **Programming Language**: Python 3.x
- **IAM Security**:
  - Least privilege policies for Lambda, SNS, and EventBridge.

---

## **Project Structure**
```bash
game-day-notifications/
├── src/
│   ├── gd_notifications.py          # Main Lambda function code
├── policies/
│   ├── gb_sns_policy.json           # SNS publishing permissions
│   ├── gd_eventbridge_policy.json   # EventBridge to Lambda permissions
│   └── gd_lambda_policy.json        # Lambda execution role permissions
├── .gitignore
└── README.md                        # Project documentation
```

## **Setup Instructions**

### **Clone the Repository**
```bash
git clone https://github.com/kingakwa/game-day-notification.git
cd game-day-notifications
git remote remove origin
git remote add origin https://github.com/kingakwa/game-day-notification.git
git branch -M main
git push -u origin main
```

### **Create an SNS Topic**
1. Open the AWS Management Console.
2. Navigate to the `SNS service`.
3. Click `Create Topic` and select `Standard` as the topic type.
4. Name the topic (e.g., gd_topic) and note the ARN.
5. Click `Create Topic`.

### **Add Subscriptions to the SNS Topic**
1. After creating the topic, click on the `topic name` from the list.
2. Navigate to the Subscriptions tab and click `Create subscription`.
3. Select a Protocol:
- For Email:
  - Choose `Email`.
  - Enter a valid email address.
- For SMS (phone number):
  - Choose `SMS`.
  - Enter a valid phone number in international format (e.g., +1234567890).


4. Click Create Subscription.
5. If you added an Email subscription:
- Check the inbox of the provided email address.
- `Confirm` the subscription by clicking the confirmation link in the email.
6. For SMS, the subscription will be immediately active after creation.

<img width="914" height="357" alt="Image" src="https://github.com/user-attachments/assets/c7d57917-59fa-48cf-b4eb-127374a14972" />


### **Create the SNS Publish Policy**
1. Open the `IAM` service in the AWS Management Console.
2. Navigate to `Policies` → `Create Policy`.
3. Click `JSON` and **paste** the JSON policy from gd_sns_policy.json file
4. Replace `REGION and ACCOUNT_ID` with your `AWS region and account ID`.
5. Click `Next`: Tags (you can skip adding tags).
6. Click `Next`: Review.
7. Enter a `name` for the policy (e.g., gd_sns_policy).
8. Review and click `Create Policy`.

### **Create an IAM Role for Lambda**
1. Open the `IAM `service in the AWS Management Console.
2. Click `Roles` → `Create Role`.
3. Select AWS Service and choose `Lambda`.
4. Attach the following policies:
- SNS Publish Policy (gd_sns_policy) (created in the previous step).
- Lambda Basic Execution Role (AWSLambdaBasicExecutionRole) (an AWS managed policy).
5. Click `Next`: Tags (you can skip adding tags).
6. Click `Next`: Review.
7. Enter a `name` for the role (e.g., gd_role).
8. Review and click `Create Role`.
9. Copy and save the ARN of the role for use in the Lambda function.

<img width="919" height="307" alt="Image" src="https://github.com/user-attachments/assets/c2a60ce3-76d4-4fa5-9db9-325f6146e605" />

### **Deploy the Lambda Function**
1. Open the AWS Management Console and navigate to the `Lambda service`.
2. Click `Create Function`.
3. Select `Author from Scratch`.
4. Enter a `function name` (e.g., gd_notifications).
5. Choose `Python 3.x` as the runtime.
6. Assign the IAM role created earlier (gd_role) to the function.
7. Under the Function Code section:
- `Copy` the content of the src/gd_notifications.py file from the repository.
- Paste it into the `inline code editor`(VScode).
8. Under the Environment Variables section, add the following:
- `NBA_API_KEY: your NBA API key`.
- `SNS_TOPIC_ARN`: the ARN of the SNS topic created earlier.
9. Click Create Function.

  <img width="402" height="407" alt="get-API-key" src="https://github.com/user-attachments/assets/60518a68-5908-4e59-88ef-d8093ecad2f9" />

 <img width="374" height="240" alt="Image" src="https://github.com/user-attachments/assets/7c616bd8-5e6a-437d-bfa5-802678978dda" />
 

### **Set Up Automation with Eventbridge**
1. Navigate to the Eventbridge service in the AWS Management Console.
2. Go to Rules → `Create Rule`.
3. Select `Event Source`: Schedule.
4. Set the cron schedule for when you want updates (e.g., hourly).
5. Under Targets, select the `Lambda function` (gd_notifications) and save the rule.

<img width="934" height="379" alt="Image" src="https://github.com/user-attachments/assets/a19f880a-7e56-45b4-b0aa-c94729a60896" />


### **Test the System**
1. Open the Lambda function in the AWS Management Console.
2. Create a `test `event to simulate execution.
3. Run the function and check CloudWatch Logs for errors.
4. Verify that SNS notifications are sent to the subscribed users.
   
<img width="878" height="311" alt="Image" src="https://github.com/user-attachments/assets/57998c18-f415-4560-b0cb-7b9f56435251" />

<img width="918" height="253" alt="Image" src="https://github.com/user-attachments/assets/04e71c07-f046-4e45-a1e7-19b26015e1e7" />

After the test succeeds, your function will have made the API call, formatted the data, and published a message to your SNS topic. If your email is confirmed as a subscription to that topic, you will receive the notification in your inbox shortly.

<img width="629" height="302" alt="Image" src="https://github.com/user-attachments/assets/874d00c1-95ff-4cf0-be71-abd1822d8f81" />

## **Schedule with EventBridge**
Go to `Amazon EventBridge` → `Scheduler` or Rules → `Create schedule`.
Target = your `Lambda function`.
Frequency = `daily/hourly` as needed.
`Save`.
You will receive the `notification` in your inbox base on your schedule set

<img width="770" height="307" alt="SCHEDULE-SET" src="https://github.com/user-attachments/assets/2e3295d0-2385-494d-a57d-17fd581c686e" />

<img width="613" height="283" alt="SCHUDULE-SUCCESS" src="https://github.com/user-attachments/assets/ba2cd01f-523e-4fdb-a786-b67a99e48844" />

### **What We Learned**
1. Designing a notification system with AWS SNS and Lambda.
2. Securing AWS services with least privilege IAM policies.
3. Automating workflows using EventBridge.
4. Integrating external APIs into cloud-based workflows.


### **Future Enhancements**
1. Add NFL score alerts for extended functionality.
2. Store user preferences (teams, game types) in DynamoDB for personalized alerts.
3. Implement a web UI

In conclusion, this project successfully automated scheduled workflows using AWS EventBridge for precise cron-based triggers, AWS Lambda for serverless execution of business logic, and Amazon SNS for real-time email notifications. The solution integrates seamlessly to run tasks at defined business-hour intervals, ensuring accuracy and eliminating manual effort. Cloud-native scalability ensures high reliability, while automation improves efficiency and reduces operational costs. This architecture demonstrates how EventBridge, Lambda, SNS, and supporting AWS services can work together to deliver a robust, maintainable, and future-ready automation framework

Happy learning
