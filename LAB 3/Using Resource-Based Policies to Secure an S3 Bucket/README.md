## Overview

From this lab we can understand the following:

 - Recognize how to use IAM identity-based policies and resource-based policies to define fine-grained access control to AWS services and resources.
 - Describe how an IAM user can assume an IAM role to gain different access permissions to an AWS account.
 - Explain how S3 bucket policies and IAM identity-based policies that are assigned to IAM users and roles affect what users can see or modify across different AWS services in the AWS Management Console.

## Task 1: Accessing the console as an IAM user

After clicking on `start lab` button and wait till the lab turns green.

Then select 'AWS Details' tab, select the  `IAMUserLoginURL` and load it in a new tab.

Enter the name as `devuser` and enter the password provided in the lab as password

## Task 2: Attempting read-level access to AWS services

Now that you are logged in to the console as the IAM user named devuser, you will explore the level of access that you have to a few AWS services, including Amazon Elastic Compute Cloud (Amazon EC2), Amazon S3, and IAM.

Select EC2 from the search

<img width="1897" height="793" alt="image" src="https://github.com/user-attachments/assets/817d616b-2c2d-49c3-8b7d-dda80c063ba4" />

We can see there are many API errors showing

<img width="1863" height="809" alt="image" src="https://github.com/user-attachments/assets/e37b7280-bfe7-4ea8-b96c-2c8e058d042c" />

When we try to create an instance we will be shown many errors on the screen, This is beacsue devuser is not granted with these permissions

We will try accessing S3 bucket, similarly we got many errors while trying to view or edit the bucket

<img width="1457" height="728" alt="image" src="https://github.com/user-attachments/assets/75334989-3351-4dcb-a80c-c3c546304d90" />

<img width="1874" height="743" alt="image" src="https://github.com/user-attachments/assets/9c6aeec1-d9db-4e37-85d0-a97e1f462a62" />

## Task 3: Analyzing the identity-based policy applied to the IAM user

Select IAM from the services menu

<img width="1051" height="686" alt="image" src="https://github.com/user-attachments/assets/2a02e108-7c95-419f-b4bf-6187870ddab6" />

We can see that the current user doesnt have enough permissions

<img width="1031" height="515" alt="image" src="https://github.com/user-attachments/assets/5d60f30c-4f73-4e52-8270-8051ac67bdb9" />

Chose User gropus from left pane, and develepor group in that

<img width="1679" height="544" alt="image" src="https://github.com/user-attachments/assets/056c3f7f-94b2-473b-b815-94c8c10a395a" />

we can see devuser is in this space

<img width="1471" height="291" alt="image" src="https://github.com/user-attachments/assets/6be9e381-b49c-479d-855f-771bf0c61fe7" />

Click on permission tab and we can see a policy attached to this group

Click the `+` icon to view the policy details

 - Notice that the policy does not allow any Amazon EC2 actions.
 - Notice the IAM actions that the policy allows. When you accessed the IAM dashboard, you saw a message that stated that you did not have iam:GetAccountSummary authorization. That action is not permitted in this policy document. However, many read-level IAM permissions are granted. For example, you are able to review the details for this policy.
 - Notice the Amazon S3 actions that the policy allows. No object-related actions are granted, but some actions related to buckets are allowed.

Save the policy as a json file in your windows as DeveloperGroupPolicy.json

## Task 4: Attempting write-level access to AWS services

In this task, we will attempt to make two API calls that require write-level access within Amazon S3. The first action is to create an S3 bucket, and the second action is to upload an object to that bucket

Now search for `S3` and click on `create bucket` option

For bucket name give your initials plus random 4 digit number eg:`zba1234`

<img width="1331" height="630" alt="image" src="https://github.com/user-attachments/assets/efb056d7-cd27-4290-a806-c9a1176eaee7" />

now click create a bucket

We will try uploading files in the bucket

Click on the created bucket,Choose Upload, and then choose Add files

Browse to and choose the DeveloperGroupPolicy.json file that you saved earlier.Choose Upload.

<img width="1889" height="738" alt="image" src="https://github.com/user-attachments/assets/491c31cf-67f9-416e-a5c0-138a0c9b2594" />

