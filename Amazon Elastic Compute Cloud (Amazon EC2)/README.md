# EC2 Setup Objective

- Create and configure an Amazon EC2 instance in the public cloud.
- Attach an EBS volume to the instance for persistent storage.
- Launch additional EC2 instances and install a basic web application.

# Accessing the AWS Management Console in module 6(Lab3)

## Lab Instructions

1. **Start the Lab**
   - At the top of these instructions, click **Start Lab**.
   - This action will initiate your lab session.

2. **Session Timer**
   - A timer will appear at the top of the page showing the time remaining for your session.
   - ⚠️ **Tip**: You can **refresh the session length** by clicking **Start Lab** again **before the timer reaches 0:00**.

3. **Wait for Readiness**
   - Before proceeding further, **wait until the circle icon** (located next to the **AWS** link in the upper-left corner) **turns green**.
   - This indicates that the AWS environment is ready.

---

✅ Once the circle is green, you can safely access the AWS Management Console and begin your lab tasks.

<img width="1917" height="798" alt="image" src="https://github.com/user-attachments/assets/0ba7554b-9eb0-4473-b49f-c4f4446d8e7f" />

# Task 1: Launch Your Amazon EC2 Instance

In this task, you will launch an Amazon EC2 instance with termination protection and stop protection. Termination protection prevents you from accidentally terminating the EC2 instance and stop protection prevents you from accidentally stopping the EC2 instance. You will also specify a User Data script when you launch the instance that will deploy a simple web server.

 

In the AWS Management Console choose  Services, choose Compute and then choose EC2.

<img width="1916" height="867" alt="image" src="https://github.com/user-attachments/assets/d6c335e8-9970-46a8-978f-0816ed2c60c5" />

Note: Verify that your EC2 console is currently managing resources in the N. Virginia (us-east-1) region.  You can verify this by looking at the drop down menu at the top of the screen, to the left of your username. If it does not already indicate N. Virginia, choose the N. Virginia region from the region menu before proceeding to the next step.

 

Choose the Launch instance  menu and select Launch instance.

<img width="1901" height="822" alt="image" src="https://github.com/user-attachments/assets/0f0ef663-51d2-4dd1-94ca-3304653c517f" />
---

## Step 1: Name and tags
Give the instance the name Web Server.

The Name you give this instance will be stored as a tag. Tags enable you to categorize your AWS resources in different ways, for example, by purpose, owner, or environment. This is useful when you have many resources of the same type — you can quickly identify a specific resource based on the tags you have assigned to it. Each tag consists of a Key and a Value, both of which you define. You can define multiple tags to associate with the instance if you want to.

<img width="1692" height="677" alt="image" src="https://github.com/user-attachments/assets/1a7f5d30-594c-4001-b0bd-6a14b34e264b" />

In this case, the tag that will be created will consist of a key called Name with a value of Web Server

---

## Step 2: Application and OS Images (Amazon Machine Image)
In the list of available Quick Start AMIs, keep the default Amazon Linux AMI selected.

Also keep the default Amazon Linux 2023 AMI selected.

<img width="1357" height="732" alt="image" src="https://github.com/user-attachments/assets/692f21ea-a98c-40bd-81a7-7e6722d9d1a3" />

An Amazon Machine Image (AMI) provides the information required to launch an instance, which is a virtual server in the cloud. An AMI includes:

- A template for the root volume for the instance (for example, an operating system or an application server with applications)
- Launch permissions that control which AWS accounts can use the AMI to launch instances
- A block device mapping that specifies the volumes to attach to the instance when it is launched

The Quick Start list contains the most commonly-used AMIs. You can also create your own AMI or select an AMI from the AWS Marketplace, an online store where you can sell or buy software that runs on AWS.

---

## Step 3: Instance type
In the Instance type panel, keep the default `t2.micro` selected.

Amazon EC2 provides a wide selection of instance types optimized to fit different use cases. Instance types comprise varying combinations of CPU, memory, storage, and networking capacity and give you the flexibility to choose the appropriate mix of resources for your applications. Each instance type includes one or more instance sizes, allowing you to scale your resources to the requirements of your target workload.

