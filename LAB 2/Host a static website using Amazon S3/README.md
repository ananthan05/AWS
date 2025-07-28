## objective

- Create a static website by using Amazon S3.

- Apply a bucket policy on an S3 bucket to configure customized data protection.

- Upload objects to an S3 bucket by using the AWS SDK for Python (Boto3).

- Configure the website that is hosted on Amazon S3 to be accessible only from a specific IP address range, and test the configuration.


# Task 1: Connecting to VS Code IDE and Configuring the Environment

This guide walks through connecting to the AWS Cloud9-based VS Code IDE and setting up the development environment for the lab.

---

## Step 1: Connect to VS Code IDE

1. At the top of the lab instructions, click **Details**, then click **AWS: Show**.
2. Copy the following values into a text editor:
   - **LabIDEURL**
   - **LabIDEPassword**

<img width="1748" height="798" alt="image" src="https://github.com/user-attachments/assets/41ce713f-a60b-4e9e-80ea-e730065f6cfc" />

3. Open a new browser tab.
4. Paste the **LabIDEURL** into the address bar and hit Enter.
5. When prompted with **Welcome to code-server**, enter the **LabIDEPassword**.
6. Click **Submit** to launch the VS Code IDE environment.
<img width="1662" height="741" alt="image" src="https://github.com/user-attachments/assets/02ffc034-f212-4815-b75d-0610fb9925fe" />


---

## Step 2: Install the AWS SDK for Python (Boto3)

1. In the **bash terminal** at the bottom of the VS Code IDE, run the following command to install Boto3:
 ```bash
   sudo pip3 install boto3
```
<img width="1907" height="857" alt="image" src="https://github.com/user-attachments/assets/f8bc30df-9675-4ce0-9185-e4b4dd9e0e08" />

## Step 3: Download Required Files for the Lab
In the same terminal, run the following command to download the code zip file:

```
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACCDEV-2-91558/02-lab-s3/code.zip -P /home/ec2-user/environment
```
After the download, confirm that the code.zip file appears in the left-hand file explorer pane.

<img width="1918" height="850" alt="image" src="https://github.com/user-attachments/assets/1054f529-6004-41c3-bc24-611876d91e07" />

## Step 4: Extract the Code Files
Run the following command to unzip the downloaded archive
```
unzip code.zip
```

<img width="1918" height="848" alt="image" src="https://github.com/user-attachments/assets/1ecda9db-dbfd-450f-b55e-c9e8886c38af" />

This extracts all the necessary files for use in upcoming tasks.

Step 5: Verify AWS CLI Version
Run the following command to check the installed AWS CLI version:

```
aws --version
```

<img width="1037" height="136" alt="image" src="https://github.com/user-attachments/assets/269d144f-93b1-4e28-a0d5-9bc163157fea" />

Confirm that the output resembles the following format (version may vary slightly):

```
aws-cli/2.15.9 Python/3.11.6 Linux/5.10.205-195.804.amzn2.x86_64 exe/x86_64.amzn.2 prompt/off
```
Ensure that version 2.x is being used.

✅ Setup Complete: Your environment is now ready for development!.

# Task 2: Creating an S3 Bucket by Using the AWS CLI

In this task, you will use the AWS CLI to create an S3 bucket for hosting a static website and adjust public access settings via the AWS Management Console.

---

## Step 1: Create the S3 Bucket Using the AWS CLI

1. In the **VS Code terminal**, run the following command after replacing `<bucket-name>` with your custom name:

   - Format: `<your-initials>-<YYYY-MM-DD>-s3site`
   - Example: `am-2025-07-28-s3site`

```bash
   aws s3api create-bucket --bucket <bucket-name> --region us-east-1
```
You should see output similar to:
```jason
{
  "Location": "/am-2025-07-28-s3site"
}
```
⚠️ Important: Copy your bucket name (without the leading /) and save it in a text file or note — you’ll need it in later steps.

<img width="1232" height="272" alt="image" src="https://github.com/user-attachments/assets/bc5d77b8-2fc0-4280-b3a2-c5896f2aa7cf" />


## Step 2: Confirm the Bucket in AWS Management Console

Go to the AWS Console and log in if required.

Navigate to Services → S3.

Confirm that your bucket (e.g., am-2025-07-28-s3site) is listed.

<img width="1902" height="671" alt="image" src="https://github.com/user-attachments/assets/eae91b87-bc23-4042-a144-52fb575cc67e" />


In the Access column, you should see:

```cpp
Bucket and objects not public
```

## Step 3: Modify Bucket Permissions

