# Performance testing 
## What Is Performance Testing?
Performance testing is a series of tests that can be used to see how your application responds to particular scenarios. The tests mimic the production environment as closely as possible.  
Types of performance tests:
- Stress test
  - tests how your application handles high numbers of users over long periods of time
- Load test
  - tests behaviour of app under normal conditions and peak conditions
- Spike test
  - tests how the app behaves when faced with a large number of requests over a short period of time or at once
- Soak test
  - tests how the app handles long hours of operation under normal conditions

## Prerequisites For Using Gatling
### Java
- https://devwithus.com/install-java-windows-10/ (download the installer)
- Java is the language behind Gatling, so we will need it installed before we can run any Gatling tests
### Gatling
- https://gatling.io/get-started/ (download normal one, not enterprise)
- Gatling is a tool used for performance testing, and we'll go into more detail about how to run these tests soon
### IntelliJ
- https://www.jetbrains.com/idea/download/#section=windows (download community version)
- IntelliJ is the Integrated Development Environment (IDE) that we'll be using for this project
### Scala
- Scala is the language that the tests will be written in, so we will need this package in order to be able to run the test scripts
#### Setting up testing environment
- In IntelliJ (with admin permissions), navigate to plugins: (`CTRL+Alt+S`) -> `install scala plugin`

### Running the test with node app
1. Make sure the app is running  
- ssh into app machine, add DB_HOST environment variable, and npm start
2. Open the page in the browser  
3. Record visiting the posts page  
- Right-click, then click `Inspect`
- Click `Network`, then click the `Clear` icon (a circle with a line through it)
- Click the `Record` button (beside it), then navigate to the /posts page
- Click the `Download` button in the `Network` tab
- Save the HAR file
4. Record visiting the fibonacci page  
- As in step 3
5. Record going back to the home page  
- As in step 3
6. Convert the HAR files to Scala files using recorder.bat  
- In IntelliJ terminal, `cd ~/gatling-charts-highcharts-bundle-3.6.1-bundle/gatling-charts-highcharts-bundle-3.6.1/bin`
- `./recorder.bat`
- In the top right-hand corner of the page that comes up, click `HAR converter`
- Click `Browse`, and select your newly-saved HAR file
- Give it a `Class Name`, then click `Start`
7. Run each one  
- `./gatling.bat`
- Select the number that corresponds with the class name for your new HAR file
- Give it a description
- When the test completes, copy the html file path it outputs at the end, and paste it into the browser -> this displays the various metrics related to how the test performed

