# Objective

- Create an AWS KMS customer managed key to encrypt and decrypt data at rest.

- Store an encrypted object in an S3 bucket by using an encryption key.

- Attempt public access and signed access to an encrypted S3 object.

- Monitor encryption key usage by using the CloudTrail event history.

- Encrypt the root volume of an existing Amazon Elastic Compute Cloud (Amazon EC2) instance.

- Disable and re-enable an AWS KMS key and observe the effects on data access.

# Task 1: Creating an AWS KMS Key

In this task, you will create a **Customer Managed AWS KMS key**. This key will later be used to encrypt and decrypt **data keys**, which will in turn protect data in **Amazon S3** and **EBS volumes**.

---

##  Step-by-Step Instructions

### 1. Open the Key Management Service (KMS) Console
- In the **AWS Management Console**, use the search bar at the top and search for:  
  `KMS`
- Select **Key Management Service**.

---

### 2. Navigate to Customer Managed Keys
- In the left navigation pane, click **Customer managed keys**.

<img width="1914" height="838" alt="image" src="https://github.com/user-attachments/assets/c1dcc87c-45c3-4cfc-ad32-8fffee3f5e28" />


>  **Tip**: If you don't see the navigation pane, click the ☰ **menu icon** in the top-left corner.

---

### 3. Create a New Key
- Click **Create key**.
- For **Key type**, select: `Symmetric`

<img width="1907" height="782" alt="image" src="https://github.com/user-attachments/assets/12ffdf23-2df0-4860-b7e5-6b876408454c" />

  - Symmetric keys **never leave AWS KMS unencrypted**.
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

>  Key administrators can manage the KMS key, but not use it to encrypt/decrypt data.

---

### 6. Define Key Usage Permissions
- In the **Key users** section, search for and select the role:  

voclabs

<img width="1908" height="753" alt="image" src="https://github.com/user-attachments/assets/85216d5a-8604-49e3-9ff5-b884173d5624" />

- Click **Next**.

>  Key users can use the key to **encrypt and decrypt** data.

---

### 7. Review and Create
- Review all the settings.
- At the bottom of the **Review** page, click **Finish**.

<img width="1773" height="808" alt="image" src="https://github.com/user-attachments/assets/12c3b3d9-3a44-4af8-94a7-67b6e81a4444" />


✅ Your key should now appear under **Customer managed keys**.

>  **Note**: If you’ve previously created KMS keys and scheduled them for deletion, you might still see those keys in the list with status `Pending deletion`. AWS enforces a 7-day minimum waiting period for deletion.

---

##  You Did It!
You successfully created a **customer-managed AWS KMS key**. This key will be used in the next tasks to manage data encryption for services like **S3** and **EC2**.


---

#  Task 2: Storing an Encrypted Object in an S3 Bucket

In this task, you’ll upload an image (`clock.png`) to an S3 bucket with **SSE-KMS encryption** using the **KMS key** you created in Task 1 (`MyKMSKey`).

---

## 📥 Step 1: Download the File to Upload

- Download the image file [clock.png](https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-ACSECF-1-DEV/lab-5.1-kms/s3/clock.png) to your local machine.

---

##  Step 2: Locate the S3 Bucket and Analyze Encryption Settings

1. Open the **Amazon S3 Console**.
2. In the navigation pane, click **Buckets**.
3. Select the bucket whose name includes `imagebucket`.

<img width="1917" height="805" alt="image" src="https://github.com/user-attachments/assets/6a49d6e7-8e74-4b33-848e-2cf1a5c8c6b2" />

4. Go to the **Properties** tab.

5. Scroll down to the **Default encryption** section:
   - 🔒 You should see that **default encryption is enabled**.

<img width="1918" height="783" alt="image" src="https://github.com/user-attachments/assets/01f387fd-ab3c-46d2-a866-a513f330e983" />

   - This ensures that **new objects** uploaded will be **automatically encrypted**, unless overridden.

---

##  Step 3: Upload the Image with KMS Encryption

1. Navigate to the **Objects** tab inside the bucket.
2. Click **Upload**.
3. Click **Add files** and select the previously downloaded `clock.png` from your system.

<img width="1908" height="782" alt="image" src="https://github.com/user-attachments/assets/aa1abbe6-574e-489c-9664-7cd4d8234c87" />

4. Expand the **Properties** section.
5. Under **Server-side encryption**, configure:
   - **Encryption key type**: `AWS Key Management Service key (SSE-KMS)`
   - **KMS Key**: Choose `MyKMSKey` from the dropdown
<img width="1626" height="686" alt="image" src="https://github.com/user-attachments/assets/c6062c79-3823-46a6-83f7-4b80a0be6f8d" />

