# Objective

- Examine security groups to determine what traffic is allowed.
- Change which security groups are applied to EC2 instances.
- Create new security groups.
- Update the inbound rules on security groups to follow the principle of least privilege.
- Understand how security groups can reference other security groups.
- Configure a network access control list (ACL) to block traffic on a specific TCP port.
- Connect to an instance in a private subnet by using SSH.
- Connect to an instance in a private subnet by using AWS Systems Manager Session Manager.
---

## 🧭 Task 1: Analyzing the VPC and Private Subnet Resource Settings

In this task, you will explore the existing **LabVPC** network setup, understand its subnets, routing, and security configurations, and rename a route table for clarity.

---

### 🛰️ Step 1: Explore the Lab VPC and Subnets
1. Open the **Amazon VPC console** from the **AWS Management Console**.
2. In the left navigation pane, choose **Your VPCs**.

#### Observations:
- Two VPCs:
  - **Default VPC** → `172.31.0.0/16`
  - ✅ **LabVPC** → `10.0.0.0/16` (Used in this lab)

<img width="1918" height="703" alt="image" src="https://github.com/user-attachments/assets/4f2afd88-f017-41d5-a5c8-760df71b0f91" />


3. In the left navigation pane, choose **Subnets**.
4. Sort subnets by **VPC** column.
5. ✅ **LabVPC** has the following subnets:
   - `PrivateSubnet` → (e.g., `10.0.3.0/24`)
   - `PublicSubnetA` → (e.g., `10.0.1.0/24`)
   - `PublicSubnetB` → (e.g., `10.0.2.0/24`)

<img width="1054" height="238" alt="image" src="https://github.com/user-attachments/assets/d36ee479-9a2d-4107-bcd6-98b83d0ad3f2" />

---

### 📍 Step 2: Inspect Routing of PrivateSubnet
1. Select the **PrivateSubnet**, then go to the **Route Table** tab.
2. Click the **route table ID link**.

<img width="1696" height="822" alt="image" src="https://github.com/user-attachments/assets/944eb56f-cffa-4791-8139-a4b6fc9367b1" />

3. In the **Routes** tab:
   - ✅ `10.0.0.0/16` → Local
   - ✅ `0.0.0.0/0` → NAT Gateway

<img width="1687" height="722" alt="image" src="https://github.com/user-attachments/assets/bdeeaeb5-01aa-4ae1-9ddf-33d3f4519d1f" />

> 💡 Since `0.0.0.0/0` points to a **NAT Gateway**, this confirms `PrivateSubnet` is **private**.

4. Go to the **Tags** tab → click **Manage tags**.

<img width="1682" height="745" alt="image" src="https://github.com/user-attachments/assets/33828b6f-6e65-4a22-ab93-ae66ed74c466" />

   - Rename the tag from `changeme` to `Private`.

<img width="1845" height="602" alt="image" src="https://github.com/user-attachments/assets/6fe93acb-97a2-43ad-9819-f9852f6e269f" />

   - Click **Save**.

---

### 🌐 Step 3: Inspect NAT Gateway
1. From the **Routes** tab of the route table, click the **nat-xxxxxx** link.
<img width="1742" height="690" alt="image" src="https://github.com/user-attachments/assets/8421a0f7-6102-48d1-8496-9e2cd7a90f83" />

2. On the **Details** tab:
   - ✅ NAT Gateway resides in **PublicSubnetA**.
<img width="1916" height="648" alt="image" src="https://github.com/user-attachments/assets/1edfc025-88aa-4d86-aa49-6d34ee735ec1" />

#### 🧠 Explanation:
- EC2 instances in `PrivateSubnet` **do not have public IPs**.
- They **access the internet** via the **NAT Gateway** in `PublicSubnetA`.
- Internet responses go to the NAT Gateway, which **forwards responses** to the private EC2 instances.

---

### 🖥️ Step 4: Analyze AppServer EC2 Instance
1. Open the **Amazon EC2 console** (right-click > open in a new tab).
2. In the left pane, select **Instances**.
3. Select the instance named **AppServer**.

