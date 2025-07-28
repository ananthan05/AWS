# Objective

- Create an AWS KMS customer managed key to encrypt and decrypt data at rest.

- Store an encrypted object in an S3 bucket by using an encryption key.

- Attempt public access and signed access to an encrypted S3 object.

- Monitor encryption key usage by using the CloudTrail event history.

- Encrypt the root volume of an existing Amazon Elastic Compute Cloud (Amazon EC2) instance.

- Disable and re-enable an AWS KMS key and observe the effects on data access.

# 🔐 Task 1: Creating an AWS KMS Key

In this task, you will create a **Customer Managed AWS KMS key**. This key will later be used to encrypt and decrypt **data keys**, which will in turn protect data in **Amazon S3** and **EBS volumes**.

---

## 🛠️ Step-by-Step Instructions

### 1. Open the Key Management Service (KMS) Console
- In the **AWS Management Console**, use the search bar at the top and search for:  
  `KMS`
- Select **Key Management Service**.

---

### 2. Navigate to Customer Managed Keys
- In the left navigation pane, click **Customer managed keys**.

<img width="1914" height="838" alt="image" src="https://github.com/user-attachments/assets/c1dcc87c-45c3-4cfc-ad32-8fffee3f5e28" />


> 💡 **Tip**: If you don't see the navigation pane, click the ☰ **menu icon** in the top-left corner.

---

### 3. Create a New Key
- Click **Create key**.
- For **Key type**, select: `Symmetric`

<img width="1907" height="782" alt="image" src="https://github.com/user-attachments/assets/12ffdf23-2df0-4860-b7e5-6b876408454c" />

  - 🔒 Symmetric keys **never leave AWS KMS unencrypted**.
  - This creates a **256-bit secret key** managed by KMS.
- Click **Next**.

---

### 4. Set an Alias for the Key
- Under **Alias**, enter:  

MyKMSKey

<img width="1887" height="835" alt="image" src="https://github.com/user-attachments/assets/ccaf0a2d-332a-42f4-b861-ec7321486a07" />

- Click **Next**.

---

### 5. Define Key Administrators
- In the **Key administrators** section, search for and select the role:  

voclabs

<img width="1916" height="812" alt="image" src="https://github.com/user-attachments/assets/778b7bca-41a9-44aa-be92-8916aa3543d3" />

- Click **Next**.

> 👤 Key administrators can manage the KMS key, but not use it to encrypt/decrypt data.

---

### 6. Define Key Usage Permissions
- In the **Key users** section, search for and select the role:  

voclabs

<img width="1908" height="753" alt="image" src="https://github.com/user-attachments/assets/85216d5a-8604-49e3-9ff5-b884173d5624" />

- Click **Next**.

> 🔑 Key users can use the key to **encrypt and decrypt** data.

---

### 7. Review and Create
- Review all the settings.
- At the bottom of the **Review** page, click **Finish**.

<img width="1773" height="808" alt="image" src="https://github.com/user-attachments/assets/12c3b3d9-3a44-4af8-94a7-67b6e81a4444" />


✅ Your key should now appear under **Customer managed keys**.

> 🕒 **Note**: If you’ve previously created KMS keys and scheduled them for deletion, you might still see those keys in the list with status `Pending deletion`. AWS enforces a 7-day minimum waiting period for deletion.

---

## 🎉 You Did It!
You successfully created a **customer-managed AWS KMS key**. This key will be used in the next tasks to manage data encryption for services like **S3** and **EC2**.




---



