# 🔐 Encrypt Data at Rest with AWS KMS

**Cloud Security | Data Encryption | AWS IAM**  
*Built and documented by Mounika Addula*

---

## 🔎 Project Overview

This project demonstrates how I used **AWS Key Management Service (KMS)** to protect data stored in **Amazon DynamoDB**. The goal was to create a **customer-managed KMS key**, encrypt a DynamoDB table, and enforce fine-grained **access control** using **IAM policies**.

I tested the complete encryption-decryption workflow by inserting and retrieving records, ensuring that only **authorized users** could access encrypted data.

---

## 🧰 Tools & Concepts

**Services Used:**

- AWS Key Management Service (KMS)
- Amazon DynamoDB
- AWS IAM

**Key Concepts Learned:**

- Data encryption and encryption at rest
- Symmetric key encryption using customer-managed KMS keys
- Access control with IAM policies
- Integration of KMS with DynamoDB for secure data storage
- Managing key permissions for different users


---


### Step 1: Creating the Customer-Managed KMS Key (CMK)

To begin securing my DynamoDB table with encryption, I created a **Customer-Managed Key (CMK)** in AWS Key Management Service (KMS). Below are the exact steps I performed.



### 🛠️ Actions I Took

1. Logged into the **AWS Console** and opened the **KMS** service.
2. In the left navigation pane, I selected **Customer managed keys**.
3. Clicked on **Create key**.
4. Chose **Symmetric** as the key type.  
   _Symmetric encryption uses a single key for both encryption and decryption—ideal for DynamoDB._
5. Selected **Encrypt and decrypt** for key usage.  
   _This enables the key to perform encryption and decryption operations._
6. Clicked **Next**.



### ⚙️ Configured Key Settings

- I set the **key alias** to: `MY-kms-key`  
  _This makes the key easier to reference later._
- For the description, I added:  
  `KMS key that will encrypt a DynamoDB database. Created for MY Encryption with AWS KMS project.`

Then I clicked **Next**.


### 👤 Set Key Administrators and Key Users

- I added my **IAM Admin user** as a **Key Administrator**  
  _Administrators can manage key settings but cannot use the key for encryption._
- I also added the **same IAM Admin user** as a **Key User**  
  _Key users can encrypt and decrypt resources using this key._

Clicked **Next** to proceed.



### 📋 Reviewed and Finalized the Key

- I reviewed the automatically generated **key policy** to ensure appropriate permissions were applied.
- Clicked **Finish** to create the key.



I successfully created a **Customer-Managed Key** named `MY-kms-key`, which will be used in the next steps to encrypt my DynamoDB table using server-side encryption with AWS KMS.