#### On the **Details** tab:
- ✅ Located in `PrivateSubnet`
- ✅ Has **private IP only** (no public IP)

<img width="1918" height="852" alt="image" src="https://github.com/user-attachments/assets/8a1439ba-ce44-40e6-a504-ce81f2fd60bf" />

> 🔐 Private IP ensures it is **not directly reachable from the internet**.

---

### 🔒 Step 5: Analyze Security Group
1. Go to the **Security** tab of the AppServer instance.
2. Click the linked security group (e.g., `AppServerSG`).

<img width="1712" height="811" alt="image" src="https://github.com/user-attachments/assets/c3f87dc8-e675-469a-a24e-568e41a836dc" />

<img width="1696" height="685" alt="image" src="https://github.com/user-attachments/assets/30637840-6eed-421f-a089-1ac61c164d28" />

<img width="1698" height="618" alt="image" src="https://github.com/user-attachments/assets/add28ceb-b9fa-46c1-84e9-3b6c09cb3b2a" />

#### Inbound Rules:
- ✅ Allow: TCP port **80 (HTTP)** from `0.0.0.0/0` (anywhere)

#### Outbound Rules:
- ✅ All outbound traffic is **allowed** (default setting)

> 🔁 **Security groups are stateful**:
- Outbound requests automatically allow the **inbound responses**.

---

✅ **Outcome**: You explored and analyzed the LabVPC, identified subnet roles, examined route tables, NAT gateway functionality, and the AppServer EC2 instance’s security configuration.


# 📘 Task 2: Analyzing the Public Subnet Resource Settings

In this task, you will analyze the **networking**, **routing**, and **security group configurations** for the EC2 instances in the public subnets of the LabVPC.

---

## 🔍 Step 1: Inspect Route Table for `PublicSubnetA`

1. Open the **Amazon VPC console**.
2. Navigate to **Subnets** from the left navigation pane.
3. Select `PublicSubnetA`.

<img width="1911" height="857" alt="image" src="https://github.com/user-attachments/assets/dabc4555-2358-4102-ac5c-ebf99d63e499" />


   > ⚠️ Tip: If multiple subnets are selected, the details tabs may be hidden. Deselect other subnets if necessary.

4. Click the **Route table** tab.

5. Verify that there is a route for:
Destination: 0.0.0.0/0
Target: igw-xxxxx (Internet Gateway)


6. Click on the **`igw-xxxxx`** link to open the Internet Gateway details.
7. Under the **Details** tab, confirm that the Internet Gateway is **attached to LabVPC**.

<img width="1918" height="816" alt="image" src="https://github.com/user-attachments/assets/94b07c27-d5b6-4a8d-832c-2629a1643f6b" />

---

## 🖥️ Step 2: Review ProxyServer1 in PublicSubnetA

1. Open the **Amazon EC2 console**.
2. Navigate to **Instances**.
3. Select **`ProxyServer1`**.
4. In the **Details** tab, confirm:
- The instance is running in `PublicSubnetA`.
- It has a **public IPv4 address**.

<img width="1918" height="858" alt="image" src="https://github.com/user-attachments/assets/44d2cd2d-c854-42c2-ac47-eb67ec80a614" />

5. Go to the **Security** tab.
6. Verify the associated security group is **`ProxySG`**.
7. Under **Inbound rules**, confirm:

<img width="1917" height="797" alt="image" src="https://github.com/user-attachments/assets/1e5d3b92-58c4-4fde-a70d-ff863d987edf" />

Type: HTTP (TCP port 80)
Source: Anywhere-IPv4 (0.0.0.0/0)


---

## 🖥️ Step 3: Review ProxyServer2 in PublicSubnetB

1. In the **EC2 console**, select **`ProxyServer2`**.
2. Confirm:
- The instance is in `PublicSubnetB`.
- It has a **public IPv4 address**.
<img width="1903" height="796" alt="image" src="https://github.com/user-attachments/assets/e7bc5a78-0ab1-4296-80cd-2498e5b6a24d" />

3. Go to the **Security** tab.
4. Verify the associated security group is **`ProxySG2`**.

