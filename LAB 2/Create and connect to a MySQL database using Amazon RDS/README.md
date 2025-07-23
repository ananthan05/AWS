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