<img width="1887" height="746" alt="image" src="https://github.com/user-attachments/assets/6c3cf94f-e403-427f-995c-53dee500d935" />

It will display upload failed

Now select Amazon S3 from the let pane 

<img width="1829" height="775" alt="image" src="https://github.com/user-attachments/assets/1157f128-9f62-4f40-bf40-61146a1a26b0" />

Return to the text editor where you copied the DeveloperGroupPolicy.json document.

Review the policy details to understand why you were able to create an S3 bucket but couldn't upload objects to it.

The policy includes permissions like s3:CreateBucket, s3:ListAllMyBuckets, and s3:ListBucket, which allow creating and listing buckets.

s3:PutObject (required to upload objects) or s3:PutObjectAcl is not included in the Action list.

Since there is no s3:PutObject action, attempts to upload files to the bucket are denied

## Task 5: Assuming an IAM role and reviewing a resource-based policy

In this task, you will try to access bucket1 and bucket2 while logged in as the devuser IAM user. You will also try to access the buckets by using a role that was preconfigured as part of the lab setup.

In the amazon S3 console chose the bucket 1 and try to download the image2 file

<img width="1020" height="644" alt="image" src="https://github.com/user-attachments/assets/90304c4f-0145-461c-b5d6-08a06028fa61" />

<img width="1489" height="548" alt="image" src="https://github.com/user-attachments/assets/8b94a5af-543c-4922-becf-6521203ff4c4" />

<img width="1911" height="329" alt="image" src="https://github.com/user-attachments/assets/94dfeebb-e4b4-456b-9e0e-0f1bee29b1da" />

An access denied error will be shown

We will go back and try to download image1 file of bucket 2 and see what happens

<img width="936" height="300" alt="image" src="https://github.com/user-attachments/assets/c9263859-5c24-41b0-a5c2-032566b20b33" />

Again Same error

now we will Assume the BucketsAccessRole IAM role in the console:

In the upper-right corner of the page, choose devuser, and then choose Switch role.

Enter the AccountID value from the AWS Details panel on the lab instructions page.

<img width="935" height="738" alt="image" src="https://github.com/user-attachments/assets/7c704b60-8825-4a6d-9466-4fc799dc52e1" />

Try to download an object from Amazon S3 again:

We will download image2 from bucket 1 again

<img width="951" height="811" alt="image" src="https://github.com/user-attachments/assets/303c124d-f386-4182-8b8a-6b626ce3ba83" />

Now we can successfully download the image

The download was successful, which means that the policy or policies applied to the BucketsAccessRole allow the s3:GetObject action on bucket1.

To Test IAM access with the BucketsAccessRole, try selecting the user groups tab

<img width="925" height="615" alt="image" src="https://github.com/user-attachments/assets/e43a8bf7-372a-4af7-aa46-f48232b56f54" />

It says access denied

You no longer have permissions to view the IAM user groups page because BucketsAccessRole does not have the iam:ListGroups action applied to it.

In the upper-right corner of the page, choose BucketsAccessRole, and then choose Switch back.

<img width="939" height="672" alt="image" src="https://github.com/user-attachments/assets/c68bad5e-b4a0-4d5f-af7a-469bf69c2db4" />

In the left navigation pane, choose User groups again.

<img width="901" height="386" alt="image" src="https://github.com/user-attachments/assets/83055781-1656-43d3-9eda-72b1d151c6f7" />

