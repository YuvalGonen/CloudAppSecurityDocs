---
# required metadata

title: Connect Amazon Web Services with Cloud App Security
description: This article provides information about how to connect your AWS app to Cloud App Security using the API connector for visibility and control over use.
keywords:
author: shsagir
ms.author: shsagir
manager: shsagir
ms.date: 06/24/2020
ms.topic: how-to
ms.collection: M365-security-compliance
ms.prod:
ms.service: cloud-app-security
ms.technology:

# optional metadata

#ROBOTS:
#audience:
#ms.devlang:
ms.reviewer: reutam
ms.suite: ems
#ms.tgt_pltfrm:
ms.custom: seodec18

---
# Connect AWS to Microsoft Cloud App Security

[!INCLUDE [Banner for top of topics](includes/banner.md)]

This article provides instructions for connecting your existing Amazon Web Services (AWS) account to Microsoft Cloud App Security using the connector APIs. For information about how Cloud App Security protects AWS, see [Protect AWS](protect-aws.md).

You can connect one or both of the following AWS to Cloud App Security connections:

- **Security auditing**: This connection gives you visibility into and control over AWS app use.
- **Security configuration**: This connection gives you fundamental security recommendations based on the Center for Internet Security (CIS) benchmark for AWS.

Since you can add either or both of the connections, the steps in this article are written as independent instructions. If you have already added one of the connections, where relevant edit the existing configurations.

## How to connect AWS Security auditing to Cloud App Security

Use the following steps to configure your AWS auditing and then connect it to Cloud App Security.

### Step 1: Configure Amazon Web Services auditing