<img width="1912" height="812" alt="image" src="https://github.com/user-attachments/assets/39430df0-838d-42e5-b264-4c7479819264" />

---

## 🔐 Step 4: Modify Inbound Rules for ProxySG2

1. Click on the **`ProxySG2`** security group link.
2. Select the **Inbound rules** tab.
3. Click **Edit inbound rules**.
4. Click **Add Rule** and configure:

| Type | Protocol | Port | Source          |
|------|----------|------|-----------------|
| HTTP | TCP      | 80   | Anywhere-IPv4   |

<img width="1910" height="762" alt="image" src="https://github.com/user-attachments/assets/dceb5a21-3149-4797-9788-911e249faa39" />


5. Click **Save rules**.

> ✅ Now both `ProxySG` and `ProxySG2` allow inbound HTTP (port 80) traffic from any IPv4 address.

---

## 🧠 Notes

- Each **security group** controls traffic **per instance or network interface**, enabling **granular control** over access.
- Associating a security group to an instance binds it to the **Elastic Network Interface (ENI)** of that instance.
- Instances can have **multiple ENIs**, each with different security groups for custom access behavior.

---

## 📈 Outcome

You have:
- Verified that **public subnets** route traffic to an **internet gateway**.
- Confirmed **EC2 instances** in public subnets have **public IPs**.
- Ensured that **security groups** are configured to allow **HTTP access** for both public-facing instances.

---

# 🌐 Task 3: Testing HTTP Connectivity from Public EC2 Instances

In this task, you will verify that the **ProxyServer1** and **ProxyServer2** instances, both in **public subnets**, can access the web server running on the **AppServer** in the **private subnet** over HTTP.

---

## 🗂️ Background

- **ProxyServer1** and **ProxyServer2** are in **public subnets** with **public IPv4 addresses**.
- **AppServer** is in a **private subnet** without public IP access.
- Both proxy servers are configured to **forward TCP port 80 traffic** to **AppServer**.
- All involved security groups allow **inbound HTTP (TCP 80)** from all IPv4 addresses.

---

## 🔗 Step 1: Test HTTP Access via ProxyServer1

1. Open the **Amazon EC2 Console**.
2. In the left navigation pane, choose **Instances**.
3. Select the **`ProxyServer1`** instance.
4. Under the **Details** tab, copy the **Public IPv4 address**.
5. Open a **new browser tab** and paste the IP address into the address bar.
6. Press **Enter**.

<img width="1471" height="823" alt="image" src="https://github.com/user-attachments/assets/7421e29f-a850-4346-81ef-78ec397f3df6" />


✅ **Expected Result**: A webpage loads that is hosted on the **AppServer**, confirming port forwarding from ProxyServer1 to the AppServer is working.

---

## 🔗 Step 2: Test HTTP Access via ProxyServer2

1. In the EC2 Console, select the **`ProxyServer2`** instance.
2. Under the **Details** tab, copy its **Public IPv4 address**.
3. Open a **new browser tab** and paste the IP address into the address bar.
4. Press **Enter**.

<img width="1425" height="746" alt="image" src="https://github.com/user-attachments/assets/992c1b5d-c100-4991-8a43-cfd7d127748c" />

✅ **Expected Result**: The **same webpage** as ProxyServer1 appears, confirming that ProxyServer2 also correctly forwards HTTP traffic to the **AppServer**.

---

## 🧹 Step 3: Cleanup

- **Close** both browser tabs where the AppServer webpage is open.
- Keep your **Amazon EC2** and **Amazon VPC** console tabs **open** for the next task.

---

## 🧠 Notes

- This test validates **public → private subnet communication** via **proxy forwarding**.
- All traffic forwarding happens over **TCP port 80**.
- **Security groups** must be configured correctly for this to succeed:
  - `ProxySG` and `ProxySG2`: allow HTTP from all sources.
  - `AppServerSG`: allows HTTP traffic from internal proxy instances.

---

## ✅ Outcome

You have:
- Verified HTTP connectivity from both public proxy servers to the AppServer.
- Confirmed that both proxies successfully forward TCP 80 traffic to a private subnet.

