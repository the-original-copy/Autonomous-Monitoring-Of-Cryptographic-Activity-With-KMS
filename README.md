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

## 2.2 Configure the ECS repository and deploy the application stack

## 2.3 Configure CloudTrail

## 2.4 Configure the workload and logging alarm

## 2.5 Test the workload functionality

# 3. Conclusion

 