The t2.micro instance type has 1 virtual CPU and 1 GiB of memory. 

Note: You may be restricted from using other instance types in this lab.

---

## Step 4: Key pair (login)
For Key pair name - required, choose `vockey`.

Amazon EC2 uses public–key cryptography to encrypt and decrypt login information. To ensure you will be able to log in to the guest OS of the instance you create, you identify an existing key pair or create a new key pair when launching the instance. Amazon EC2 then installs the key on the guest OS when the instance is launched.  That way, when you attempt to login to the instance and you provide the private key, you will be authorized to connect to the instance.

<img width="1271" height="575" alt="image" src="https://github.com/user-attachments/assets/2b1d150c-e23f-4a0e-aed6-ac1fa854162f" />

Note: In this lab you will not actually use the key pair you have specified to log into your instance.

---

## Step 5: Network settings
Next to Network settings, choose Edit.

For VPC, select Lab VPC.

The Lab VPC was created using an AWS CloudFormation template during the setup process of your lab. This VPC includes two public subnets in two different Availability Zones.

Note: Keep the default subnet PublicSubnet1. This is the subnet in which the instance will run. Notice also that by default, the instance will be assigned a public IP address.

Under Firewall (security groups), choose  Create security group and configure:

- **Security group name**: `Web Server security group`
- **Description**: `Security group for my web server`

A security group acts as a virtual firewall that controls the traffic for one or more instances. When you launch an instance, you associate one or more security groups with the instance. You add rules to each security group that allow traffic to or from its associated instances. You can modify the rules for a security group at any time; the new rules are automatically applied to all instances that are associated with the security group.

Under Inbound security group rules, notice that one rule exists. `Remove this rule.`

<img width="1642" height="671" alt="image" src="https://github.com/user-attachments/assets/0867b826-0638-4127-b248-876bfff817e5" />


---

## Step 6: Configure storage
In the Configure storage section, keep the default settings.

Amazon EC2 stores data on a network-attached virtual disk called Elastic Block Store.

You will launch the Amazon EC2 instance using a default 8 GiB disk volume. This will be your root volume (also known as a 'boot' volume).

---

## Step 7: Advanced details
Expand  Advanced details.

For Termination protection, select Enable.

When an Amazon EC2 instance is no longer required, it can be terminated, which means that the instance is deleted and its resources are released. A terminated instance cannot be accessed again and the data that was on it cannot be recovered. If you want to prevent the instance from being accidentally terminated, you can enable termination protection for the instance, which prevents it from being terminated as long as this setting remains enabled.

<img width="1227" height="697" alt="image" src="https://github.com/user-attachments/assets/158ba765-8996-41a8-88ad-16a8360b6803" />

Scroll to the bottom of the page and then copy and paste the code shown below into the User data box:

```bash
#!/bin/bash
dnf install -y httpd
systemctl enable httpd
systemctl start httpd
echo '<html><h1>Hello From Your Web Server!</h1></html>' > /var/www/html/index.html
```

<img width="1791" height="697" alt="image" src="https://github.com/user-attachments/assets/3b9af53d-8808-48d7-9d6c-1cd64524fcf8" />


When you launch an instance, you can pass user data to the instance that can be used to perform automated installation and configuration tasks after the instance starts.

Your instance is running Amazon Linux 2023. The shell script you have specified will run as the root guest OS user when the instance starts. The script will:

- Install an Apache web server (httpd)
- Configure the web server to automatically start on boot
- Run the Web server once it has finished installing
- Create a simple web page

---

## Step 8: Launch the instance

At the bottom of the Summary panel choose Launch instance

You will see a Success message.


Choose View all instances

<img width="1912" height="822" alt="image" src="https://github.com/user-attachments/assets/b5d8b9b2-1924-4152-97e9-b4e09d712e81" />

In the Instances list, select  Web Server. 

Review the information displayed in the Details tab. It includes information about the instance type, security settings and network settings.

<img width="1910" height="813" alt="image" src="https://github.com/user-attachments/assets/b75429e1-83aa-4075-92c2-57487c9f3aec" />

