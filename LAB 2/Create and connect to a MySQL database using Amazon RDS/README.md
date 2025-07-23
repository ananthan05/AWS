 # Objective

- Launch an Amazon RDS DB instance with high availability.

- Configure the DB instance to permit connections from your web server.

- Open a web application and interact with your database.


##  Build Your DB Server and Interact With Your DB Using an App in module 8 (LAB5)

---

## 🛠️ Task 1: Create a Security Group for the RDS DB Instance

In this task, you will create a security group that allows your EC2 Web Server to access the RDS database instance.

### 🔹 Step-by-Step Instructions:

1. In the **AWS Management Console**, search for and select **VPC**.
2. In the left navigation pane, choose **Security groups**.

<img width="1912" height="867" alt="image" src="https://github.com/user-attachments/assets/0ad36b0f-9e4d-4e6f-8d31-160af9178e3d" />

3. Click **Create security group** and configure the following:
   - **Security group name**: `DB Security Group`
   - **Description**: `Permit access from Web Security Group`
   - **VPC**: Select `Lab VPC` (Click the ❌ on the default selection and choose `Lab VPC` from the list)

4. Under the **Inbound rules** pane, click **Add rule**:
   - **Type**: `MySQL/Aurora (3306)`
   - **Source**: In the field, type `sg`, then select `Web Security Group` from the dropdown.

<img width="1892" height="783" alt="image" src="https://github.com/user-attachments/assets/678e309b-44ad-41c0-9d7d-da27dae664ab" />

   ✅ This will allow inbound MySQL traffic only from EC2 instances in the Web Security Group.

5. Click **Create security group**.

### ✅ Outcome:

<img width="1907" height="790" alt="image" src="https://github.com/user-attachments/assets/63421fdf-898b-4c7f-bfdc-bf789ba406de" />

You’ve successfully created a security group that allows traffic on port **3306 (MySQL)** from your web server. This group will be attached to your RDS instance in upcoming steps.

---

## 🛠️ Task 2: Create a DB Subnet Group

In this task, you will create a **DB Subnet Group** to define which subnets Amazon RDS can use. RDS requires at least two subnets in different Availability Zones for high availability.

### 🔹 Step-by-Step Instructions:

1. In the **AWS Management Console**, search for and select **Aurora and RDS**.

2. In the left navigation pane, click **Subnet groups**.
   > ℹ️ If the menu is collapsed, click the **☰ (menu icon)** in the upper-left corner.

<img width="1905" height="806" alt="image" src="https://github.com/user-attachments/assets/a1eb4636-b17b-4313-8043-a67e88de1361" />

3. Click **Create DB Subnet Group** and configure the following:
   - **Name**: `DB-Subnet-Group`
   - **Description**: `DB Subnet Group`
   - **VPC**: Select `Lab VPC`



4. Scroll down to **Add subnets** section:
   - Under **Availability Zones**, select:
     - `us-east-1a`
     - `us-east-1b`
   - Under **Subnets**, select the subnets with:
     - `10.0.1.0/24`
     - `10.0.3.0/24`

<img width="1877" height="822" alt="image" src="https://github.com/user-attachments/assets/b48bbeee-dd79-4710-a06a-0c4225d90ebf" />

   ✅ These subnets will now appear in the **Subnets selected** table.

5. Click **Create**.

### ✅ Outcome:

<img width="1917" height="867" alt="image" src="https://github.com/user-attachments/assets/529b94d8-42bf-4798-975d-6e259a8a42f7" />

You’ve successfully created a **DB Subnet Group** with two subnets across different Availability Zones. This will be used when launching your RDS instance in the next task.

---

## 🛠️ Task 3: Create an Amazon RDS DB Instance

In this task, you will launch a **Multi-AZ MySQL** database instance using **Amazon RDS**, which ensures high availability by deploying the primary DB instance with a standby in a different Availability Zone.

### 🔹 Step-by-Step Instructions:

1. In the **RDS Console**, select **Databases** from the left navigation pane.

2. Click **Create database**.
   > ℹ️ If you see **"Switch to the new database creation flow"**, click it.
<img width="1905" height="848" alt="image" src="https://github.com/user-attachments/assets/d8583865-4d31-4e1d-8e22-290a68d0621f" />

---

### 🧱 Engine Options:
- **Engine type**: `MySQL`
- **Template**: `Dev/Test`

<img width="1893" height="793" alt="image" src="https://github.com/user-attachments/assets/39a5f66f-e385-4b7b-94ef-4852199db6bb" />

---

### 🛡️ Availability & Durability:
- Choose: `Multi-AZ DB instance`

<img width="1817" height="782" alt="image" src="https://github.com/user-attachments/assets/99ab1a86-c43a-4a2b-9a81-59622e2615de" />

---