6. Click **Upload**.

7. Once the upload is complete, click **Close**.

 `clock.png` is now stored in the bucket as an **SSE-KMS encrypted object**.

---

##  Step 4: Understand the Behind-the-Scenes Encryption Process

| Step | Description |
|------|-------------|
| 1️⃣   | User uploads `clock.png` to S3. |
| 2️⃣   | Amazon S3 requests a **data key** from AWS KMS. |
| 3️⃣   | AWS KMS generates a plaintext data key and encrypts it using **MyKMSKey**. |
| 4️⃣   | AWS KMS returns both the **plaintext** and **encrypted** data key to S3. |
| 5️⃣   | S3 encrypts the file using the plaintext key, stores it, and **discards the plaintext** version. The **encrypted data key** is saved in the object's metadata. |

---

## 🔍 Step 5: Verify Object-Level Encryption

1. In the **S3 Console**, click on the `clock.png` object.
2. In the **Properties** tab, scroll to **Server-side encryption**.
3. Verify that:
   - **Encryption type** is `SSE-KMS`
   - **AWS KMS key** is `MyKMSKey`

<img width="1851" height="802" alt="image" src="https://github.com/user-attachments/assets/ef4964e4-f17e-44e9-bcbd-d09e7ffc5f03" />


 This confirms that the object is protected using the correct KMS key.

---

## 🏁 Summary

- You uploaded an image file to an **S3 bucket**.
- You enforced **KMS encryption (SSE-KMS)** using the custom key **MyKMSKey**.
- You verified that the object was correctly encrypted with your customer-managed key.

---

#  Task 3: Attempting Public Access to the Encrypted S3 Object

In this task, you will attempt to access an **SSE-KMS encrypted object** (`clock.png`) in Amazon S3 using its **object URL**, and observe how encryption and access control mechanisms protect it from unauthorized public access.

---

##  Step 1: Attempt to Open the Object via URL

1. In the S3 Console, go to the `clock.png` object.
2. Copy the **Object URL** from the **Object overview** section.
   - Format: `https://<bucket-name>.s3.amazonaws.com/clock.png`
3. Paste the URL into a **new browser tab** and press **Enter**.

<img width="777" height="158" alt="image" src="https://github.com/user-attachments/assets/d627311b-7f38-4e25-8b17-ab5b17aa3a3b" />

 **Result**: You receive an `Access Denied` error.

>  Reason: By default, **S3 blocks all public access**, so the object is not publicly readable.

---

##  Step 2: Modify Bucket-Level Public Access Settings

1. In the S3 Console, go to the **bucket** that contains `imagebucket`.
2. Click on the **Permissions** tab.
3. In the **Block public access (bucket settings)** section:
   - Click **Edit**.
   - Uncheck ✅ **Block all public access**.
<img width="1918" height="812" alt="image" src="https://github.com/user-attachments/assets/b8745f97-e1c3-4f46-b604-98e7c033775d" />

   - Click **Save changes**.
   - Type `confirm` to verify the change.

---

##  Step 3: Modify Object Ownership and Access Control List (ACL)

1. In the same bucket, go to the **Permissions** tab.
2. Under **Object Ownership**, click **Edit**:
   - Choose **ACLs enabled**.
   - Acknowledge the warning.
   - Keep **Bucket owner preferred** selected.
   - Click **Save changes**.

3. Go to the **Objects** tab.
4. Select `clock.png`.
5. Choose **Actions > Make public using ACL**.
6. Confirm by choosing **Make public**.

✅ A confirmation banner appears indicating that the object was successfully made public.

---

##  Step 4: Revisit the Object URL

1. Go back to the browser tab where you previously saw the `Access Denied` error.
2. Refresh the page.

 **New Result**: You now see an `InvalidArgument` error.

---

##  Analysis: Why You Still Can't Access the File

Although public access is now granted:
- The object is encrypted using **SSE-KMS**.
- S3 requires **Signature Version 4 authentication** to decrypt KMS-encrypted objects.
- Since the public request **lacks signing credentials**, the object remains **inaccessible**.

###  Security Insight:
Even if **public access is accidentally enabled**, encryption using **AWS KMS** ensures that data **cannot be read** unless the user is **authorized to use the KMS key**.

---

##  Final Step: Close the Tab

- Close the browser tab displaying the S3 object URL with the error.

---

##  Summary

| Step | Outcome |
|------|---------|
| Access object via URL | ❌ Access Denied |
| Modify bucket ACL and object | ✅ Public access granted |
| Retry access | ❌ InvalidArgument (due to KMS encryption) |
| Insight | SSE-KMS protects data even if public access is misconfigured |

---