The instance is assigned a Public IPv4 DNS that you can use to contact the instance from the Internet.

To view more information, drag the window divider upwards.

At first, the instance will appear in a Pending state, which means it is being launched. It will then change to Initializing, and finally to Running.

 
Wait for your instance to display the following:

- **Instance State:**  Running  
- **Status Checks:**   2/2 checks passed


 <img width="1915" height="826" alt="image" src="https://github.com/user-attachments/assets/15f5093b-94f4-4b5c-be18-f1a1793a1b03" />


🎉 **Congratulations!** You have successfully launched your first Amazon EC2 instance.

# Task 2: Monitor Your Instance

Monitoring is an important part of maintaining the reliability, availability, and performance of your Amazon Elastic Compute Cloud (Amazon EC2) instances and your AWS solutions.

  
In the Actions  menu towards the top of the console, select Monitor and troubleshoot  Get system log.

<img width="1903" height="777" alt="image" src="https://github.com/user-attachments/assets/29cce218-f466-48c5-bfdf-1a0604d6f560" />

The System Log displays the console output of the instance, which is a valuable tool for problem diagnosis. It is especially useful for troubleshooting kernel problems and service configuration issues that could cause an instance to terminate or become unreachable before its SSH daemon can be started. If you do not see a system log, wait a few minutes and then try again.

 -Scroll through the output and note that the HTTP package was installed from the user data that you added when you created the instance.

<img width="1918" height="861" alt="image" src="https://github.com/user-attachments/assets/9ee6c2f3-2937-49ec-91f9-48536abca2ff" />

Choose `Cancel`.

Ensure Web Server is still selected. Then, in the Actions  menu, select Monitor and troubleshoot  Get instance screenshot.

<img width="1918" height="855" alt="image" src="https://github.com/user-attachments/assets/6a46450c-d4db-49a1-a83b-635100697a33" />

This shows you what your Amazon EC2 instance console would look like if a screen were attached to it.

<img width="1897" height="832" alt="image" src="https://github.com/user-attachments/assets/d264d2c7-8041-43af-9cea-d133e2251779" />

Choose Cancel.

 - Congratulations! You have explored several ways to monitor your instance.

# Task 3: Update Your Security Group and Access the Web Server

When you launched the EC2 instance, you provided a script that installed a web server and created a simple web page. In this task, you will access content from the web server.

---

## Step 1: Get Your Instance's Public IP

- Ensure **Web Server** is still selected.  
- Choose the **Details** tab.
- Copy the **Public IPv4 address** of your instance to your clipboard.

<img width="1912" height="858" alt="image" src="https://github.com/user-attachments/assets/d3fa844c-2f89-46bf-899d-13e88b24e7e9" />

---

## Step 2: Test Access in Browser

- Open a new tab in your web browser.
- Paste the IP address you just copied, then press **Enter**.

<img width="1911" height="893" alt="image" src="https://github.com/user-attachments/assets/4740d781-dd8c-455b-8f53-105ce806864b" />


### ❓ Question: Are you able to access your web server? Why not?

You are **not currently able to access your web server** because the security group is **not permitting inbound traffic on port 80**, which is used for HTTP web requests.  
This is a demonstration of using a **security group as a firewall** to restrict the network traffic that is allowed in and out of an instance.

To correct this, you will now update the security group to permit web traffic on port 80.

---

## Step 3: Update the Security Group




- Keep the browser tab open, but return to the **EC2 Console** tab.
- In the left navigation pane, choose **Security Groups**.
- Select **Web Server security group**.
- Choose the **Inbound rules** tab.

<img width="1917" height="837" alt="image" src="https://github.com/user-attachments/assets/1fbf502a-9cc4-4425-8e64-3ef166787a9a" />

The security group currently has **no inbound rules**.

- Choose **Edit inbound rules**
- Select **Add rule**, and configure:
  - **Type:** HTTP  
  - **Source:** Anywhere-IPv4
- Choose **Save rules**

<img width="1877" height="616" alt="image" src="https://github.com/user-attachments/assets/484a5f69-c7bb-4358-b42c-1c1168517126" />

---