1. Click your **bucket name** to open its details in the AWS Management Console.
2. Navigate to the **Permissions** tab.
3. Under **Block public access (bucket settings)**, click **Edit**.
4. Uncheck **Block all public access**.
5. Ensure the following options remain **checked**:
   - ✅ Block public access to buckets and objects granted through **new ACLs**
   - ✅ Block public access to buckets and objects granted through **any ACLs**
   - ✅ Block public and cross-account access to buckets and objects through any public bucket or access point policies
6. Click **Save changes**.
7. In the **confirmation dialog**, type `confirm` and choose **Confirm**.

<img width="1912" height="762" alt="image" src="https://github.com/user-attachments/assets/4f6d0c68-a8f2-456a-8b57-e7b34dd4c33b" />


---

## Step 4: Review the Bucket Policy Section

1. Scroll down further within the **Permissions** tab.
2. Verify that there is **no bucket policy** currently attached.

<img width="1917" height="807" alt="image" src="https://github.com/user-attachments/assets/82666809-0d60-4351-afa6-297eb31693f7" />


ℹ️ _You will add a bucket policy in a later task._

---

✅ **Task Complete:** Your S3 bucket is now created, and its permissions are properly configured. You're ready to move on to the next step!


# Task 3: Setting a Bucket Policy on the Bucket Using the AWS SDK for Python

In this task, you will create a custom S3 bucket policy that restricts access to specific users and conditions, and apply the policy using the AWS SDK for Python (Boto3).

---

## Step 1: Create the Policy Document

1. In the **VS Code IDE**, go to the **Explorer** pane (left sidebar).
2. Click the **menu** icon (three lines), then choose:
`File → New File`
3. Name the file:
```
website_security_policy.json
```
4. In the popup showing the file path, click **OK** to confirm.

5. Paste the following content into the `website_security_policy.json` file:

