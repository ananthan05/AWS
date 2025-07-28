## Overview

In this lab, you will interact with Amazon DynamoDB using both the AWS CLI and Boto3 (AWS SDK for Python). DynamoDB is a fully managed NoSQL database service designed for fast and flexible data handling. The goal of this lab is to perform core database operations that support modern application backends.

### Task 1: Preparing the lab

Before you can start working on this lab, you must import some files and install some packages in the VS Code IDE that was prepared for you.

### Connect to the VS Code IDE

At the top of these instructions, choose Details followed by  AWS: Show 

Copy values from the table similar to the following and paste it into an editor of your choice for use later.

`LabIDEURL`,`LabIDEPassword`

In a new browser tab, paste the value for LabIDEURL to open the VS Code IDE.

On the prompt window Welcome to code-server, enter the value for LabIDEPassword you copied to the editor earlier, choose Submit to open the VS Code IDE.

<img width="1169" height="410" alt="image" src="https://github.com/user-attachments/assets/11a14f94-225f-44eb-b747-11f39fc0d222" />

Download and extract the files that you need for this lab.

run this code on the vscode terminal

```
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACCDEV-2-91558/03-lab-dynamo/code.zip -P /home/ec2-user/environment
```

A code.zip can be seen

<img width="1384" height="840" alt="image" src="https://github.com/user-attachments/assets/ad7a29fb-0fad-4ea8-95cc-39963abf3014" />

unzip it using `unzip code.zip`

Run a script that upgrades the version of the AWS CLI installed on the VS Code IDE

run this command

`chmod +x ./resources/setup.sh && ./resources/setup.sh`

Confirm the version using `aws --version`

## Task 2: Creating a DynamoDB table by using the SDK for Python

Select DynamoDB fron AWS console and select tables from the side pane

<img width="946" height="778" alt="image" src="https://github.com/user-attachments/assets/a3d28b02-5636-42a8-bd65-90d6f0e61fc8" />

Review the Tables pane and confirm that no tables exist.

Return to the VS Code IDE browser tab.

In the navigation pane of the VS Code IDE, expand the python_3 directory. 

Open the create_table.py script by double-clicking it.

Replace the <FMI_1> placeholder with the table name, which is:

`FoodProducts`

<img width="1083" height="515" alt="image" src="https://github.com/user-attachments/assets/a9686a48-e574-4927-a2f8-3b3acc54d4fa" />

Close the file by choosing X from the top. 


Now run 

`cd python_3
python3 create_table.py`

on the terminal

<img width="572" height="88" alt="image" src="https://github.com/user-attachments/assets/e91326f8-696d-4cd0-b310-41a83fd0f967" />

Now run this command to check if all the tables are created succesfully

`aws dynamodb list-tables --region us-east-1`

<img width="773" height="132" alt="image" src="https://github.com/user-attachments/assets/4ab6d1f4-6703-41c1-b95e-254c7e07598d" />

We get the correct result

<img width="908" height="320" alt="image" src="https://github.com/user-attachments/assets/3aa36c5f-a02c-4725-a1e8-70693f9a8c94" />

Table created succesfully

## Task 3: Working with DynamoDB data – Understanding DynamoDB condition expressions

Review the JavaScript Object Notation (JSON) data that defines the new record.

In the VS Code IDE, expand the resources folder.

Open the not_an_existing_product.json file by double-clicking it.

<img width="548" height="300" alt="image" src="https://github.com/user-attachments/assets/129bbb82-2001-4879-b11c-723a3f5102d3" />

This file contains one item with two attributes: product_name and product_id. Both of these attributes are strings. The primary key (product_name) was defined when the DynamoDB table was created

To insert the new record, run the following command. Ensure that you are still in the python_3 folder.

```bash
aws dynamodb put-item \
--table-name FoodProducts \
--item file://../resources/not_an_existing_product.json \
--region us-east-1
```

Return to the DynamoDB console and choose the FoodProducts link.

Choose Explore table items.

Under Items returned you can see the created table

<img width="479" height="296" alt="image" src="https://github.com/user-attachments/assets/3d663dce-cc8d-402a-95d0-641c397fbb7c" />

### create a new record

Return to the VS Code IDE and load the not_an_existing_product.json file in the text editor.

Replace the product_name value of `<best cake>` with best pie

Do not change the product_id value

<img width="510" height="232" alt="image" src="https://github.com/user-attachments/assets/52b6e5f3-9daa-458e-add4-ba98418e8591" />

Now run this command

