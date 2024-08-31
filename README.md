# Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS

# Table of Contents
1 [Introduction](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#1-introduction) </br>
2 [Methodology](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#2-methodology) </br>
&nbsp; 2.1 [Deploy the base lab infrastructure](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#21-deploy-the-base-lab-infrastructure) </br>
&nbsp; 2.2 [Configure the ECS repository and deploy the application stack](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#22-configure-the-ecs-repository-and-deploy-the-application-stack) </br>
&nbsp; 2.3 [Configure CloudTrail](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#23-configure-cloudtrail) </br>
&nbsp; 2.4 [Configure the workload and logging alarm](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#24-configure-the-workload-and-logging-alarm) </br>
&nbsp; 2.5 [Test the workload functionality](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#25-test-the-workload-functionality) </br>
3 [Conclusion](https://github.com/the-original-copy/Autonomous-Monitoring-Of-Cryptographic-Activity-With-KMS/blob/main/README.md#3-conclusion)

# 1. Introduction

Ensuring a robust security posture in cloud-based applications requires the capability to trace and automatically respond to security events. By diligently monitoring key
activity metrics, architects can identify potentially malicious behavior early on, allowing for timely and appropriate responses. This proactive approach, paired with effective alerting systems, creates an autonomous feedback loop that keeps cloud administrators informed and able to mitigate risks before they escalate into serious incidents. This repository explores the integration of AWS CloudTrail, Amazon CloudWatch, and other services to enhance security monitoring and automated responses within an AWS environment.

# 2. Methodology

In order to achieve the intended goal of autonomous monitoring of cyrtographic activities with KMS I would have to accomplish the following tasks:
1. Deploy the base lab infrastructure
2. Configure the ECS repository and deploy the application stack
3. Configure CloudTrail
4. Configure the workload and logging alarm
5. Test the workload functionality

## 2.1 Deploy the base lab infrastructure

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

## 2.2 Configure the ECS repository and deploy the application stack

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



## 2.3 Configure CloudTrail

## 2.4 Configure the workload and logging alarm

## 2.5 Test the workload functionality

# 3. Conclusion

 