1. In your [Amazon Web Services console](https://console.aws.amazon.com/), under **Security, Identity & Compliance**, click **IAM**.

    ![AWS identity and access](media/aws-identity-and-access.png "AWS identity and access")

1. Select **Users** and then click **Add user**.

    ![AWS users](media/aws-users.png "AWS users")

1. In the **Details** step, provide a new user name for Cloud App Security. Make sure that under **Access type** you select **Programmatic access** and click **Next Permissions**.<a name="set-permissions"></a>

    ![Create user in AWS](media/aws-create-user.png "Create user in AWS")

1. Click on the **JSON** tab:

    ![AWS JSON tab](media/aws-json.png "AWS JSON tab")

1. Paste the following script into the provided area:

    ```json
    {
      "Version" : "2012-10-17",
      "Statement" : [{
          "Action" : [
            "cloudtrail:DescribeTrails",
            "cloudtrail:LookupEvents",
            "cloudtrail:GetTrailStatus",
            "cloudwatch:Describe*",
            "cloudwatch:Get*",
            "cloudwatch:List*",
            "iam:List*",
            "iam:Get*",
            "s3:ListAllMyBuckets",
            "s3:PutBucketAcl",
            "s3:GetBucketAcl",
            "s3:GetBucketLocation"
          ],
          "Effect" : "Allow",
          "Resource" : "*"
        }
      ]
     }
    ```

     ![AWS code](media/aws-code.png "AWS code")

1. Click **Review policy**.

1. Provide a **Name** and click **Create policy**.

    ![Provide AWS policy name](media/aws-create-policy.png "Provide AWS policy name")

1. Back in the **Add user** screen, refresh the list if necessary, and select the user you created, and click **Next Review**.

    ![Attach existing policy in AWS](media/aws-attach-policy.png "Attach existing policy in AWS")

1. If all the details are correct, click **Create user**.

    ![User permissions in AWS](media/aws-user-permissions.png "Review user permissions in AWS")

1. When you get the success message, click **Download .csv** to save a copy of the new user's credentials, you need these later.

    ![Download csv in AWS](media/aws-download-csv.png "Download csv in AWS")

1. In the AWS console, click **Services** and then under **Management Tools** click **CloudTrail**.

    ![AWS CloudTrail](media/aws-cloudtrail.png "AWS CloudTrail")

    If you haven't used CloudTrail before, click **Get Started** and set it up by providing a name and selecting the appropriate S3 bucket and click **Turn On**. To make sure you have complete coverage, set **Apply to all regions** to **Yes**.

    ![Turn on CloudTrail in AWS](media/aws-turnon-cloudtrail.png "Turn on CloudTrail in AWS")

    You should see the new CloudTrail name in the **Trails** list.

    ![CloudTrail list in AWS](media/aws-cloudtrail-list.png "CloudTrail list in AWS")

    > [!NOTE]
    > After connecting AWS, you'll receive events for seven days prior to connection. If you just enabled CloudTrail, you'll receive events from the time you enabled CloudTrail.

### Step 2: Connect Amazon Web Services auditing to Cloud App Security

1. In the Cloud App Security portal, click **Investigate** and then **Connected apps**.

1. In the **App connectors** page, to provide the AWS connector credentials, do one of the following:

    **For a new connector**

    1. Click the plus sign followed by **Amazon Web Services**.

        ![connect AWS auditing](media/connect-aws.png "connect AWS")

    1. In the pop-up, provide a name for the connector, and then click **Connect Amazon Web Services**.

        ![AWS auditing connector name](media/connect-aws-name.png)

    1. On the Connect Amazon Web services page, select **Security auditing**, paste the **Access key** and **Secret key** from the .csv file into the relevant fields, and click **Connect**.

        ![Connect AWS app security auditing for new connector](media/aws-connect-app-audit.png "Connect AWS app security auditing")

    **For an existing connector**

    1. In the list of connectors, on the row in which the AWS connector appears, click **Connect security auditing**.

        ![Screenshot of the Connected Apps page, showing edit Security Auditing link](media/aws-connect-app-edit-audit.png)

    1. On the Connect Amazon Web Services page, paste the **Access key** and **Secret key** from the .csv file into the relevant fields, and click **Connect**.

        ![Connect AWS app security auditing for existing connector](media/aws-connect-app-edit-audit-creds.png "Connect AWS app security auditing")

1. Click **Test API** to make sure the connection succeeded.

    Testing may take a couple of minutes. When it's finished, you get a success or failure notification. After receiving a success notice, click **Done**.

## How to connect AWS security configuration to Cloud App Security

Connecting AWS security configuration gives you insights into fundamental security recommendations based on the Center for Internet Security (CIS) benchmark for AWS.

Follow these steps to connect AWS security configuration to Cloud App Security.

> [!div class="checklist"]
>
> - [Set up AWS Security Hub](#set-up-aws-security-hub)
> - [Connect AWS security configuration to Cloud App Security](#connect-aws-security-configuration-to-cloud-app-security)

### Set up AWS Security Hub

To view security recommendations for multiple regions, repeat the following steps for each relevant region.

> [!NOTE]
> If you are using a master account, repeat these steps to configure the master account and all connected member accounts across all relevant regions.

1. Enable [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
1. Enable [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
1. Verify that there is data flowing to the Security Hub.

    > [!NOTE]
    > When you first enable Security Hub, it may take several hours for data to be available.

### Connect AWS Security configuration to Cloud App Security

Before you can connect AWS security configuration, make sure that you have [set up your AWS environment](#set-up-aws-security-hub) to collect fundamental security and compliance recommendations.

> [!NOTE]
> If you are using an [AWS master account](https://aws.amazon.com/security-hub/faqs/), use the following steps to connect the master account. Connecting your master account allows you to receive recommendations for all member accounts across all regions.

### Step 1: Configure Amazon Web Services security configuration

1. Follow the *How to connect AWS Security auditing* steps to get to the [permissions](#set-permissions) page.

1. On the permissions page, click **Attach existing policies directly**, apply the **AWSSecurityHubReadOnlyAccess** and **SecurityAudit** policies, and then click **Next Tags**.

    ![Attach existing policy in AWS](media/aws-attach-policy.png "Attach existing policy in AWS")

1. Optional: Add tags to the user.

    ![Add tags to user in AWS](media/aws-add-tags.png)

    > [!NOTE]
    > Adding tags to the user won't affect the connection.

1. Click **Next Review**.

1. If all the details are correct, click **Create user**.

    ![User permissions in AWS](media/aws-user-permissions.png "Review user permissions in AWS")

1. When you get the success message, click **Download .csv** to save a copy of the **Access key ID** and the **Secret access key**, you need these later.

    ![Download csv in AWS](media/aws-download-csv.png "Download csv in AWS")

### Step 2: Connect Amazon Web Services security configuration to Cloud App Security

1. In Cloud App Security, click **Investigate**, and then select **Connected apps**.

1. In the **Security configuration apps** tab, click the plus button, and then select **Amazon Web Services**.

    ![connect AWS security configuration](media/connect-aws-security-configuration.png)

1. In the **Instance name** page, choose the instance type, and then click **Next**.

    - For an existing connector, choose the relevant instance.

        ![AWS instance selection](media/connect-aws-existing-instance.png)

    - For a new connector, provide a name for the instance.

        ![AWS security configuration connector name](media/aws-connect-name.png)

1. In the **Account details** page, paste the **Access key** and **Secret key** from the .csv file into the relevant fields, and then click **Next**.

    ![Connect AWS account details](media/aws-connect-account-details.png)

1. In the **Finished** page, make sure the connection succeeded, and then click **Finished**.

If you have any problems connecting the app, see [Troubleshooting App Connectors](troubleshooting-api-connectors-using-error-messages.md).

## Next steps

> [!div class="nextstepaction"]
> [Control cloud apps with policies](control-cloud-apps-with-policies.md)

[!INCLUDE [Open support ticket](includes/support.md)]