![test-with-1-user](https://user-images.githubusercontent.com/88166874/134039241-bd3b7413-0272-4828-89e3-c7647b2d1670.PNG)  

8. Run each one with 10% more visitors  
- Increase the number in the following line (at the end of the .scala file) to increase the number of users:  

![line-for-increasing-users](https://user-images.githubusercontent.com/88166874/134322063-04ce6edd-badb-4c79-a3ff-4aee5cc9052d.PNG)

- And enjoy watching them fail!  

![test-with-10-users](https://user-images.githubusercontent.com/88166874/134039282-5b46276b-bc19-40ec-954b-c2895f42dee6.PNG)  

![test-with-100-users](https://user-images.githubusercontent.com/88166874/134039316-92a9bdb9-7559-47bb-b55e-e67ece3f3b26.PNG)  

![test-with-1000-users](https://user-images.githubusercontent.com/88166874/134039329-b5322dca-76ed-4f2b-8d58-3a6b307c8448.PNG)

### Load Balancer With Test Users Diagram
![load-balancer-img](https://user-images.githubusercontent.com/88166874/134197989-d46a62fb-4552-4671-9bea-9604c7caec84.png)
- In order to handle the number of users making requests as you increase the number of users, the auto scaling group will create new instances, and the load balancer can then redirect users to different instances so that no one instance is overloaded

# CloudWatch

## How CloudWatch Connects To Our Current Setup

![241556504_353893073182767_944237784430600497_n](https://user-images.githubusercontent.com/88166874/134310351-092e0bac-cfe5-47f7-9cc1-773410570554.jpg)
*find the final main.tf file in the [sre_terraform repo](https://github.com/am93596/sre_terraform)*

### What are we monitoring?
- CPU utilisation
- Network In
- RequestCountPerTarget
- Network Monitoring
- Application Performance Monitoring
- Third-party resources

### AWS CloudWatch Dashboard
1. Go to `CloudWatch` -> `Dashboards` -> `Create dashboard`  
2. Name the dashboard, and click `Create dashboard`  
3. Select a widget type to configure (e.g. `Line`), then click `Next`  
4. Select your data source (e.g. `Metrics`), and click `Configure`
5. In `All metrics` tab, select `EC2` -> `By Auto Scaling Group`. Select one of the metrics associated with your auto scaling group (e.g. `CPU utilisation`). Then click `Create widget`  
6. Once you are happy, click `Save dashboard` to save your changes

### What is the difference between scaling out and scaling up?
- Scaling up means to use a more powerful server that can handle more requests, for example moving from a t2.micro instance to a t2.medium instance
- Scaling out means to spin up new instances in line with the specified scaling policies

### Scale Out and Scale In Policies
- Here is an example of a scale out policy in a terraform file. This one tracks the average CPU utilisation, and scales up by 1 instance when this goes over 3%.

![scale-out-policy](https://user-images.githubusercontent.com/88166874/134338974-61aa82da-6396-441e-9689-df98e3b48526.PNG)

- Here is an example of a scale in policy in a terraform file. It is connected to a CloudWatch alarm that tracks the number of bytes received by the instance on all network interfaces. If the number of bytes drops below 500,000 bytes, the policy terminates 1 instance from the group of instances created by the auto scaling group.

![scale-down-policy](https://user-images.githubusercontent.com/88166874/134340393-0db2060b-2674-4522-bda2-4743c487afae.PNG)

![scale-down-alarm](https://user-images.githubusercontent.com/88166874/134340407-7f080ff9-f51d-4691-b132-e515ce0e3bf6.PNG)

## Simple Notification Service (SNS)
SNS is a service provided by AWS that works in conjunction with CloudWatch to take action and send alerts to you, depending on how you configure it. SNS is set up by creation of a `Topic` and a `Subscription` in the `SNS Dashboard`, as follows:  
1. In the SNS dashboard, click `Topics` -> `Create topic`  
2. Set the `Type` to `Standard`, and give it a name and a display name  
3. Leave the rest as default, and click `Create topic`
4. Next, click on the name of your topic, then scroll down and click `Create subscription`  

![sns-topic-subscription](https://user-images.githubusercontent.com/88166874/134347083-dfa560c1-d1f6-41cf-a897-1ca60247f285.PNG)  

5. Select the protocol (e.g. `Email`), and enter the endpoint (e.g. your email address)  
6. Click `Create subscription`. This will send an email to the address you specified. Click the link in the email to allow subscription so that you can receive emails from SNS later  

### Linking SNS to Alarm
1. In CloudWatch, click `Alarms` -> `All alarms` -> `Create alarm`  
2. In `Metrics` tab, select `EC2` -> `By Auto Scaling Group`. Select one of the metrics associated with your auto scaling group (e.g. `CPU utilisation`). Click `Select metric`.  
3. Set the period to your preferred time (e.g. `1 minute`)  
4. `Threshold type`: `Static`. `Whenever CPUUtilization is...`: select the appropriate one for your alarm (e.g. `Lower/Equal`). `than...`: input your threshold value (e.g. `1` for <=3%). Click `Next`.
5. In the `Notification` section, set the `Alarm state trigger` to `In alarm`, and the `SNS topic` to the one you created earlier. You can also add an Auto Scaling action to trigger one of your ASG policies as follows:
  1. Set the following: `Alarm state trigger`: `In alarm`, `Resource type`: `EC2 Auto Scaling group`, and select your ASG. Then select the policy you want to be carried out.
6. Click `Next`, and give your alarm a name and description. Click `Next`. Then click `Create alarm`.

### SNS Application to Application (A2A)
SNS A2A sends events to applications (e.g. Simple Queue Service (SQS)), so that actions can be taken automatically to react to changes. In the example of SQS, SNS can send messages to a subscribed queue so that the application(s) in the queue can process it.

### SNS Application to Person (A2P)
SNS A2P sends events to people, usually through email or text, allowing you to stay up to date on any changes and actions being taken. For example, you may want to set up an SNS to notify you when a new instance is being spun up, or when a certain percentage of requests are failing.  


![241520686_593063538715923_6621770552432359305_n](https://user-images.githubusercontent.com/88166874/134354311-a663d701-ba13-4aab-af85-0b10a5b29661.jpg)  

This diagram provides an overview of how the SNS application links with the CloudWatch setup and the auto scaling group. In the example, CloudWatch has been set up to track the CPU utilisation, with an alarm threshold set at 75%. If the CPU reaches or goes over this threshold, the alarm is triggered, which in turn triggers the SNS email to your email address, and the auto scaling group to scale out (make a new instance). The load balancer can then start redirecting traffic to the new instance, to reduce the number of users using the busy instance.


### Simple Queue Service (SQS)
SQS is a message queueing service from AWS.