---

# 🔒 Task 4: Restricting HTTP Access by Using an IP Address

In this task, you will enhance security by restricting access to the **AppServer** so that only **ProxyServer1** can forward HTTP requests to it. You will do this by modifying the **AppServerSG** security group to allow HTTP traffic **only from ProxyServer1’s private IP**.

---

## 🧾 Prerequisites

You must have completed:
- Task 3: Verified that both ProxyServer1 and ProxyServer2 could forward traffic to AppServer.
- You should have access to the **Amazon EC2** or **Amazon VPC** console.

---

## 📥 Step 1: Copy ProxyServer1’s Private IP Address

1. Go to the **AWS Details** section of your lab environment.
2. Copy the value of **ProxyServer1PrivateIP** (should begin with `10.0.1.`).
3. Keep it ready for the next step.

<img width="1895" height="790" alt="image" src="https://github.com/user-attachments/assets/38da72da-9611-448f-b370-acddfb4319a6" />


---

## 🛡️ Step 2: Modify AppServerSG Inbound Rules

1. Open the **Amazon EC2 Console**.
2. In the navigation pane, choose **Security Groups**.
3. Select the **`AppServerSG`** security group.
4. Go to the **Inbound rules** tab and click **Edit inbound rules**.
5. Modify the existing HTTP rule:
   - Delete the current rule with **Source: 0.0.0.0/0**.
   - Add a new rule:
     - **Type**: HTTP
     - **Protocol**: TCP
     - **Port Range**: 80
     - **Source**: `<ProxyServer1PrivateIP>/32` (e.g., `10.0.1.12/32`)

<img width="1882" height="705" alt="image" src="https://github.com/user-attachments/assets/8da49dd3-7b69-4fcb-a955-acba4c2405ba" />

6. Click **Save rules**.

✅ This restricts HTTP access to **only ProxyServer1**.

---

## 🌐 Step 3: Test Access from ProxyServer1

1. From **AWS Details**, copy the **ProxyServer1PublicIP**.
2. Open a new browser tab and paste the IP address.
3. Press **Enter** to load the application.

<img width="1667" height="772" alt="image" src="https://github.com/user-attachments/assets/8905d456-0356-434e-b0e9-9ccc42bd6df4" />

✅ **Expected Result**: The AppServer’s webpage loads successfully.

---

## 🚫 Step 4: Test Access from ProxyServer2 (Should Fail)

1. From **AWS Details**, copy the **ProxyServer2PublicIP**.
2. Open a new browser tab and paste the IP address.
3. Press **Enter**.

<img width="1617" height="810" alt="image" src="https://github.com/user-attachments/assets/609f3036-e4f6-410c-bee1-1129107bc26d" />

❌ **Expected Result**: The webpage **does not load**. After a delay, a **timeout error** should occur.

> 💡 If it does load, perform a **hard refresh** (Ctrl+Shift+R or Cmd+Shift+R) to clear browser cache.

---

## 🧹 Step 5: Cleanup

- Close both browser tabs opened in this task.
- Keep the **Amazon EC2** and **Amazon VPC** console tabs open for use in the next task.

---

## 🧠 Summary

In this task, you:
- Restricted HTTP traffic to AppServer to **a single internal IP** (ProxyServer1).
- Verified successful access from **ProxyServer1**.
- Verified that **ProxyServer2** was blocked.
- Prepared for the next step: **security group-based access control**.

---
# 🔄 Task 5: Scaling Restricted HTTP Access by Referencing a Security Group

In this task, you will simplify access control to the **AppServer** by referencing a security group (SG) instead of hardcoding IP addresses. This allows you to easily grant HTTP access to any future proxy servers by assigning them the same SG.

---

## 🛡️ Step 1: Modify AppServerSG to Use ProxySG as Source

1. Open the **Amazon EC2 Console**.
2. In the navigation pane, choose **Security Groups**.
3. Select the **`AppServerSG`** security group.
4. Go to the **Inbound rules** tab and click **Edit inbound rules**.
5. Delete the existing rule that uses ProxyServer1’s IP address.
6. Click **Add rule** and configure:
   - **Type**: HTTP
   - **Protocol**: TCP
   - **Port Range**: 80
   - **Source**: Custom
   - In the source box, type `sg`, and select the **`ProxySG`** security group from the dropdown.