## Step 4: Refresh and Confirm

- Return to the web server tab you previously opened.
- Refresh the page.

You should see the message:

```html
Hello From Your Web Server!
```
<img width="1918" height="887" alt="image" src="https://github.com/user-attachments/assets/56d217f5-cc76-4a7d-9685-b5750d76fe36" />


Congratulations! You have successfully modified your security group to permit HTTP traffic into your Amazon EC2 Instance.

---

# Task 4: Resize Your Instance: Instance Type and EBS Volume

As your needs change, you might find that your instance is over-utilized (too small) or under-utilized (too large). If so, you can change the instance type. For example, if a t2.micro instance is too small for its workload, you can change it to an m5.medium instance. Similarly, you can change the size of a disk.

---

## Step 1: Stop Your Instance

Before you can resize an instance, you must stop it.

> When you stop an instance, it is shut down. There is no runtime charge for a stopped EC2 instance, but the storage charge for attached Amazon EBS volumes remains.

- On the EC2 Management Console, in the left navigation pane, choose **Instances** and then select the **Web Server** instance.
- In the **Instance state** menu, select **Stop instance**.
- Choose **Stop**.

Your instance will perform a normal shutdown and then will stop running.

<img width="1698" height="740" alt="image" src="https://github.com/user-attachments/assets/bbe4cb1f-9050-4680-a587-755404b5263f" />

Wait for the **Instance state** to display: `Stopped`.

<img width="1671" height="473" alt="image" src="https://github.com/user-attachments/assets/c217b7a9-3d0d-4f5d-bacf-9aa1d66d9129" />

---

## Step 2: Change The Instance Type and Enable Stop Protection

- Select the **Web Server** instance.
- In the **Actions** menu, select **Instance settings > Change instance type**, then configure:

<img width="1687" height="783" alt="image" src="https://github.com/user-attachments/assets/99957217-0c96-49ec-821b-6fec7f2647d1" />

  - **Instance Type**: `t2.small`
- Choose **Apply**
<img width="1111" height="703" alt="image" src="https://github.com/user-attachments/assets/a08bc04d-d9ab-4016-a006-0fb5b96cf0d6" />

> When the instance is started again it will run as a t2.small, which has twice as much memory as a t2.micro instance.  
> **NOTE**: You may be restricted from using other instance types in this lab.

- Select the **Web Server** instance.
- In the **Actions** menu, select **Instance settings > Change stop protection**.
- Select **Enable** and then choose **Save**.

<img width="1910" height="623" alt="image" src="https://github.com/user-attachments/assets/6a64eb7c-274a-4054-bc15-51950188847a" />


> When you stop an instance, the instance shuts down. When you later start the instance, it is typically migrated to a new underlying host computer and assigned a new public IPv4 address.  
> An instance retains its assigned private IPv4 address. When you stop an instance, it is not deleted.  
> Any EBS volumes and the data on those volumes are retained.

---

## Step 3: Resize the EBS Volume

- With the **Web Server** instance still selected, choose the **Storage** tab.
- Select the name of the **Volume ID**, then select the checkbox next to the volume that displays.
<img width="1662" height="682" alt="image" src="https://github.com/user-attachments/assets/48c92575-e496-43a0-9f08-9c23c7bc5d8a" />

- In the **Actions** menu, select **Modify volume**.
<img width="1687" height="598" alt="image" src="https://github.com/user-attachments/assets/3c50f447-ca70-483b-9579-0d2307097038" />

> The disk volume currently has a size of 8 GiB. You will now increase the size of this disk.

- Change the size to: `10`  
  > **NOTE**: You may be restricted from creating Amazon EBS volumes larger than 10 GB in this lab.

- Choose **Modify**

<img width="1902" height="733" alt="image" src="https://github.com/user-attachments/assets/07de36c4-dd68-4ac5-95f8-9929ef5bbd37" />

- Choose **Modify** again to confirm and increase the size of the volume.

<img width="1687" height="741" alt="image" src="https://github.com/user-attachments/assets/273893d2-94e1-4a55-8d91-66fda929aacf" />

---

## Step 4: Start the Resized Instance