![Create Customer-Managed KMS Key Screenshot](https://github.com/addula-mounika12/Encrypt-Data-at-Rest-with-AWS-KMS/blob/9a9727749cc379fec6e7c0f59f92fe0dbd2b5405/assets/Screenshot%202025-07-02%20124620.png) 

---

## Step 2: Create and Encrypt DynamoDB Table Using KMS

In this step, I created a **DynamoDB table** and enabled encryption at rest using the **customer-managed KMS key** created earlier. This ensures that all data stored in the table is encrypted and only accessible by authorized users.




#### ♻️ Created DynamoDB Table

1. Opened the **AWS Management Console** and navigated to the **DynamoDB** service.
2. Clicked **Create Table**.
3. Set the **Table name** to: `my_table_kms`
4. Defined a **Partition key**:
   - Attribute name: `id`
   - Type: `String`
5. Kept all other settings as default.
6. Clicked **Create Table** and waited for the status to change to **Active**.

![DynamoDB Table Active Screenshot](https://github.com/addula-mounika12/Encrypt-Data-at-Rest-with-AWS-KMS/blob/c7785127d9b32228c23618410756944a0cd801c1/assets/Screenshot%202025-07-02%20160332.png) 



### 🔒 Encrypted Table with Customer-Managed KMS Key

1. Once the table was active, I selected it from the DynamoDB console.
2. Clicked the **Additional settings** tab.
3. Scrolled down to the **Encryption** section and clicked **Manage encryption**.
4. Selected the option: **Stored in your account, and owned and managed by you**.
5. From the dropdown, I selected the KMS key I created earlier: `nextwork-kms-key`.
6. Clicked **Save changes**.



- The **DynamoDB table** is now encrypted using a **customer-managed KMS key**.
- All data at rest in the table is encrypted with this key.
Access to encryption and decryption is controlled via IAM and the KMS key policy.

![DynamoDB Table Encrypted with KMS Key Screenshot](https://github.com/addula-mounika12/Encrypt-Data-at-Rest-with-AWS-KMS/blob/2c938c8e20d81980d95b6a69943f388e30963698/assets/Screenshot%202025-07-02%20161836.png) 


---



## Step 3: Add Data to Your Encrypted DynamoDB Table

In this step, I added data to the DynamoDB table that I previously encrypted using AWS KMS. The goal was to test whether I, as an authorized user, could interact with encrypted data and confirm that encryption at rest was functioning transparently.


#### 1. Add an Item to DynamoDB

- Opened the **AWS Management Console** and navigated to the **DynamoDB** table.
- Selected the table I created (`my_table_kms`).
- Clicked **Actions > Create item**.
- Entered a simple item:
  - Attribute: `id`
  - Value: `1`
- Clicked **Create item** to save.

#### 2. Explore Items

- From the left navigation, selected **Explore items**.
- Refreshed the tab.
- Confirmed that the item I added was visible in the list.


### 🔐 Why Could I See the Encrypted Data?

Although the data is encrypted at rest using a **customer-managed KMS key**, I was able to view the item because:

- I am an **authorized key user**.
- **AWS DynamoDB** uses **transparent data encryption**, meaning:
    Data is automatically decrypted for users with valid KMS permissions.
    Encryption and decryption occur behind the scenes with no extra steps needed from the developer.

Even though the DynamoDB table is encrypted, I could see the items because DynamoDB securely decrypts the data for authorized users._

![Added Item in DynamoDB Table Screenshot](https://github.com/addula-mounika12/Encrypt-Data-at-Rest-with-AWS-KMS/blob/7b93c32d110cef8284f196efac8916090597dd90/assets/Screenshot%202025-07-02%20162719.png)



---


## 🧑‍💼 Step #4: Created a Test User

To validate encryption and access control, I created a test IAM user **without** permissions to use the AWS KMS key. This simulated a real-world scenario where different users have different access levels.

In this step, I created a test user that did not have permission to encrypt or decrypt using the KMS key. This helped validate whether encrypted data could still be accessed by unauthorized users.


### 👤 Created the IAM User

1. Opened the **IAM Console** in the AWS Management Console.
2. From the left navigation pane, clicked **Users**.
3. Clicked **Create user**.
4. Entered a user name: `mounika`.
5. Checked the box: **Provide user access to the AWS Management Console - optional**.
6. Unchecked: **Users must create a new password at the next sign-in** (for faster login testing).
7. Clicked **Next**.
8. Selected **Attach existing policies directly**.
9. Attached the **AmazonDynamoDBFullAccess** policy.
10. Clicked **Next**, then **Create user**.
11. **Downloaded the `.csv` file** containing user credentials. This was the only time the password was visible.



### 🔐 Permissions Summary

I configured a new IAM user to test access restrictions on encrypted data. The permission policy I granted this user was **AmazonDynamoDBFullAccess**, but I did **not** assign any permissions related to encryption or decryption using AWS KMS.

This configuration ensured that while the user could fully interact with DynamoDB, they could not decrypt the table’s contents due to a lack of access to the KMS key.


### Observed Behavior

Once logged in as this test user, I attempted to read data from the encrypted DynamoDB table. As expected, the attempt resulted in an access error due to insufficient permissions to use the encryption key.

![Test User Setup Screenshot](https://github.com/addula-mounika12/Encrypt-Data-at-Rest-with-AWS-KMS/blob/5f15e50d7a7080e6c08beddb5f6b0b165839e926/assets/Screenshot%202025-07-02%20171707.png)



---



## Step #5: Granted KMS Access to Test User

To complete the encryption access test, I granted my test IAM user permission to use the KMS key I created earlier. This validated that once a user is added as a **key user**, they can encrypt and decrypt data stored in DynamoDB.


### 🔐 Updated the Key Policy

I returned to the **KMS Console** while logged in as the IAM Admin user.

1. Navigated to **Key Management Service (KMS)**.
2. Opened the **Customer managed keys** section.
3. Selected the key named `nextwork-kms-key`.
4. Under the **Key policy** tab, I clicked **Add** next to Key users.
5. Selected `nextwork-kms-user` as the new key user.
6. Clicked **Add** to update the key policy.
7. Switched to **policy view** to inspect the permissions added.

The updated key policy granted the following actions:
- `kms:Encrypt`
- `kms:Decrypt`
- `kms:ReEncrypt*`
- `kms:GenerateDataKey*`
- `kms:DescribeKey`

These permissions confirmed that the test user could now perform encryption and decryption operations with this key.

![KMS Key User Policy Screenshot](https://github.com/addula-mounika12/Encrypt-Data-at-Rest-with-AWS-KMS/blob/98f83447850b53233f4f8c4ade7ef18f68207895/assets/Screenshot%202025-07-02%20172953.png)


### ✅ Verified Access as Test User

After updating the key policy, I switched to the **incognito browser session** where I had signed in as the test user.

- Refreshed the DynamoDB console.
- Navigated to the table containing encrypted items.
- Confirmed that the previously restricted data was now **visible** to the test user.

This confirmed that:
Granting **key user** permissions through KMS is a secure and effective method to authorize access to encrypted data in AWS.

### 📝 What This Demonstrated

By adding `nextwork-kms-user` as a key user, I validated the effectiveness of **KMS-based encryption control**. Without this permission, the user could not decrypt data even though they had full DynamoDB access. Once added as a key user, decryption succeeded.

![Test User Successful Decryption Screenshot](https://github.com/addula-mounika12/Encrypt-Data-at-Rest-with-AWS-KMS/blob/52a41ecc94cd6be56e77e67e61628a333aecc357/assets/Screenshot%202025-07-02%20173442.png)




---



## 🧹 Delete Your Resources

Before wrapping up the project, I made sure to delete all resources I had provisioned. This ensured no unnecessary charges accumulated and maintained a clean AWS environment.

### Deleted the DynamoDB Table

I navigated to the **DynamoDB Console** using my IAM Admin credentials and followed these steps:

1. Selected **Tables** from the left-hand sidebar.
2. Chose the table named `nextwork-kms-table`.
3. Clicked **Delete**.

During the deletion:

- ✅ I kept the checkbox **Delete all CloudWatch alarms** selected to prevent extra charges.
- ❌ I left the **Create an on-demand backup** option unchecked, as no long-term backup was needed.

After typing `confirm` in the confirmation box, I completed the deletion.

### 🔐 Scheduled Deletion of the KMS Key

Next, I headed to the **KMS Console** and:

1. Selected **Customer-managed keys** from the sidebar.
2. Chose the `nextwork-kms-key`.
3. Clicked **Key actions** > **Schedule key deletion**.

- I set the **waiting period** to 7 days (the minimum allowed by AWS) to expedite cleanup.
- Checked the box to confirm key deletion.
- Clicked **Schedule deletion**.


### 👤 Deleted the Test IAM User

To remove the test IAM user:

1. Navigated to the **IAM Console** and selected **Users**.
2. Chose the user `nextwork-kms-user`.
3. Clicked **Delete**, entered the username to confirm, and finalized the deletion.

I also deleted the downloaded `.csv` file (`nextwork-kms-user_credentials.csv`) from my local machine to eliminate sensitive access details.


### ✅ Cleanup Complete

With the table, key, IAM user, and credentials removed, all project resources were successfully deleted. My AWS environment was left clean and ready for future projects.