7. Click **Save rules**.

<img width="1887" height="638" alt="image" src="https://github.com/user-attachments/assets/1e0fd65e-6a15-49d6-8b70-bfe7f8bdbac6" />


✅ Now any instance associated with **ProxySG** can access the AppServer.

---

## 🔁 Step 2: Associate ProxyServer2 with ProxySG

1. In the **EC2 Console**, go to **Instances**.
2. Select **ProxyServer2**.
3. In the top-right corner, choose **Actions > Security > Change security groups**.
4. Remove the existing **ProxySG2** security group.
5. In the search bar, find and **select `ProxySG`**.
6. Click **Add security group**, then **Save**.

<img width="1918" height="632" alt="image" src="https://github.com/user-attachments/assets/abb68f05-12e2-4562-9f9e-62912af1403f" />


✅ ProxyServer2 is now in the same SG as ProxyServer1.

---

## 🌐 Step 3: Test Access from ProxyServer1

1. From your lab’s **AWS Details**, copy the **ProxyServer1PublicIP**.
2. Paste it into a new browser tab.
3. Press **Enter**.

<img width="1711" height="713" alt="image" src="https://github.com/user-attachments/assets/efc61709-f495-4eef-ac92-f6cd4574a4ef" />

✅ **Expected Result**: The AppServer’s webpage loads.

---

## 🌐 Step 4: Test Access from ProxyServer2

1. From **AWS Details**, copy the **ProxyServer2PublicIP**.
2. Paste it into a new browser tab.
3. Press **Enter**.

<img width="1557" height="696" alt="image" src="https://github.com/user-attachments/assets/e6400bdd-5683-4673-98db-c04f469d018f" />


✅ **Expected Result**: The AppServer’s webpage also loads.

> 💡 If it doesn’t load, perform a **hard refresh** (Ctrl+Shift+R or Cmd+Shift+R).

---

## 🧹 Step 5: Cleanup

- Close the two browser tabs with the AppServer webpage.
- Keep **EC2** and **VPC** consoles open for the next task.

---

## 🧠 Summary

In this task, you:
- Used a **security group as a source** instead of static IPs to allow HTTP access.
- Updated **AppServerSG** to reference **ProxySG**.
- Reconfigured **ProxyServer2** to use **ProxySG**.
- Successfully verified access from both ProxyServer1 and ProxyServer2.

✅ This approach is scalable and secure — perfect for dynamic infrastructure!

---

# 🚫 Task 6: Restricting HTTP Access Using a Network ACL

In this task, you’ll enforce access control using a **Network ACL (NACL)**. Unlike security groups, NACLs operate at the subnet level and support **explicit allow and deny** rules, evaluated in numerical order.

---

## 🌐 Step 1: Deny HTTP Access via NACL

1. Open the **Amazon VPC Console**.
2. In the navigation pane, select **Network ACLs**.
3. Choose the **network ACL associated with `LabVPC`**.

<img width="1918" height="860" alt="image" src="https://github.com/user-attachments/assets/f911b9be-38dc-48b7-b98c-11e70921c9b0" />

4. Go to the **Inbound rules** tab and click **Edit inbound rules**.
5. Click **Add new rule** and configure:
   - **Rule number**: `99`
   - **Type**: `HTTP`
   - **Protocol**: `TCP` (auto-filled with HTTP)
   - **Port Range**: `80`
   - **Source**: `0.0.0.0/0`
   - **Allow/Deny**: `Deny`

<img width="1918" height="582" alt="image" src="https://github.com/user-attachments/assets/52d67064-caf6-4f12-8439-0843966cd95d" />

6. Click **Save changes**.

🔒 This rule blocks **all HTTP access** to your VPC.

---

## 🧪 Step 2: Test Website Access (Should Fail)