### ⚙️ Settings:
- **DB instance identifier**: `lab-db`
- **Master username**: `main`
- **Master password**: `lab-password`
- **Confirm password**: `lab-password`

<img width="1606" height="676" alt="image" src="https://github.com/user-attachments/assets/2e7b202b-dda2-49ca-ba0a-5942ed782dca" />

---

### 📦 DB Instance Class:
- **Class type**: `Burstable classes (includes t classes)`
- **Instance type**: `db.t3.micro`
<img width="1401" height="377" alt="image" src="https://github.com/user-attachments/assets/94bbaf7e-777b-46b6-b0b5-6facfff25cc2" />

---

### 💾 Storage:
- **Storage type**: `General Purpose (SSD)`
- **Allocated storage**: `20 GiB`

<img width="1656" height="558" alt="image" src="https://github.com/user-attachments/assets/0e9eebe4-e5ac-48d9-94f7-3a1493303ed6" />

---

### 🌐 Connectivity:
- **VPC**: `Lab VPC`
- **Existing VPC security groups**: 
  - ✅ Select: `DB Security Group`
  - ❌ Deselect: `default`

<img width="1918" height="832" alt="image" src="https://github.com/user-attachments/assets/ff364079-c938-4321-82b6-3013bb636784" />

---

### 📊 Monitoring:
- Expand **Additional configuration**
- ❌ Uncheck: `Enable Enhanced monitoring`
<img width="1487" height="385" alt="image" src="https://github.com/user-attachments/assets/ddd813db-dd61-4771-9b6f-f6609f623b25" />


---

### 🔧 Additional Configuration:
- **Initial database name**: `lab`
- ❌ Uncheck: `Enable automatic backups`
- ❌ Uncheck: `Enable encryption`

<img width="1487" height="697" alt="image" src="https://github.com/user-attachments/assets/eab2d414-434b-427f-bc64-a4d44697048d" />

---

### 🟢 Launch:
- Click **Create database**

> ⚠️ **Note**: If you receive a `iam:CreateRole` error, ensure `Enhanced Monitoring` is disabled.

<img width="1916" height="778" alt="image" src="https://github.com/user-attachments/assets/c17d115a-1edf-4404-ae88-aaddd85fc6dd" />

---

### 🔍 Post-Creation:
- Click on the **lab-db** database link.
- Wait ~4 minutes for the status to change to **Modifying** or **Available**.

<img width="1662" height="512" alt="image" src="https://github.com/user-attachments/assets/3da5bf23-bd09-4df4-8f35-52e4102b681c" />

### 📡 Retrieve Endpoint:
- Scroll to **Connectivity & security**
- Copy the **Endpoint** (e.g., `lab-db.xxxxx.us-east-1.rds.amazonaws.com`)

<img width="1642" height="747" alt="image" src="https://github.com/user-attachments/assets/a3e53c72-cb92-4b42-8f73-a644f0801c2d" />

- 📋 Paste it into a text editor for later use.

`lab-db.cuz2lnxsn3oh.us-east-1.rds.amazonaws.com`

✅ **Outcome**: You have successfully launched a Multi-AZ Amazon RDS MySQL database instance.

---


## 🌐 Task 4: Interact with Your Database

In this task, you will configure a web application hosted on an EC2 instance to connect to your Amazon RDS database. The application will use the database for storing and managing contact information.

---

### 🔍 Step 1: Get Web Server IP Address
- From the **AWS Details** dropdown above the lab instructions, copy the **WebServer IP address**.

<img width="1918" height="862" alt="image" src="https://github.com/user-attachments/assets/49df6ec9-ba6e-4004-bbd1-2c1250ea367b" />


---

### 🌐 Step 2: Access the Web Application
- Open a **new browser tab**.
- Paste the **WebServer IP address** into the address bar and press **Enter**.
- The **web application homepage** should load, showing EC2 instance details.

---

### 🔗 Step 3: Connect to RDS
- Click the **RDS** link at the top of the page.
- Fill in the following fields:

  | Field     | Value                                  |
  |-----------|----------------------------------------|
  | Endpoint  | *Paste the RDS Endpoint you saved*     |
  | Database  | `lab`                                  |
  | Username  | `main`                                 |
  | Password  | `lab-password`                         |

- Click **Submit**.

> 🟢 A message should appear confirming the connection and data population.

---

### 📒 Step 4: Use the Address Book
- You should now see the **Address Book** application.
- Test the app by:
  - Adding a new contact
  - Editing a contact
  - Deleting a contact

✅ All data is being stored in the RDS database and **replicated across Availability Zones** thanks to Multi-AZ configuration.

---

### 🎉 Final Step: Submit Your Work
- Submit your lab as instructed to complete the task.

---

✅ **Outcome**: You successfully connected a web application to an Amazon RDS Multi-AZ MySQL instance and verified persistent database interaction.


