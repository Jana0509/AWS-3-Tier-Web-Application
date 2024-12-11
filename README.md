# AWS: Build a Secure, Scalable and Resilient 3-tier web application

# 3 TIER APPLICATION:
Imagine you want to build a 3 -tier web application where users hit via a front end, backend should process the request and sends the response back to the user with the correct data. This is how normal application works.

# HOW 3 TIER APPLICATION WORKS ?
when the user wants to access any application, it needs the UI to interact, User Interface is called as presentation layer. Once the user started accessing the UI, application needs to respond to the user according to the business need. So the Business logic operations happens at business layer. Finally, application needs some kind of persistent storage to interact with user, This layer is called as data layer.

**Three Layers are** 
1. Presentation Layer [UI]
2. Business Layer [Business logic]
3. Data Layer [Data]

# Why we need three tier application ?
If you look at the above points, we are separating the application into multiple layer which helps to scales and secure independently and main modularity . These layers are physically separated, with each running on its own infrastructure.

The architecture presented in this article is one of many possible ways to design a three-tier system and is specifically tailored to meet the goals of availability, security, scalability, and performance.

In this article, We will build Secure, Scalable and Resilient 3-tiered Web application in AWS Cloud.

Let's Start building it!

# Network - Amazon VPC :

**What is VPC ?**
VPC [Virtual Private Cloud] is the virtual network isolation environment in AWS. In this Space, you can design and build your own application in a virtual network that you define.

**Why we need it ?**
AWS by default provides the default VPC, but if you are creating the resources or running your workload in default VPC, your application might get access to external world and it's not safe to build the application in default VPC.
In order to protect your resources, you will be building your own network space in cloud and you will be having the control such as inbound and outbound traffic for the VPC. 

# VPC COMPONENTS:

**what is subnets ?**
Subnet is network portion in your VPC, just think VPC is the full pizza and subnet is one slice of complete pizza.