```
aws dynamodb put-item \
--table-name FoodProducts \
--item file://../resources/not_an_existing_product.json \
--region us-east-1
```

To view the new record in the table by using the console:

Return to the Item explorer in the DynamoDB console.

Confirm that the FoodProducts table is selected.

Choose Scan, then Run

<img width="1364" height="477" alt="image" src="https://github.com/user-attachments/assets/ce10f6dc-e0d7-4ac6-918b-7cc406d7d447" />


We can see the result

<img width="1125" height="327" alt="image" src="https://github.com/user-attachments/assets/093d2ef7-fecf-481e-959c-fc8d7b5c1999" />

Return to the VS Code IDE and try re-running the previous AWS CLI command

Then select the `Run` command again, we can see that no new elment was created.This is because the product id prevents the same product name being added multiple times.

### Update the JSON record:

Return to the VS Code IDE and the not_an_existing_product.json file.

Don't change the value of product_name but replace the product_id value of <676767676767> with 3333333333

Now run the code again

```
aws dynamodb put-item \
--table-name FoodProducts \
--item file://../resources/not_an_existing_product.json \
--region us-east-1
```

<img width="1138" height="346" alt="image" src="https://github.com/user-attachments/assets/fc1a68d9-393a-4fa8-a21b-3d6368605ca6" />

The product_id value of the best pie record was replaced with the new value of product_id.However, you don't want this behavior. You want separate operations for adding new products and for updating product attributes

### Test the condition expression

Return to the VS Code IDE and the not_an_existing_product.json file.

Don't change the value of product_name.

Replace the product_id value of <3333333333> with 2222222222

Run this command

```bash
aws dynamodb put-item \
--table-name FoodProducts \
--item file://../resources/an_existing_product.json \
--condition-expression "attribute_not_exists(product_name)" \
--region us-east-1
```

<img width="1138" height="158" alt="image" src="https://github.com/user-attachments/assets/2ebf8b3a-dc3c-43af-bd2d-a00492d5817d" />

This behavior is expected because the condition expression prevented an overwrite of the existing item.

### Task 4: Adding and modifying a single item by using the SDK

In this task, you will add and modify a single item by using the SDK.

Update the conditional_put.py script.

In the VS Code IDE, go to the python_3 directory.

Open the conditional_put.py script.

Replace the <FMI> placeholders as directed in the script. 

<img width="965" height="688" alt="image" src="https://github.com/user-attachments/assets/ca22c011-f5d4-4493-9d0b-f31e662757e4" />

Now run this code on the terminal

`python3 conditional_put.py`

After scaning again we get the updated result

<img width="1099" height="392" alt="image" src="https://github.com/user-attachments/assets/96a71ca7-4420-4aee-8969-2a40a8ebdcc9" />

In the VS Code IDE, update the conditional_put.py script again. This time, replace the product_id value of <a444> to a555 and close the file.

As you might expect, the item remains unchanged. Because the condition attribute_not_exists(product_name) was included in the put_item operation, the item was not overwritten. This failure behavior is exactly the behavior that you want.

Now change the product_name to `cherry pie` and run the code again

<img width="1108" height="404" alt="image" src="https://github.com/user-attachments/assets/fd50dd7e-d8ab-4a23-9f1c-6c6fd74c355f" />

A new record was added to the table. Now, only new products will be added to the table. This feature prevents accidental updates to existing records when more records are inserted.

### Task 5: Adding multiple items by using the SDK and batch processing

Delete all records

Select the check boxes for all the table records. 

From the Actions menu, choose Delete item(s). 

In the pop-up window confirmation box, enter Delete and choose Delete items

Open test.json file

<img width="689" height="607" alt="image" src="https://github.com/user-attachments/assets/35a46877-9717-41c0-b2fb-2ed29d60ef3e" />

This file contains six records that you use to test the batch-load script

Update the test_batch_put.py script

Update the <FMI_1> placeholder with the `FoodProducts` table name. 

Replace the <FMI_2> with the `product_name` primary key name.

Now Run the command

`python3 test_batch_put.py`

<img width="725" height="159" alt="image" src="https://github.com/user-attachments/assets/a02931f2-fa87-4f27-a26f-2cce0a6eb66d" />

<img width="892" height="364" alt="image" src="https://github.com/user-attachments/assets/d0c6895f-25dd-46c5-8779-5e75845a5818" />

Instead of keeping the first value of price_in_cents, for apple_pie, the most recent value in the data file was applied. 

With single-item PUT requests (put_item), you can avoid overwriting duplicate records by including a condition. However, with batch inserts, you have two options for handling duplicate keys. You can either allow the overwrite, or you can cause the entire batch process to fail. 