1. Open the **AWS Details** panel.
2. Copy the **ProxyServer1PublicIP** value.
3. Open a new browser tab and paste the IP.

<img width="1570" height="832" alt="image" src="https://github.com/user-attachments/assets/5164501f-13e1-454f-8df7-bb2c944a87c5" />

❌ **Expected Result**: Page fails to load or times out.

> 💡 Note: If the page loads, perform a **hard refresh** (`Ctrl+Shift+R` or `Cmd+Shift+R`).

**🔍 Analysis**:
Even though the security group allows HTTP (port 80) traffic, the **deny rule in the NACL takes precedence**, blocking all access.

---

## 🔓 Step 3: Add an Allow Rule to Override Deny

1. With the **LabVPC NACL still selected**, click **Edit inbound rules**.
2. Click **Add new rule** and configure:
   - **Rule number**: `98` _(lower than 99)_
   - **Type**: `HTTP`
   - **Protocol**: `TCP`
   - **Port Range**: `80`
   - **Source**: `0.0.0.0/0`
   - **Allow/Deny**: `Allow`
3. Click **Save changes**.

<img width="1918" height="748" alt="image" src="https://github.com/user-attachments/assets/9ea96905-07ae-41da-ab2e-a62a6b2b01b0" />

✅ The `Allow` rule will now match first due to the lower rule number.

---

## 🔁 Step 4: Re-Test Website Access

1. Paste the **ProxyServer1PublicIP** in a new browser tab again.
2. Press **Enter**.

<img width="1622" height="617" alt="image" src="https://github.com/user-attachments/assets/4a785677-c674-45f2-a984-04bd7034671a" />

✅ **Expected Result**: AppServer website loads successfully.

---

## 🧠 Key Takeaways

- **NACL rules are evaluated in ascending order of rule number**.
- The first matching rule is applied — no further rules are evaluated.
- A **lower-numbered "Allow" rule** can override a **higher-numbered "Deny" rule**.
- Both **security groups and NACLs must allow traffic** for it to pass.

---

## 🧹 Cleanup

- Close the AppServer browser tab.
- Keep VPC and EC2 consoles open for the next task.

---

# Task 7: Connecting to the AppServer by Using a Bastion Host and SSH

In this task, we connect to an EC2 instance in a **private subnet** using a **bastion host** (jump server) located in a **public subnet**, leveraging **SSH Agent Forwarding** for secure access.

---

## 🛠️ Step 1: Rename EC2 Instance

1. Go to **EC2 Console > Instances**.
2. Select `ProxyServer2`.
3. Edit the **Name** tag and rename it to:  
`Bastion`

<img width="1918" height="586" alt="image" src="https://github.com/user-attachments/assets/563886dd-f8f0-4e55-81b4-a2af5a4e92f4" />

---

## 🛡️ Step 2: Create BastionSG Security Group

1. Navigate to **Security Groups** > **Create Security Group**.
2. Configure:
- **Security group name**: `BastionSG`
- **Description**: `BastionSG`
- **VPC**: Select `LabVPC`
3. Add Inbound Rule:
- **Type**: SSH
- **Source**: Anywhere-IPv4 (`0.0.0.0/0`)
4. Click **Create security group**.

<img width="1867" height="797" alt="image" src="https://github.com/user-attachments/assets/06ba1747-a3b2-4434-9050-0b8928912ee0" />

---

## 🔐 Step 3: Attach BastionSG to Bastion Instance

1. Go to **EC2 > Instances > Bastion**.
2. Choose **Actions > Security > Change security groups**.
3. Remove `ProxySG`, add `BastionSG`, and click **Save**.

<img width="1908" height="715" alt="image" src="https://github.com/user-attachments/assets/d67533f9-4ddd-40e9-ac4e-32b488ef2514" />


---

## 🔐 Step 4: Allow SSH to AppServer from Bastion

1. Get the `BastionPrivateIP` from **AWS Details**.
2. Go to **Security Groups**, select `AppServerSG`.
3. **Edit Inbound Rules**:
- **Type**: SSH
- **Source**: `BastionPrivateIP/32`
4. Click **Save rules**.