You will now start the instance again, which will now have more memory and more disk space.

- In the left navigation pane, choose **Instances**.
- Select the **Web Server** instance.
- In the **Instance state** menu, select **Start instance**.

<img width="1917" height="863" alt="image" src="https://github.com/user-attachments/assets/93b1f77b-70ac-4872-b1a9-628fbfd089db" />


🎉 **Congratulations!** You have successfully resized your Amazon EC2 Instance.  
In this task you changed your instance type from `t2.micro` to `t2.small`.  
You also modified your root disk volume from `8 GiB` to `10 GiB`.

---

# Task 5: Explore EC2 Limits

Amazon EC2 provides different resources that you can use. These resources include images, instances, volumes, and snapshots. When you create an AWS account, there are default limits on these resources on a per-region basis.

---

## Step 1: Access Service Quotas

- In the AWS Management Console, in the search box next to **Services**, search for and choose **Service Quotas**.

<img width="1918" height="856" alt="image" src="https://github.com/user-attachments/assets/7a4a5343-d770-41b7-a794-77dd0e048ac2" />


---

## Step 2: Navigate to EC2 Quotas

- Choose **AWS services** from the navigation menu.
- In the **AWS services - Find services** search bar, search for `ec2` and choose **Amazon Elastic Compute Cloud (Amazon EC2)**.

<img width="1915" height="852" alt="image" src="https://github.com/user-attachments/assets/c53d4f16-f8e6-4962-9c41-b1c2ce54e0ef" />

<img width="1913" height="820" alt="image" src="https://github.com/user-attachments/assets/855f675f-8bee-4ba9-91ca-b5386eb4cb30" />

---

## Step 3: Explore EC2 Limits

- In the **Find quotas** search bar, search for `running on-demand`, but **do not make a selection**.
- Instead, **observe the filtered list** of service quotas that match the criteria.

<img width="1892" height="760" alt="image" src="https://github.com/user-attachments/assets/c35cdb49-89a7-4978-bce2-a8dadc4df796" />

> Notice that there are limits on the number and types of instances that can run in a region.  
> For example, there is a limit on the number of **Running On-Demand Standard...** instances that you can launch in this region.  
> When launching instances, the request must not cause your usage to exceed the instance limits currently defined in that region.

> If you are the AWS account owner, you can **request an increase** for many of these limits.

---

# Task 6: Test Stop Protection

You can stop your instance when you do not need to access but you would still like to retain it. In this task, you will learn how to use stop protection.

---

## Step 1: Return to the EC2 Console

- In the AWS Management Console, in the search box next to **Services**, search for and choose **EC2** to return to the EC2 console.

---

## Step 2: Attempt to Stop the Instance

- In the left navigation pane, choose **Instances**.
- Select the **Web Server** instance and in the **Instance state** menu, select **Stop instance**.
- Then choose **Stop**.

> Note that there is a message that says:  
> `Failed to stop the instance i-1234567xxx. The instance 'i-1234567xxx' may not be stopped. Modify its 'disableApiStop' instance attribute and try again.`


<img width="1688" height="733" alt="image" src="https://github.com/user-attachments/assets/d4072515-1894-4fda-b406-92f5a8ba8be1" />


This shows that the **stop protection** that you enabled earlier in this lab is now providing a safeguard to prevent the accidental stopping of an instance.

---

## Step 3: Disable Stop Protection

- In the **Actions** menu, select **Instance settings > Change stop protection**.
- Remove the check next to **Enable**.

<img width="1902" height="723" alt="image" src="https://github.com/user-attachments/assets/328b0672-fcd1-4517-ab3b-1fedc8b39600" />

- Choose **Save**.

---

## Step 4: Stop the Instance

- Select the **Web Server** instance again.
- In the **Instance state** menu, select **Stop instance**.
- Choose **Stop**.


<img width="1831" height="640" alt="image" src="https://github.com/user-attachments/assets/888b4506-408b-45ac-b93b-1c981f037488" />


🎉 **Congratulations!** You have successfully tested stop protection and stopped your instance.

---

### Submit your work and the lab will grade you.