> ⚠️ Replace `<bucket-name>` with your actual S3 bucket name in **all 3 places**.  
> Replace `<ip-address>` with your **real public IPv4 address** from [https://whatismyip.com](https://whatismyip.com).

```json
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": [
                "arn:aws:s3:::<bucket-name>/*",
                "arn:aws:s3:::<bucket-name>"
            ],
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": [
                        "<ip-address>/32"
                    ]
                }
            }
        },
        {
            "Sid": "DenyOneObjectIfRequestNotSigned",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::<bucket-name>/report.html",
            "Condition": {
                "StringNotEquals": {
                    "s3:authtype": "REST-QUERY-STRING"
                }
            }
        }
    ]
}

<img width="1667" height="807" alt="image" src="https://github.com/user-attachments/assets/98b4674a-1d22-42cd-b06e-54f0bbcbe4ff" />

```

## Step 2: Apply the Bucket Policy Using the SDK for Python

In the Explorer pane, expand the python_3 directory.

Open the permissions.py file.

In the file, replace all <bucket-name> placeholders with your actual bucket name.

<img width="1913" height="848" alt="image" src="https://github.com/user-attachments/assets/f82dd610-f971-4b00-9855-dd4395e21e6f" />


✅ Changes are saved automatically.


## Step 3: Run the Python Script
In the terminal at the bottom of the IDE, navigate to the python_3 directory:

```bash
cd python_3
```

Run the script:

```bash
python3 permissions.py
```
If successful, you will see:

```nginx
DONE
```

<img width="1912" height="866" alt="image" src="https://github.com/user-attachments/assets/6a46cd54-a636-4697-afae-670594cac700" />

Task Complete: Your custom bucket policy is now set and applied using Python!

# Task 4: Uploading Objects to the Bucket to Create the Website

In this task, you will upload website files from the `resources/website` directory to your S3 bucket. This step publishes the initial version of your site, with caching disabled for easier development.

---

## Step 1: Upload Website Files to the S3 Bucket

1. Open the **VS Code Terminal**.

2. Ensure you're in the `python_3` directory. If not, navigate to it:

```bash
   cd /home/ec2-user/environment/python_3
```

3. Run the following command to recursively upload all files from the resources/website directory to your S3 bucket:

🔁 Replace <bucket-name> with your actual S3 bucket name
🚫 This command disables caching by setting `Cache-Control` to `"max-age=0"`

```
aws s3 cp ../resources/website s3://<bucket-name>/ --recursive --cache-control "max-age=0"
```

4. If successful, the terminal will list each file as it's uploaded, similar to:

```
upload: ../resources/website/index.html to s3://your-bucket-name/index.html
upload: ../resources/website/style.css to s3://your-bucket-name/style.css
...
```

<img width="1432" height="313" alt="image" src="https://github.com/user-attachments/assets/46658e73-0102-4a2e-9e4f-7345480c87e5" />

Task Complete: Website files have been uploaded to your S3 bucket. You're one step closer to launching your site!


# Task 5: Testing Access to the Website

Now that the website files are uploaded to your S3 bucket, it's time to test the site and confirm access restrictions are working as expected.

---

## Step 1: Open the Website via S3 Object URL

1. In the **Amazon S3 Console**, open your **bucket**.
2. Go to the **Objects** tab.
3. If you don’t see your files, click the **refresh icon** to reload.

<img width="1918" height="797" alt="image" src="https://github.com/user-attachments/assets/612e95e2-868d-4fcd-9a02-f70e73225dd7" />

4. Click on the `index.html` file.

5. Copy the **Object URL** — it will look like:
```
https://<bucket-name>.s3.amazonaws.com/index.html
```
<img width="1860" height="823" alt="image" src="https://github.com/user-attachments/assets/d227216e-04a1-4588-a6e4-56bc25b6c779" />


6. Paste the URL into your **browser** on the same machine (with the allowed IPv4 address).

7. ✅ You should see the website load successfully.

<img width="1908" height="862" alt="image" src="https://github.com/user-attachments/assets/2bb52528-276e-4432-847d-ec8ab10457ac" />

---

## Step 2: Test Access from Outside Your Network

Your bucket policy restricts access to only a specific IPv4 address. You can verify this restriction in one of two ways:

### Option A: Use a Mobile Device on Cellular Data

1. Open a browser on your **mobile phone** with **mobile data** (not Wi-Fi).
2. Paste the Object URL.
3. ❌ You should receive an **AccessDenied** error — this is expected.

### Option B: Use `curl` from the IDE Terminal

1. In the **VS Code terminal**, run:

```bash
curl https://<bucket-name>.s3.amazonaws.com/index.html
```

<img width="1233" height="177" alt="image" src="https://github.com/user-attachments/assets/46dd6296-b43a-4fee-9cbc-648557466eec" />


This should also return an AccessDenied response, because the IDE uses a different IP address.

## Step 3: Test Website Behavior
In the browser where the website loads correctly, click Login in the top-right corner.

You should see the following alert message:
```
No API to call
```

<img width="1882" height="607" alt="image" src="https://github.com/user-attachments/assets/c2459075-79c5-4ef2-a3ba-bf176d3ba6c2" />


This behavior is expected. The backend API isn't built yet — you'll create it in a later lab.


# Task 6: Analyzing the Website Code

In this task, you will explore the frontend code behind the website hosted on Amazon S3. You'll review the HTML, CSS, JavaScript, and JSON structure that define the static version of the application.

---

## Step 1: Explore the `index.html` File

1. In **VS Code**, navigate to:
resources → website → index.html

<img width="1913" height="798" alt="image" src="https://github.com/user-attachments/assets/f785f34e-0150-4e4d-9b9b-5c429e97e788" />

2. Observe the contents of the file:
- The `<head>` section links multiple **CSS stylesheets**.
- The `<body>` section includes several **JavaScript files**, including:
  - `config.js`
  - `pastries.js`

---

## Step 2: Review the `config.js` File

1. Open `config.js` in the same directory.

2. You'll find the following JavaScript object:

```javascript
window.COFFEE_CONFIG = {
  API_GW_BASE_URL_STR: null,
  COGNITO_LOGIN_BASE_URL_STR: null
};

<img width="1452" height="526" alt="image" src="https://github.com/user-attachments/assets/e2c57820-2421-4ee7-af35-ac4000e23f7b" />

```

3. Insight: These values will be updated in future labs when you add:

Amazon API Gateway

Amazon Cognito authentication

For now, the values are set to null, so no backend API calls will be made.

## Step 3: Review the pastries.js File
Open the pastries.js file.

<img width="1916" height="801" alt="image" src="https://github.com/user-attachments/assets/989475e1-521c-42d6-8028-d164c1cc10c1" />

Find the loadAllItems() function. It contains logic like this:

```javascript
if (window.COFFEE_CONFIG.API_GW_BASE_URL_STR === null) {
    // Load static product data from all_products.json
}
```
3. Insight: Since API_GW_BASE_URL_STR is null, the script will fall back to loading the product data from a static JSON file.

4. Scroll down to locate the printItems() function. This handles rendering the product list on the page.

## Step 4: Examine the all_products.json File

Open all_products.json located in the same website directory.

<img width="1912" height="843" alt="image" src="https://github.com/user-attachments/assets/9bab88f1-4e74-4b51-b8d5-bf2186496bf0" />

This file contains the hardcoded product data (e.g., pastries, coffee, etc.) shown on the website.

Example structure:
```
{
  "items": [
    {
      "id": "p1",
      "name": "Blueberry Muffin",
      "price": 3.99,
      "description": "Freshly baked with real blueberries."
    },
    ...
  ]
}
```
3.Insight: In a future lab, this data will be moved into a database and fetched dynamically using REST API calls.