<img width="1887" height="742" alt="image" src="https://github.com/user-attachments/assets/ae82112e-99ae-4cd2-9dd3-8caa892ef7c3" />


---

## 🖥️ Step 5: Connect to Bastion Host

In the terminal provided:

```bash
exec ssh-agent bash
ssh-add ~/.ssh/labsuser.pem
```

<img width="946" height="107" alt="image" src="https://github.com/user-attachments/assets/c6037194-59c1-4ac6-8957-3005eb87277c" />

Then connect to the Bastion host (replace IP):
```bash
ssh -i ~/.ssh/labsuser.pem -A ec2-user@<BastionPublicIP>
```
---
<img width="917" height="425" alt="image" src="https://github.com/user-attachments/assets/57955e3c-e597-411d-a882-d4260d911762" />

##  Step 6: SSH to AppServer (Private Subnet)
From the bastion shell:

```bash
ssh ec2-user@<AppServerPrivateIP>
```
When prompted, type yes.

<img width="722" height="313" alt="image" src="https://github.com/user-attachments/assets/a4843eeb-65bd-4f84-896d-566c8dcad8c4" />

Once connected:

```bash
touch newfile.txt
```
<img width="677" height="97" alt="image" src="https://github.com/user-attachments/assets/f7958929-f3d9-4397-b984-ca12e396c405" />

This proves successful access to the private AppServer.

🧪 Verification & Exit
The prompt should say ec2-user@appserver.

Run:

```bash
exit  # Exit AppServer
exit  # Exit Bastion
```


<img width="512" height="136" alt="image" src="https://github.com/user-attachments/assets/0d53b21d-6204-4230-adf4-4f0d76adbafb" />

Now you're back on the external Ubuntu terminal.

📘 Summary
✅ We used SSH Agent Forwarding to avoid copying private keys into the bastion.
✅ The AppServerSG only allows SSH from BastionPrivateIP.
✅ This is a secure and auditable method to access private resources in AWS.



# 🔒 Task 8: Connecting Directly to a Host in a Private Subnet Using Session Manager

In this task, you will connect to the **AppServer** instance located in a **private subnet**—**without using a bastion host or port 22 (SSH)**. You’ll use **AWS Systems Manager Session Manager**, which provides a secure and auditable shell connection without any network exposure.

---

## ⚙️ Step 1: Use Session Manager to Access AppServer

1. Open the **Amazon EC2 Console**.
2. In the left navigation pane, choose **Instances**.
3. Select the instance named **AppServer**.
4. Click **Connect** in the upper-right corner.
5. In the pop-up:
   - Select the **Session Manager** tab.
   - Click **Connect**.

🖥️ A new browser tab opens and you're now connected to the AppServer terminal:

```bash
sh-4.2$
```
<img width="1918" height="787" alt="image" src="https://github.com/user-attachments/assets/a0d03e77-ef3c-48ec-b7f9-00c96da78466" />


✅ You’ve connected securely using Session Manager without needing SSH access.

💡 Note: Port 22 is still blocked via the NACL — this proves Session Manager requires no SSH!

## Step 2: Modify the Webpage on AppServer
In the Session Manager terminal connected to AppServer, run the following command:

```bash
sudo sed -i 's/instance!/instance! Session manager was used to edit this file./g' /var/www/html/index.html
```

<img width="1918" height="366" alt="image" src="https://github.com/user-attachments/assets/f7b8b322-c1fa-40be-83ab-456230ae0fe6" />

✅ This command modifies the homepage of the website hosted on the AppServer.

## Step 3: Test the Website Using the ProxyServer Public IP

Open the AWS Details panel.

Copy the value for ProxyServer1PublicIP.

Open a new browser tab and paste the IP address.

✅ Expected Result: The webpage loads and now says:

<img width="1833" height="766" alt="image" src="https://github.com/user-attachments/assets/7c6a50c7-d019-40ca-87e6-89ea9227580d" />

"Session manager was used to edit this file."

💡 If the old version appears, perform a hard refresh:
Ctrl + Shift + R (Windows) or Cmd + Shift + R (Mac).


Submit Your Work
