# Alibaba Cloud SMS Integration Guide

Log in to the Alibaba Cloud console and navigate to the "SMS Service" page: https://dysms.console.aliyun.com/overview

## Step 1: Add Signature
![Step](images/alisms/sms-01.png)
![Step](images/alisms/sms-02.png)

After completing the above steps, you will get a signature. Please write it to the control panel parameter: `aliyun.sms.sign_name`

## Step 2: Add Template
![Step](images/alisms/sms-11.png)

After completing the above steps, you will get a template code. Please write it to the control panel parameter: `aliyun.sms.sms_code_template_code`

Note: The signature needs to wait for 7 business days for operator registration to be successful before SMS can be sent successfully.

Note: The signature needs to wait for 7 business days for operator registration to be successful before SMS can be sent successfully.

Note: The signature needs to wait for 7 business days for operator registration to be successful before SMS can be sent successfully.

You can wait until the registration is successful before continuing with the next steps.

## Step 3: Create SMS Account and Enable Permissions

Log in to the Alibaba Cloud console and navigate to the "Access Control" page: https://ram.console.aliyun.com/overview?activeTab=overview

![Step](images/alisms/sms-21.png)
![Step](images/alisms/sms-22.png)
![Step](images/alisms/sms-23.png)
![Step](images/alisms/sms-24.png)
![Step](images/alisms/sms-25.png)

After completing the above steps, you will get access_key_id and access_key_secret. Please write them to the control panel parameters: `aliyun.sms.access_key_id`, `aliyun.sms.access_key_secret`

## Step 4: Enable Mobile Registration Function

1. Normally, after filling in all the above information, you should see this effect. If not, you may have missed a step.

![Step](images/alisms/sms-31.png)

2. Enable non-admin user registration by setting the parameter `server.allow_user_register` to `true`

3. Enable mobile registration function by setting the parameter `server.enable_mobile_register` to `true`
![Step](images/alisms/sms-32.png)