Now that you unassumed the BucketsAccessRole, you have the permissions that are assigned to the devuser IAM user (through this user's membership in the DeveloperGroup). You are able to view the user groups page again.

Analyze the IAM policy that is associated with the BucketsAccessRole:

In the left navigation pane, choose Roles.Search for BucketsAccessRole and choose the role name when it appears.

<img width="933" height="676" alt="image" src="https://github.com/user-attachments/assets/b6875b54-3428-4e36-922d-d14f21c1fb92" />


Choose the arrow to the left of  ListAllBucketsPolicy.

This policy grants the same s3:ListAllMyBuckets action to every resource. This permission allows you to see all S3 buckets when you assume BucketsAccessRole.


<img width="889" height="381" alt="image" src="https://github.com/user-attachments/assets/9f6c9d8b-9802-4594-9f31-c9293ba47114" />

Choose the arrow to the left of  GrantBucket1Access.

Analysis: This policy allows the s3:GetObject, s3:ListObjects, and s3:ListBucket actions. Notice that this policy does not grant s3:PutObject access. The allowed actions are only granted for specific resources, bucket1 and all objects within bucket1 (as indicated by /*). The asterisk (*) is a wildcard character, which indicates that this would match any value.

<img width="882" height="495" alt="image" src="https://github.com/user-attachments/assets/be52b117-b7e3-493b-be6b-d20ebb961409" />


Because of this policy, when you assumed the BucketsAccessRole, you could see and download objects from bucket1.

 
Save a copy of the GrantBucket1Access policy to your computer

 choose the Trust relationships tab.

Notice that the devuser IAM user in this AWS account is listed as a trusted entity that can assume this role.

<img width="906" height="478" alt="image" src="https://github.com/user-attachments/assets/95c0be2c-077f-4746-8711-a3a049c592fc" />

Now Assume the BucketsAccessRole, Navigate to s3 and try to upload an image2 that we downloaded earlier to bucket2

<img width="937" height="609" alt="image" src="https://github.com/user-attachments/assets/e11b26d1-bbf7-49f3-9d85-b48168bd1b13" />

image 2 was successfully uploaded.

After assuming the BucketsAccessRole, you successfully accessed bucket1 to download an object. You then uploaded the same object to bucket2.

The policies attached to the BucketsAccessRole were limited to bucket 1 but we were able to access bucket 2.It will be clear in next task

## Task 6: Understanding resource-based policies

On the details page for bucket2, choose the Permissions tab.

In the Bucket policy section, review the policy that is applied to bucket2.

<img width="1703" height="675" alt="image" src="https://github.com/user-attachments/assets/4e4d3ffc-5858-49ec-88b6-5f2e7508cf48" />

The policy has two statements.

The first statement ID (SID) is `S3Write`. The principal is the BucketsAccessRole IAM role that you assumed. This role is allowed to call the actions `s3:GetObject` and `s3:PutObject` on the resource, which is bucket2.

The second SID is ListBucket. The principal is BucketsAccessRole. This role is allowed to call the action `s3:ListBucket` on the resource, which is bucket2.

he role-based policies attached to the BucketsAccessRole IAM role granted `s3:GetObject` and `s3:ListBucket` access to bucket1 and the objects in it. These role-based policies did not explicitly allow access to bucket2; however, they also did not explicitly deny access.

<img width="1100" height="476" alt="image" src="https://github.com/user-attachments/assets/440a86d0-f4f6-4225-8182-0b891927f052" />

While assuming BucketsAccessRole, you tried to upload an object to bucket2, and you were able to do it. That seemed strange based on the IAM policies that you reviewed. However, after you reviewed the resource-based policy (in this case, a bucket policy) that was attached to the bucket, your access made sense. That bucket policy grants access, including the s3:PutObject action, to bucket2 to the BucketsAccessRole principal.

Challenge task

Your objective for this challenge task is to figure out a way to upload the Image2.jpg file to bucket3.

Trying to upload as devuser 

<img width="896" height="728" alt="image" src="https://github.com/user-attachments/assets/7e4241a3-d655-4551-8348-bb34a70a4d6d" />

upload failed

We cant see the bucket policy either

Now assumed the role of `BucketAccessRole`

<img width="879" height="672" alt="image" src="https://github.com/user-attachments/assets/ababe044-97a1-4632-aca7-daecfd6299a0" />

Insufficent permission for listing objects.

While viewnig the policy we can see that another role called `OtherBucketAccessRole` has access to this bucket3

<img width="902" height="723" alt="image" src="https://github.com/user-attachments/assets/6310b038-9f72-4923-a33c-bca5d91f1064" />

So we will assume this role now

<img width="936" height="780" alt="image" src="https://github.com/user-attachments/assets/fb06bdaa-8079-4614-b870-25fef2cfc27a" />

After swithcing we will try to upload the image2 on bucket 3

<img width="911" height="747" alt="image" src="https://github.com/user-attachments/assets/d5ac883f-2e64-42a4-a53e-558839cd0581" />

Succesfully uploaded now