![image](https://github.com/user-attachments/assets/f189cbe7-91d5-4eff-8bd2-1c1515e789eb)


**why we need subnet ?**
We can create the network portion in VPC such as private and public subnets and we can set the rules such as only the resources in the public subnet can access the internet and resources in private subnet shouldn't access it.

**Internet gateway : [IGW]**
It is the one of the AWS component in VPC, with the help of IGW, your VPC can access to the internet. So, without creating and attaching the IGW to VPC, your VPC can't access to the internet world.

**NAT Gateway : [NAT GW]**
It is the one of the AWS component in VPC. NAT GW helps private instance resources to talk to internet. For Ex., Your private instances need to get the patch, in this case your private resources will talk with internet via NAT gateway. NAT Gateway will be present in Public subnet and acts as a proxy for your private instances.

**Final Architecture**
The final architecture plans to be built through this chapter(networking) is as follows.

![image](https://github.com/user-attachments/assets/f3d9a406-eb68-41c0-97b8-224d842f9c0a)

**CREATE VPC :**
1. Go to the AWS Console and search VPC and create the VPC by giving the name and IPV4 CIDR as 10.0.0.0 /16 (65,536 hosts can be created in the CIDR range)

   ![image](https://github.com/user-attachments/assets/66682c6b-8de4-4f81-8689-e75d9b39a074)

2. After creating the VPC, we have to create the subnets [It's the isolation environment in our VPC]
   
3. Go to subnet and click create subnet and provide the values as below

   ![image](https://github.com/user-attachments/assets/254ed27c-ae9f-47bc-bd25-1274dc771bc9)

4. We are going to create 4 subnets, 1 public and private subnet in each AZ, so totally 4 subnets. PFB values for creating the subnets.

5. Create the 4 subnets with the CIDR values as provided above. Make sure to select the correct VPC which you have created.

   ![image](https://github.com/user-attachments/assets/c16816fe-82a3-49a6-b9a9-d0d499663bb3)

![image](https://github.com/user-attachments/assets/a972f4de-d9ef-4acc-912b-e453beba6f0d)

6. After creating the subnets, the subnets will look like below

   ![image](https://github.com/user-attachments/assets/ebdb7c62-e80d-469b-96eb-3b4f0be126af)


# CREATE ROUTE TABLE :
1. Create the private and public route table with the name and select the VPC.
   ![image](https://github.com/user-attachments/assets/7344dc0a-c377-4fdc-93a6-eefd1abdfe43)

![image](https://github.com/user-attachments/assets/d50ab5fe-0c4a-4ee0-ab05-362432fda2a3)

2. Now associate the subnet with the Route table. With the Route table, traffic knows where to go.

   ![image](https://github.com/user-attachments/assets/e5f5b914-9b52-4709-8552-762bb7a4c827)

3. To Associate the public subnet with public route table, check in public route table and click edit subnet associations.

   ![image](https://github.com/user-attachments/assets/36c785b4-5eab-4e96-8715-1aad6681c309)

Follow the same process, to associate the private subnets with private route table.

# CREATE INTERNET GATEWAY:

1. Search for internet gateway[IGW] in AWS Console, give the name and click create.

   ![image](https://github.com/user-attachments/assets/1ab40d20-7c5e-4ac3-99e2-5b00bf1af8a2)

2. Click Attach in the IGW and attach with the VPC which we created.

   ![image](https://github.com/user-attachments/assets/e05d46b5-0b88-4f69-978b-be6a5f6d2397)

# EDIT ROUTES IN ROUTE TABLE:

We are adding the route values to route 0.0.0.0/0 traffic to IGW for the public route table. So only the resources present in public subnet can access the internet.

![image](https://github.com/user-attachments/assets/f78b69e3-7424-4ec5-bd8b-1580961de892)

# CREATE NAT GATEWAY :
If your private instance resources needs to talk to internet, they can't talk directly as there is no route configured for it. For Ex., Your private instances need to get the patch, in this case your private resources will talk with internet via NAT gateway. NAT GW acts as a proxy for your private instances.

1. Search NAT Gateway and click create and assign this nat gateway to one public subnet and attach one elastic IP.

   ![image](https://github.com/user-attachments/assets/1a7091fa-daf1-49ae-8adf-d36c28ed739f)

2. Edit the private route table and add the route as below.

   ![image](https://github.com/user-attachments/assets/145000e8-785d-4d2e-b2c2-14bea62305d7)

This make sure that the private resources can talk to internet via NAT GW and not directly.

# FINAL ARCHITECTURE :

![image](https://github.com/user-attachments/assets/af6563db-f3c8-47e8-a529-9d97d70a708c)


# Launch a web server instance:
Here, we are going to create the EC2 resources using the autoscaling group in the private subnet which we created in VPC section.

**FINAL ARCHITECTURE :**

![image](https://github.com/user-attachments/assets/2dc847e8-212e-490d-8c6e-a41c038f39a7)

The following items are contained in this chapter.
* Launch web server instances and execute user data
* Set up a security group
* Create a custom Amazon Machine Image (AMI)
* Launch an Application Load Balancer (ALB)
* Configure a Launch Template
* Configure an Auto Scaling Group
* Test auto scaling and change manual settings

**LAUNCH THE WEB SERVER INSTANCE :**
1. Create EC2 by providing the name and select the instance type and AMI

   ![image](https://github.com/user-attachments/assets/cf63f62f-d60f-4a48-9ede-efc1ae3cb386)

![image](https://github.com/user-attachments/assets/4188908e-8be3-436f-a6fd-942c54a2c05b)

2. Select the VPC which we created and create the Security group as below.

   ![image](https://github.com/user-attachments/assets/752ebeec-cbc8-48b9-9cb3-8f6820760ba5)

3. Add the Security Group inbound rule to allow your IP as below
   ![image](https://github.com/user-attachments/assets/0ade6eaf-53b4-4119-91d7-274be831eac5)

4.  Go to Advanced and provide the below code in the user data, this user data script will run when the instance is booting up for the first time. It installs the web server and start run when instance started running.

![image](https://github.com/user-attachments/assets/c295b985-53a0-4349-bff6-025b9a8e0521)

# TEST THE INSATNCE :
1. Go the instance and copy the public DNS name and hit it from the browser, you should be getting the below page.

   ![image](https://github.com/user-attachments/assets/4200ac2e-8952-4640-8393-12d14d136053)

![image](https://github.com/user-attachments/assets/2f0db4f2-b827-43a0-a632-aecd6154eb97)

This Confirms that your public instances in the VPC are able to reach the internet and serves the results.

# Create the Custom AMI :
1. Create the Amazon Machine Image[AMI] which is the blue print for your instance configuration such as instance type, AMI, user data.

2. To create, refer the below screenshot.
   ![image](https://github.com/user-attachments/assets/0bdf33e8-b33b-4ac3-ae0e-ca29a783f8eb)

3. Provide the Image name and click create.
   ![image](https://github.com/user-attachments/assets/91cdf579-f89c-4d27-9a27-99e67b5cea4c)

4. Once the AMI is created, you can check the AMI under AMI section.

5. Terminate the EC2 instance which we created previously. 
Note:  Do not terminate the "Web server for custom AMI" Instance until the AMI creation process is fully completed. Ensure the AMI status shows as Available before proceeding.

**ARCHITECTURE SO FAR :**

![image](https://github.com/user-attachments/assets/2ece21c9-573b-4e34-a917-ec6f036e0df7)

# CREATE THE APPLICATION LOAD BALANCER:
Application Load balancer is used to route the traffic to the instances. 

1. Search Application Load balancer and provide the name

   ![image](https://github.com/user-attachments/assets/2baa77b6-0b03-429d-8641-09089cff4511)

2. Add both the public subnet for the VPC

   ![image](https://github.com/user-attachments/assets/1d295798-24a4-485c-bc3e-53103b3979a0)

3. Create the SG for the ALB which allows inbound traffic from everywhere

   ![image](https://github.com/user-attachments/assets/56d8d835-59ea-4898-89d1-16950ad49980)

4. Add the created Security group under Security group of Application Load Balancer.
5. Create the Target group for the ALB.

   ![image](https://github.com/user-attachments/assets/367d0f6d-1751-4620-8083-523a1ab49c8c)

![image](https://github.com/user-attachments/assets/702e9aba-f795-446e-bffb-81845e36452d)

6. This is where we would register our instances. However, as we mentioned earlier, there are not instances to register at this moment. Click Create target group.

   ![image](https://github.com/user-attachments/assets/a2011e44-8f6b-4c80-895b-d6a4189e01bf)

7. Again, move into the Load balancers page, click refresh button and select Web-TG. And then Click Create load balancer.

8. Review and create the ALB
   ![image](https://github.com/user-attachments/assets/aa6b2ea5-376a-44f8-96b5-ede5b3d58508)


# CONFIGURE LAUNCH TEMPLATE :

The launch template configures all parameters within a resource at once, reducing the number of steps required to create an instance. Launch templates make it easier to implement best practices with support for Auto Scaling and spot fleets, as well as spot and on-demand instances. This helps you manage costs more conveniently, improve security, and minimize the risk of deployment errors.

The launch template contains information that Amazon EC2 needs to start an instance, such as AMI and instance type. The Auto Scaling group refers to this and adds new instances when a scaling out event occurs. If you need to change the configuration of the EC2 instance to start in the Auto Scaling group, you can create a new version of the launch template and assign it to the Auto Scaling group. You can also select a specific version of the launch template that you use to start an EC2 instance in the Auto Scaling group, if necessary. You can change this setting at any time.

1. In the EC2 console, select Launch Templates from the left navigation panel. Then click Create Launch Template.
   ![image](https://github.com/user-attachments/assets/1e87c6c1-b7db-482f-9693-9d9e92768dd1)

2. Select the AMI which we have already created. you can find our web AMI under My AMIs
   ![image](https://github.com/user-attachments/assets/29a3775e-b8d6-4408-b236-0e1fd91505cc)

3. Create the Security for your web instances and allow inbound traffic only from ALB security group. So that , only your ALB can talk with Web instances. It adds the security for your public subnet instances.

   ![image](https://github.com/user-attachments/assets/077237a4-b79b-46bf-b395-5240e337c626)

![image](https://github.com/user-attachments/assets/d58aecb9-2329-43f9-9b4d-13810734f023)

---

4. Leave all other settings as default, and click the Create launch template button at the bottom right to create a launch template.

---

# SET AUTO SCALING GROUP :
1. Enter the EC2 console and select Auto Scaling Groups at the bottom of the left navigation panel. Then click the Create Auto Scaling group button to create an Auto Scaling Group. Select the Launch Template which we created in the previous step.

   ![image](https://github.com/user-attachments/assets/eb7d1705-5d78-4f0e-a1aa-a8551d6667cb)

2. Set the network configuration with the Purging options and instance types as default. Choose VPC-Lab-vpc for VPC, select Private subnet 1 and Private subnet 2 for Subnets. When the setup is completed, click the Next button.

   ![image](https://github.com/user-attachments/assets/7c72494f-7cbc-4ca5-9732-99ed73b00c4e)

3. Attach to the ALB which we created

   ![image](https://github.com/user-attachments/assets/3328dbaa-1636-490e-92ee-9f6f4234fc04)

4. In the step of Configure group size and scaling policies, set scaling policy for Auto Scaling Group. In the Group size column, specify Desired capacity as 2 and Minimum capacity as 1 and Maximum capacity as 3.
   
5. In the Scaling policies section, select Target tracking scaling policy and type 30 in Target value. This is a scaling policy for adjusting the number of instances based on the CPU average utilization remaining at 30% overall. Leave all other settings as default and click the Next button in the lower right corner.

   ![image](https://github.com/user-attachments/assets/ee690e4a-0c69-4e73-84b9-1ba84015a2c3)

6. Now we are in the final stage of review. After checking the all settings, click the Create Auto Scaling Group button at the bottom right.

7. Auto Scaling group has been created. You can see the Auto Scaling group created in the Auto Scaling group console as shown below.

   ![image](https://github.com/user-attachments/assets/736bfa88-69cc-41bb-ba0a-c9a0de97c530)

8. Now go to the EC2 instance, you should see that 2 instances are running as we kept as 2 as desired instances.

   ![image](https://github.com/user-attachments/assets/33e9dbd4-7583-4fac-a7f0-42b86deee0ab)

Now, we've built a web service that is high available and automatically scales under load! The configuration of the services we have created so far is as follows.

![image](https://github.com/user-attachments/assets/1f2a2376-d34e-4cae-8b67-149edef2977e)


# Check web service and test:
1. To access through the Application Load Balancer configured for the web service, click the Load Balancers menu in the EC2 console and select the Web-ALB you created earlier. Copy DNS name from the basic configuration.
2. Now, you can see the traffic are going to both the servers, ALB is routing the traffic to both the instances.

   ![image](https://github.com/user-attachments/assets/6bdf8a77-249c-4b59-8f01-76501269e21f)

![image](https://github.com/user-attachments/assets/39a6d8d6-2173-4e3c-95f8-ab91480928a2)



NOTE : We won't be able to access the instances directly using instances DNS name, only the ALB can access the instances. It adds the security here.



**CREATE DATABASE INSTANCE :**
In this section, we will deploy RDS Aurora instance in VPC-Lab and configure the web service(Apache + PHP) in Auto Scaling Group to use RDS Aurora(MySQL). When the connection to the database is completed, create a new version of the existing custom AMI and update the Auto Scaling Group to use the new version of AMI. 

FINAL ARCHITECTURE :
![image](https://github.com/user-attachments/assets/5cf9a00f-986d-490d-b3b7-aa4b4342a94e)

**Create Security Group For DB tier:**
1. Like Web Tier, create SG for DB tier and allow inbound traffic from web security group.

   ![image](https://github.com/user-attachments/assets/62e6c6dd-0d3a-4a87-ac7c-3ea265a63296)

![image](https://github.com/user-attachments/assets/d5b41cc9-efda-4eda-a255-8454f663190d)

2. Under Type, select MySQL/Aurora The port range should default to 3306. The protocol and port ranges are automatically specified.

# CREATE DB :
1. Select Create Database in dashboard to start creating a RDS instance.
2. You want to select the RDS instances' database engine. In Amazon RDS, you can select the database engine based on open source or commercial database engine. In this lab, we will use Amazon Aurora with MySQL-compliant database engine. Select Standard Create in the choose a database creation method section.

![image](https://github.com/user-attachments/assets/c1db412b-9427-4dc0-8bbc-bdf431d80438)

3. Under Settings, we want to specify administrator information for identifying the RDS instances. Enter the information as it appears

   ![image](https://github.com/user-attachments/assets/ab0352c5-6c8f-4b79-b46a-3a7d04b71d32)

4. Select Production in Template. Under Settings, we want to specify administrator information for identifying the RDS instances. Enter the information as it appears

   ![image](https://github.com/user-attachments/assets/580fc228-6087-4372-a2cd-35e173db1f40)

5. Under DB instance size select Memory Optimized class. Under Availability & durability select Create an Aurora Replica or reader node in a different AZ. Select
6. Set up network and security on the Connectivity page. Select the VPC-Lab that you created earlier in the Virtual private cloud (VPC) and specify the subnet that the RDS instance will be placed in, public access, and security groups. Enter the information as it appears below.

   ![image](https://github.com/user-attachments/assets/8fc05ddd-6dbe-480b-a9c3-9e2744dcd2b1)

7. Scroll down and click Additional configuration. Set database options as shown below. Be aware of the uppercase and lowercase letters of Initial database name.

   ![image](https://github.com/user-attachments/assets/084814a7-bf4e-4723-8177-0490a61946a3)

---

**Congratulations!** You successfully created an Amazon Aurora RDS instance. The RDS instance is now available for applications to connect to.

---

# CONNECT TO RDS INSTANCE :
1. Now, we can send the traffic from the web tier to DB tier as we have security group rules allowing traffic from web tier to DB tier.
2. In order to test, you can create the table and insert the data and access the data from the web tier.

Now, with the work done so far, you have built a web service with guaranteed high availability. The infrastructure architecture we have constructed so far is as follows.

![image](https://github.com/user-attachments/assets/a0777b9a-b970-4842-9b99-08f42f0ade1d)


**CONGRATULATIONS!** You have successfully completed designing and running 3 tier web application using AWS.

**Remember, don't forget to delete all resources created and configured when you are done following the steps of this article.**

If you've got this far, thanks for reading! I hope it was worthwhile to you.

Signing off!
Jana