### Updating the script so that it fails when duplicates are included in the batch

Update line 12 by changing `<with table.batch_writer(overwrite_by_pkeys=['product_name']) as batch>` to 

```bash
with table.batch_writer() as batch:
```

Now delete all the entries from AWS.Now run script again

```
python3 test_batch_put.py
```

<img width="1480" height="375" alt="image" src="https://github.com/user-attachments/assets/4116f584-4bb2-4530-9115-8ab016c063b3" />

We got an error,`ClientError: An error occurred (ValidationException) when calling the BatchWriteItem operation: Provided list of item keys contains duplicates`

<img width="1147" height="371" alt="image" src="https://github.com/user-attachments/assets/3390788e-5a7c-41fe-97fb-616acc1d2faf" />

No table was added in dynamoDB.

Now go to resources/website/all_products.json You will find many items.

In order to load the raw JSON used in the website, you use a new script called batch_put.py.

Modify the python_3/batch_put.py script. 

Replace <FMI> with `FoodProducts`

Run the script `python3 batch_put.py`

<img width="733" height="369" alt="image" src="https://github.com/user-attachments/assets/7913d37f-ea6e-42b6-b315-61cf81537f90" />

<img width="1071" height="626" alt="image" src="https://github.com/user-attachments/assets/a1d548f2-5bad-4803-b998-e5e36c6487e3" />


## Task 6: Querying the table by using the SDK

Now that the data is loaded, we need a way to retrieve, or query, the product information from the DynamoDB table. 

The SDK has two operations for retrieving data from a DynamoDB table: scan() and query().

The scan operation reads all records in the table, and unwanted data can then be filtered out. If only a subset of the table data is needed, the query operation often provides better performance because it reads only a subset of the records in the table or index.

Edit the script that selects all records from the table:

In VS Code IDE, open python_3 > get_all_items.py.

Update the <FMI_1> placeholder with the FoodProducts table name

<img width="899" height="542" alt="image" src="https://github.com/user-attachments/assets/86bb520f-f84c-4fea-819e-b0d90207ec80" />

Now run the code

`python3 get_all_items.py`

We will get a result

<img width="1543" height="708" alt="image" src="https://github.com/user-attachments/assets/c6746592-cc2b-42cf-bf08-ab73d18413a4" />

### creating a new script that returns a single product instead of returning all products

Update the get_one_item.py script.

Replace the <FMI_1> with the name of the table's primary key

<img width="1038" height="653" alt="image" src="https://github.com/user-attachments/assets/9e9fcd6e-abf2-4749-b4ce-6f82dfaed8ca" />

<img width="1546" height="77" alt="image" src="https://github.com/user-attachments/assets/54d07ab2-9a21-4508-8a87-4e21b3be33b1" />

You will notice that this is now in the DynamoDB object format (unlike the query you did before) and thus includes keys such as N and S. 

We got the result successfully

## Task 7:  Adding a global secondary index to the table

Searching on a primary key is easy. However, in order to search on attributes that are not part of a primary key, you need to add a Global Secondary Index to the existing FoodProducts table.

In this task you will implement this feature

Update the add_gsi.py script.

Replace the <FMI_1> with the KeyType of HASH

On line 12 focus on the GlobalSecondaryIndexUpdates parameter. Notice that a new index named special_GSI is created. This new index consists of one attribute: special.

Now run the code 

`python3 add_gsi.py`

In the DynamoDB console, monitor the status of the index:

Choose Tables -> FoodProducts -> Indexes tab.

<img width="1114" height="415" alt="image" src="https://github.com/user-attachments/assets/37538a31-b782-447f-bd50-9bfd20e18e8c" />

Update the scan_with_filter.py script.

Change <FMI_1> to special_GSI

Change  <FMI_2> to tags

<img width="987" height="487" alt="image" src="https://github.com/user-attachments/assets/0e9402be-0f13-4516-8324-cfc00fa7db77" />

On line 18, including the IndexName option lets the scan operator know that it will be going to the index and not the main table to read the data

The filter expression processes the records that have been read and shows only records that meet the comparison criteria. In this case, it shows records only if they don't have out of stock in the tags attribute. This ensures that only items that are available or "on offer" are shown to customers. 

Now run `python3 scan_with_filter.py`


<img width="1565" height="195" alt="image" src="https://github.com/user-attachments/assets/1d50aa43-969c-4aad-8386-d0ea65ab19b8" />

Submit your work
