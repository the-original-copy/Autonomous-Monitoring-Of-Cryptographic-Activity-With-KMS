# Autonomous Monitoring Of Cryptographic Activity With KMS

# Table of Contents
1 [Introduction](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#1-introduction) </br>
2 [Methodology](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#2-methodology) </br>
&nbsp; 2.1 [Deploy the base lab infrastructure](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#21-deploy-the-base-lab-infrastructure) </br>
&nbsp; 2.2 [Configure the ECS repository and deploy the application stack](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#22-configure-the-ecs-repository-and-deploy-the-application-stack) </br>
&nbsp; 2.3 [Configure CloudTrail](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#23-configure-cloudtrail) </br>
&nbsp; 2.4 [Configure the workload and logging alarm](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#24-configure-the-workload-and-logging-alarm) </br>
&nbsp; 2.5 [Test the workload functionality](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#25-test-the-workload-functionality) </br>
3 [Conclusion](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#3-conclusion)

#  Introduction

Ensuring a robust security posture in cloud-based applications requires the capability to trace and automatically respond to security events. By diligently monitoring key
activity metrics, architects can identify potentially malicious behavior early on, allowing for timely and appropriate responses. This proactive approach, paired with effective alerting systems, creates an autonomous feedback loop that keeps cloud administrators informed and able to mitigate risks before they escalate into serious incidents. This repository explores the integration of AWS CloudTrail, Amazon CloudWatch, and other services to enhance security monitoring and automated responses within an AWS environment.

#  Methodology

In order to achieve the intended goal of autonomous monitoring of cyrtographic activities with KMS I would have to accomplish the following tasks:
1. Deploy the base lab infrastructure
2. Configure the ECS repository and deploy the application stack
3. Configure CloudTrail
4. Configure the workload and logging alarm
5. Test the workload functionality

##  Deploy the base lab infrastructure

In order to perform this lab I had to have certain prerequisites which included an aws account that is able to be used for testing, the ability to execute aws commands in cli
and docker version 18.09.9 or above. The first two prerequisites had already been met, I installed docker as confirmed by the picture below:

<div align="center">

 ![image](https://github.com/user-attachments/assets/33458665-defe-44d7-b601-d2176a318c75)
<br/>Image 1: Docker version used 

</div>

### i) Get the Cloudformation Template.

I decided to deploy the cloud formation template directly from the command line. I managed to download the CloudFormation Template and proceed to the next steps of the lab.

#### Execute Command

After downloading the template I proceeded to execute the command **aws cloudformation create-stack** in order to create the necessary lab resources as confirmed by the image below:

<div align="center">

  ![image](https://github.com/user-attachments/assets/8200436e-fb71-4df9-9054-92e0a42b8599)
<br/>Image 2: Create Stack Command 

</div>

#### Confirm the stack is correctly installed

After executing the create stack command I had to ensure that the stack was created successfully. I did this by running the command **aws cloudformation describe-stacks** as shown in the below image. The image also confirms that the StackStatus tag has been set to **CREATE_COMPLETE** which has been highlighted.

<div align="center">

  ![image](https://github.com/user-attachments/assets/0631b02d-201d-49a9-95f2-779820f4987f)
<br/>Image 3: Describe Stack 

</div>

As a final confirmation procedure I went to the amazon console and as shown below the stack was successfully created.

<div align="center">

 ![image](https://github.com/user-attachments/assets/dff3913d-625b-47b1-8218-3d22b1e8f850)
<br/>Image 4: Cloud Formation tack panel 

</div>

##  Configure the ECS repository and deploy the application stack

In this section I had to prepare the sample application. I had to package it as a docker image and push it to a repository. Since I did this lab after the cloud 9 services had been discontinued I packaged the docker image and pushed it to the repository from my local machine.

### i) Configure the ECS Container Repository.

#### Download sample_app.zip

I successfully managed to install the sample_app.zip file as shown in the image below:
<div align="center">

  ![image](https://github.com/user-attachments/assets/844c220f-8299-42fa-adb9-0f0c405eb47b)
<br/>Image 5: Sample app installed 

</div>

#### Altered Docker permissions

Before proceeding to the next step I saw it fit to alter docker default permissions. I added docker to the sudo group so that docker commands can run at the administrative level without requiring the sudo key word as confirmed in the image below:

<div align="center">

![image](https://github.com/user-attachments/assets/1e2f638b-769e-4856-8d5d-800247485ffa)
<br/>Image 6: Modified docker permissions

</div>

#### Altered build-container.sh file

The script that would build the docker container and push it was already provided. In order to successfully run it from my local machine I had to alter some of the aws commands present in the script. Most alterations made included manually adding the profile and region tags as shown in the image below.

<div align="center">

  ![image](https://github.com/user-attachments/assets/c2ce875a-7f18-458b-a5d6-9f5dcda0daf9)
<br/>Image 6: Altered build-container.sh file 

</div>

The script also needed variables such as AWS_ACCOUNT and AWS_REGION in order to run properly. The region variable was already provided. In order to get the aws account details I run the command **aws --profile** as shown in the below image. Using those details I was able to insert the details neccesary to deploy the app to docker from my local machine.

<div align="center">

  ![image](https://github.com/user-attachments/assets/04ec4fbc-df56-4f1e-8b3a-f4fdcac882ba)
<br/>Image 7: aws --profile command executed 

</div>

After altering all the necessary details I run the script which was successful as shown by the image below:

<div align="center">

![image](https://github.com/user-attachments/assets/0493cd16-3711-4d60-8951-3d631f5bb318)
<br/>Image 8: Succesfully run the deployment script 

</div>

The image was successfully pushed to the ECR and I took note to the ESC image URI produced. The URI is highlighted in the image below.

<div align="center">
 
![image](https://github.com/user-attachments/assets/6e5b4829-3560-4d81-ba01-faf6360d9fe9)
<br/>Image 9: ECS image URI 

</div>

Lastly I confirmed that the image exists in the **Amazon Elastic Container Registry** as confirmed by the picture below.

<div align="center">

  ![image](https://github.com/user-attachments/assets/a2ca5926-f2ab-42d4-bac0-aa6cd948a0c0)
<br/>Image 10: Private repositories pane 

</div>

### ii) Deploy The Application Stack

Next I had to deploy the application to Amazon ECS. The application is built using nodejs express and the service exposes a REST APPI with **/encrypt** and **/decrypt** actions.

#### Deploying the service linked role

Since this was my first time working with the ECS service, I had to deploy a service linked role which will be able to assume the IAM role to perform the required activities within my account. I used the command **aws iam create-service-linked-role --aws-service-name ecs.amazonaws.com** as shown in the below image: 

<div align="center">

  ![image](https://github.com/user-attachments/assets/da50a427-df3f-4275-b46d-116338b665df)
<br/>Image 11: Service linked role 

</div>

#### CloudFormation Deployment

After creating the service linked role I had to create the stack called pattern1-app. I did this by first downloading the cloudformation app template the proceeded to run the command **aws cloudformation create-stack** with all the necessary parameters as shown in the image below: 

<div align="center">

  ![image](https://github.com/user-attachments/assets/a28c9fae-6f34-48fb-ad1f-da9bdddd21ad)
<br/>Image 12: Created the stack pattern1-app

</div>

Next I had to confirm the successful creation of the stack. I proceeded to the cloud formation console and located the stack named pattern1-app whose state was **CREATE-COMPLETE** as shown in the diagram below.

<div align="center">

  ![image](https://github.com/user-attachments/assets/9f77eb5c-f24e-472f-b66b-0fef2e1d4aed)
<br/>Image 13: Confirmed the stack was created 

</div>

#### Test the Application Launched

Finally I had to confirm that the application had successfully been launched. I recorded the below output details as they would be required later:<br/>
**StackName:** pattern1-app<br/>
**OutputPattern1ApplicationEndpoint:** http://patter-patte-p6lcs13cm5hw-1216160412.ap-southeast-2.elb.amazonaws.com/<br/>
**OutputPattern1ECSTaskRole:** arn:aws:iam::533267206157:role/pattern1-app-Pattern1ECSTaskRole<br/>

After recording the outputs I could now test the encrypt API for the sample application I had just deployed. The application takes a JSON payload with **Name** and
**Text** key, and will encrypt the value under text key with a designated KMS key. Once the text I encrypted, it will store the encrypted text in the RDS database with the **Name** as the primary key.<br/>
In order to test the application I created the variable **ALBURL** and assigned it the **OutputPattern1ApplicationEndpoint** as seen in the picture below. Next I used the curl command to send a post request with the action **/encrypt** and the process was successful as seen in the image below:<br/>

<div align="center">

  ![image](https://github.com/user-attachments/assets/6b6def86-64e3-4da3-9574-ab32d4b645a9)
<br/>Image 14: Successfuly sent the post request 

</div>

I finished this stage of the lab by taking note of the key from the output given. The key has been highlighted.

##  Configure CloudTrail

During this stage I focused on the creation and configuration of the AWS CloudTrail service. This represents the source of record for all API calls generated within the architecture which filters will be applied later.

### i) Command Line Deployment

I downloaded the logging template and proceeded to execute the command **aws cloudformation create-stack** with all the necessary parameters. The arn of OutputPattern1ECSTaskRole was a necessary parameter that was utilized here as shown in the image below:

<div align="center">

  ![image](https://github.com/user-attachments/assets/9f5df3c7-fc6f-40bc-9b55-b22e76232b20)
<br/>Image 15: Created te pattern1-logging stack 

</div>

I proceeded to the amazon console to confirm that the stack was successfully created as shown in the image below:

<div align="center">

  ![image](https://github.com/user-attachments/assets/bc1075b6-c922-4621-b86b-d58ae4c9b18d)
<br/>Image 16: Confirmed the creation of pattern1-logging stack 

</div>

##  Configure the workload and logging alarm

At this stage I was going to create a filter within my CloudWatch Log Group. This filter is to generate a CloudWatch metric which I will use to create the alarm. In order to create the filter I navigated to CloudWatch, choose the log group I created and from the actions pane selected create metric filter a shown in the image below:

<div align="center">

![image](https://github.com/user-attachments/assets/7421cc86-a18f-43a0-b761-755548bb52cc)
<br/>Image 17: Creating a Metric Filter

</div>

Next I entered the code given under the Filter pattern as shown in the image below. The code specifies that any error code originating from the AWS key management service matching the OutputPattern1ECSTaskRole identified by its arn should generate an alert.

<div align="center">

  ![image](https://github.com/user-attachments/assets/a89b8526-44ab-461f-8a59-64e6de6815d3)
<br/>Image 18: Defined the filter pattern  

</div>

Lastly I completed the configuration by assigning the Assign Metric form the metric namespace **Pattern1Application/KMSSecurity**. This is confirmed by the picture below:

<div align="center">

![image](https://github.com/user-attachments/assets/b5b561e4-53c4-475e-ab08-361cc8158adc)
<br/>Image 19: Specified the Metric namespace 

</div>

### ii) Create the metric alarm

Once the metric filter had been created I had to create the Metric Alarm from this filter. I choose the metric filter I had created and selected the create alarm option as seen in the image below:

<div align="center">

![image](https://github.com/user-attachments/assets/3b8cbbe0-83e3-453d-a618-f1553db7a5df)  
<br/>Image 20: Created the metric alarm 

</div>

Next I changed the name of the metric to KMSsecurityError and set the preriod to 10 seconds as seen in the picture below.

<div align="center">

  ![image](https://github.com/user-attachments/assets/92bcebaf-68b5-43f7-8638-248c75786a4c)
<br/>Image 21: Changed the name of the security filter 

</div>

Next, within the conditions dialog box I set the threshold type as static, the condition as **Greater > threshold** and set the threshold value as 1. This can be confirmed by the image below:

<div align="center">

  ![image](https://github.com/user-attachments/assets/8bc9288a-f738-439e-be25-dabc1f3b8215)
<br/>Image 22: Conditions for the metric 

</div>

Under the additional configuration settings I set the number of datapoints within the evaluation period that must be breached as 1 out of one so that an form of data breach can be picked up. I also set that mising data should be ignored as seen by the diagram below:

<div align="center">

  ![image](https://github.com/user-attachments/assets/446994c8-e854-43d6-a3a2-3d0b8e0a2380)
<br/>Image 23: Additional configuration 

</div>

Next, in the notification dialogue I configured the Select In alarm as the Alarm trigger state. I created a new topic and set pattern1-logging-topic as the topic name as seen in the below photograph.

<div align="center">

  ![image](https://github.com/user-attachments/assets/621440c5-52f2-4a0a-ac08-db35d0150ca5)
<br/>Image 24: Notification dialogue settings 

</div>

After completing the configuration and creating the alarm I received an email and confirmed the subscription to the topic a seen in the image below:

<div align="center">

  ![image](https://github.com/user-attachments/assets/24550556-cda8-4fe8-9cd2-a55f2758d3ff)
<br/>Image 25: Topic subscription confirmed

</div>

##  Test the workload functionality

At this point I had to test the workload. I did this by running the decrypt API call to the application. This will trigger a failed decrypt event which should result in the alarm being triggered and an SNS notification sent to the email address specified.

### i) Initiate a successful decryption operation

I initiated a successful decryption as shown below:

<div align="center">

  ![image](https://github.com/user-attachments/assets/1e525707-758a-4d5a-821d-ff63b65ffb5b)
<br/>Image 26: Initiated a successful decryption 

</div>

### ii) Initiate an unsuccessful decryption

Next I initiated Multiple unsuccessful decryption as shown below in order to invoke the alert as shown below:

<div align="center">

  ![image](https://github.com/user-attachments/assets/095b0cfd-ec89-4833-9fd9-baab21619719)
<br/>Image 27: Initiated an unsuccesful description 

</div>

I then received an email notification triggered by the cloudwatch alarm as shown below:

<div align="center">

![image](https://github.com/user-attachments/assets/636989e2-13fa-45e8-863b-2d8a68ca1d68)  
<br/>Image 28: Email alarm recieved 

</div>

I clicked the URL in the email and saw an activity change as shown in the email below. The alarm updated from insufficient data to in alarm. The alarm did not update to alarm OK since I sent multiple wrong decryptions as confirmed by the picture below:

<div align="center">

  ![image](https://github.com/user-attachments/assets/dbd4dca3-504f-43d4-a2f7-e08d92ced4a2)
<br/>Image 29: Activity state

</div>

#  Conclusion

In this lab, we demonstrated how to monitor the Key Management Service (KMS) for encryption and decryption activities, detect anomalies, and respond accordingly. By leveraging AWS CloudTrail for capturing API events, Amazon CloudWatch for logging and metric filtering, and Amazon Simple Notification Service (SNS) for alerting, we established a comprehensive monitoring solution. The lab provided both CloudFormation templates and manual steps to set up the necessary infrastructure, enabling flexibility in deployment. Through these tools and techniques, administrators can maintain a high level of security and responsiveness, ensuring the integrity and confidentiality of their applications in the cloud.
 
