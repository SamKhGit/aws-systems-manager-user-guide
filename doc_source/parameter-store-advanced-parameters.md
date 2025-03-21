# Managing parameter tiers<a name="parameter-store-advanced-parameters"></a>

Parameter Store, a capability of AWS Systems Manager, includes *standard parameters * and *advanced parameters*\. You individually configure parameters to use either the standard\-parameter tier \(the default tier\) or the advanced\-parameter tier\. 

You can change a standard parameter to an advanced parameter at any time, but you can’t revert an advanced parameter to a standard parameter\. This is because reverting an advanced parameter to a standard parameter would cause the system would truncate the size of the parameter from 8 KB to 4 KB, resulting in data loss\. Reverting would also remove any policies attached to the parameter\. Also, advanced parameters use a different form of encryption than standard parameters\. For more information, see [How AWS Systems Manager Parameter Store Uses AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/services-parameter-store.html) in the *AWS Key Management Service Developer Guide*\.

If you no longer need an advanced parameter, or if you no longer want to incur charges for an advanced parameter, delete it and recreate it as a new standard parameter\. 

The following table describes the differences between the tiers\.


****  

|  | Standard | Advanced | 
| --- | --- | --- | 
|  Total number of parameters allowed \(per AWS account and AWS Region\)  |  10,000  |  100,000  | 
|  Maximum size of a parameter value  |  4 KB  |  8 KB  | 
|  Parameter policies available  |  No  |  Yes For more information, see [Assigning parameter policies](parameter-store-policies.md)\.  | 
|  Cost  |  No additional charge  |  Charges apply For more information, see [AWS Systems Manager Pricing](https://aws.amazon.com/systems-manager/pricing/)\.  | 

**Topics**
+ [Specifying a default parameter tier](#ps-default-tier)
+ [Changing a standard parameter to an advanced parameter](#parameter-store-advanced-parameters-enabling)

## Specifying a default parameter tier<a name="ps-default-tier"></a>

In requests to create or update a parameter \(that is, the `[PutParameter](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_PutParameter.html)` operation\), you can specify the parameter tier to use in the request\. The following is an example, using the AWS Command Line Interface \(AWS CLI\)\.

------
#### [ Linux & macOS ]

```
aws ssm put-parameter \
    --name "default-ami" \
    --type "String" \
    --value "t2.micro" \
    --tier "Standard"
```

------
#### [ Windows ]

```
aws ssm put-parameter ^
    --name "default-ami" ^
    --type "String" ^
    --value "t2.micro" ^
    --tier "Standard"
```

------

Whenever you specify a tier in the request, Parameter Store creates or updates the parameter according to your request\. However, if you don't explicitly specify a tier in a request, the Parameter Store default tier setting determines which tier the parameter is created in\.

The default tier when you begin using Parameter Store is the standard\-parameter tier\. If you use the advanced\-parameter tier, you can specify one of the following as the default:
+ **Advanced**: With this option, Parameter Store evaluates all requests as advanced parameters\. 
+ **Intelligent\-Tiering**: With this option, Parameter Store evaluates each request to determine if the parameter is standard or advanced\. 

  If the request doesn't include any options that require an advanced parameter, the parameter is created in the standard\-parameter tier\. If one or more options requiring an advanced parameter are included in the request, Parameter Store creates a parameter in the advanced\-parameter tier\.

**Benefits of Intelligent\-Tiering**  
The following are reasons you might choose Intelligent\-Tiering as the default tier\.

**Cost control** – Intelligent\-Tiering helps control your parameter\-related costs by always creating standard parameters unless an advanced parameter is absolutely necessary\. 

**Automatic upgrade to the advanced\-parameter tier** – When you make a change to your code that requires upgrading a standard parameter to an advanced parameter, Intelligent\-Tiering handles the conversion for you\. You don't need to change your code to handle the upgrade\.

Here are some examples of automatic upgrades:
+ Your AWS CloudFormation templates provision numerous parameters when they're run\. When this process causes you to reach the 10,000 parameter limit in the standard\-parameter tier, Intelligent\-Tiering automatically upgrades you to the advanced\-parameter tier, and your AWS CloudFormation processes aren't interrupted\.
+ You store a certificate value in a parameter, rotate the certificate value regularly, and the content is less than the 4 KB limit of the standard\-parameter tier\. If a replacement certificate value exceeds 4 KB, Intelligent\-Tiering automatically upgrades the parameter to the advanced\-parameter tier\.
+ You want to associate numerous existing standard parameters to a parameter policy, which requires the advanced\-parameter tier\. Instead of your having to include the option `--tier Advanced` in all the calls to update the parameters, Intelligent\-Tiering automatically upgrades the parameters to the advanced\-parameter tier\. The Intelligent\-Tiering option upgrades parameters from standard to advanced whenever criteria for the advanced\-parameter tier are introduced\.

Options that require an advanced parameter include the following:
+ The content size of the parameter is more than 4 KB\.
+ The parameter uses a parameter policy\.
+ More than 10,000 parameters already exist in your AWS account in the current AWS Region\.

**Default Tier Options**  
The tier options you can specify as the default include the following\.
+ **Standard** – The standard\-parameter tier is the default tier when you begin to use Parameter Store\. Using the standard\-parameter tier, you can create 10,000 parameters for each AWS Region in an AWS account\. The content size of each parameter can equal a maximum of 4 KB\. Standard parameters don't support parameter policies\. There is no additional charge to use the standard\-parameter tier\. Choosing **Standard** as the default tier means that Parameter Store always attempts to create a standard parameter for requests that don't specify a tier\. 
+ **Advanced** – Use the advanced\-parameter tier to create a maximum of 100,000 parameters for each AWS Region in an AWS account\. The content size of each parameter can equal a maximum of 8 KB\. Advanced parameters support parameter policies\. There is a charge to use the advanced\-parameter tier\. For more information, see [AWS Systems Manager Pricing](https://aws.amazon.com/systems-manager/pricing/)\. Choosing **Advanced** as the default tier means that Parameter Store always attempts to create an advanced parameter for requests that don't specify a tier\.
**Note**  
When you choose the advanced\-parameter tier, explicitly authorize AWS to charge your account for any advanced parameters you create\.
+ **Intelligent\-Tiering **– With the Intelligent\-Tiering option, Parameter Store determines whether to use the standard\-parameter tier or advanced\-parameter tier based on the content of the request\. For example, if you run a command to create a parameter with content under 4 KB, and there are fewer than 10,000 parameters in the current AWS Region in your AWS account, and you don't specify a parameter policy, a standard parameter is created\. If you run a command to create a parameter with more than 4 KB of content, you already have more than 10,000 parameters in the current AWS Region in your AWS account, or you specify a parameter policy, an advanced parameter is created\. 
**Note**  
When you choose Intelligent\-Tiering, explicitly authorize AWS to charge your account for any advanced parameters you created\. 

You can change the Parameter Store default tier setting at any time\.

### Configuring permissions to specify a Parameter Store default tier<a name="parameter-store-tier-permissions"></a>

Verify that you have permission in AWS Identity and Access Management \(IAM\) to change the default parameter tier in Parameter Store by doing one of the following:
+ Make sure that you attach the `AdministratorAccess` policy to your IAM user, group, or role\.
+ Make sure that you have permission to change the default tier setting by using the following API operations:
  + [GetServiceSetting](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_GetServiceSetting.html)
  + [UpdateServiceSetting](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_UpdateServiceSetting.html)
  + [ResetServiceSetting](https://docs.aws.amazon.com/systems-manager/latest/APIReference/API_ResetServiceSetting.html)

Use the following procedure to add an inline IAM policy to a user account\. This policy allows a user to view and change the default tier setting for parameters in a specific AWS Region in an AWS account\. 

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane, choose **Users**\.

1. In the list, choose the name of the user to attach a policy to\.

1. Choose the **Permissions** tab\.

1. On the right side of the page, under **Permission policies**, choose **Add inline policy**\. 

1. Choose the **JSON** tab\.

1. Replace the default content with the following:

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "ssm:GetServiceSetting"                
               ],
               "Resource": "*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "ssm:UpdateServiceSetting"
               ],
               "Resource": "arn:aws:ssm:region:account-id:servicesetting/ssm/parameter-store/default-parameter-tier"
           }
       ]
   }
   ```

1. Choose **Review policy**\.

1. On the **Review policy** page, for **Name**, enter a name for the inline policy, such as **Parameter\-Store\-Default\-Tier** or another name you prefer\.

1. Choose **Create policy**\.

Administrators can specify read\-only permission by assigning the following inline policy to the user's account\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ssm:GetServiceSetting"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Deny",
            "Action": [
                "ssm:ResetServiceSetting",
                "ssm:UpdateServiceSetting"
            ],
            "Resource": "*"
        }
    ]
}
```

For more information about creating and editing IAM policies, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html) in the *IAM User Guide*\.

### Specifying or changing the Parameter Store default tier \(console\)<a name="parameter-store-tier-changing"></a>

The following procedure shows how to use the Systems Manager console to specify or change the default parameter tier for the current AWS account and AWS Region\. 

**Tip**  
If you haven't created a parameter yet, you can use the AWS Command Line Interface \(AWS CLI\) or AWS Tools for Windows PowerShell to change the default parameter tier\. For information, see [Specifying or changing the Parameter Store default tier \(AWS CLI\)](#parameter-store-tier-changing-cli) and [Specifying or changing the Parameter Store default tier \(PowerShell\)](#parameter-store-tier-changing-ps)\.

**To specify or change the Parameter Store default tier**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Parameter Store**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Parameter Store**\.

1. Choose the **Settings** tab\.

1. Choose **Change default tier**\.

1. Choose one of the following options\.
   + **Standard**
   + **Advanced**
   + **Intelligent\-Tiering**

   For information about these options, see [Specifying a default parameter tier](#ps-default-tier)\.

1. Review the message, and choose **Confirm**\.

If you want to change the default tier setting later, repeat this procedure and specify a different default tier option\.

### Specifying or changing the Parameter Store default tier \(AWS CLI\)<a name="parameter-store-tier-changing-cli"></a>

The following procedure shows how to use the AWS CLI to change the default parameter tier setting for the current AWS account and AWS Region\.

**To specify or change the Parameter Store default tier using the AWS CLI**

1. Open the AWS CLI and run the following command to change the default parameter tier setting for a specific AWS Region in an AWS account\.

   ```
   aws ssm update-service-setting --setting-id arn:aws:ssm:region:account-id:servicesetting/ssm/parameter-store/default-parameter-tier --setting-value tier-option
   ```

   *region* represents the identifier for an AWS Region supported by AWS Systems Manager, such as `us-east-2` for the US East \(Ohio\) Region\. For a list of supported *region* values, see the **Region** column in [Systems Manager service endpoints](https://docs.aws.amazon.com/general/latest/gr/ssm.html#ssm_region) in the *Amazon Web Services General Reference*\.

   *tier\-option* values include `Standard`, `Advanced`, and `Intelligent-Tiering`\. For information about these options, see [Specifying a default parameter tier](#ps-default-tier)\.

   There is no output if the command succeeds\.

1. Run the following command to view the current throughput service settings for Parameter Store in the current AWS account and AWS Region\.

   ```
   aws ssm get-service-setting --setting-id arn:aws:ssm:region:account-id:servicesetting/ssm/parameter-store/default-parameter-tier
   ```

   The system returns information similar to the following\.

   ```
   {
       "ServiceSetting": {
           "SettingId": "/ssm/parameter-store/default-parameter-tier",
           "SettingValue": "Advanced",
           "LastModifiedDate": 1556551683.923,
           "LastModifiedUser": "arn:aws:sts::123456789012:assumed-role/Administrator/Jasper",
           "ARN": "arn:aws:ssm:us-east-2:123456789012:servicesetting/ssm/parameter-store/default-parameter-tier",
           "Status": "Customized"
       }
   }
   ```

If you want to change the default tier setting again, repeat this procedure and specify a different `SettingValue` option\.

### Specifying or changing the Parameter Store default tier \(PowerShell\)<a name="parameter-store-tier-changing-ps"></a>

The following procedure shows how to use the Tools for Windows PowerShell to change the default parameter tier setting for a specific AWS Region in an Amazon Web Services account\.

**To specify or change the Parameter Store default tier using PowerShell**

1. Change the Parameter Store default tier in the current AWS account and AWS Region using the AWS Tools for PowerShell \(Tools for PowerShell\)\.

   ```
   Update-SSMServiceSetting -SettingId "arn:aws:ssm:region:account-id:servicesetting/ssm/parameter-store/default-parameter-tier" -SettingValue "tier-option" -Region region
   ```

   *region* represents the identifier for an AWS Region supported by AWS Systems Manager, such as `us-east-2` for the US East \(Ohio\) Region\. For a list of supported *region* values, see the **Region** column in [Systems Manager service endpoints](https://docs.aws.amazon.com/general/latest/gr/ssm.html#ssm_region) in the *Amazon Web Services General Reference*\.

   *tier\-option* values include `Standard`, `Advanced`, and `Intelligent-Tiering`\. For information about these options, see [Specifying a default parameter tier](#ps-default-tier)\.

   There is no output if the command succeeds\.

1. Run the following command to view the current throughput service settings for Parameter Store in the current AWS account and AWS Region\.

   ```
   Get-SSMServiceSetting -SettingId "arn:aws:ssm:region:account-id:servicesetting/ssm/parameter-store/default-parameter-tier" -Region region
   ```

   *region* represents the identifier for an AWS Region supported by AWS Systems Manager, such as `us-east-2` for the US East \(Ohio\) Region\. For a list of supported *region* values, see the **Region** column in [Systems Manager service endpoints](https://docs.aws.amazon.com/general/latest/gr/ssm.html#ssm_region) in the *Amazon Web Services General Reference*\.

   The system returns information similar to the following\.

   ```
   ARN              : arn:aws:ssm:us-east-2:123456789012:servicesetting/ssm/parameter-store/default-parameter-tier
   LastModifiedDate : 4/29/2019 3:35:44 PM
   LastModifiedUser : arn:aws:sts::123456789012:assumed-role/Administrator/Jasper
   SettingId        : /ssm/parameter-store/default-parameter-tier
   SettingValue     : Advanced
   Status           : Customized
   ```

If you want to change the default tier setting again, repeat this procedure and specify a different `SettingValue` option\.

## Changing a standard parameter to an advanced parameter<a name="parameter-store-advanced-parameters-enabling"></a>

Use the following procedure to change an existing standard parameter to an advanced parameter\. For information about how to create a new advanced parameter, see [Creating Systems Manager parameters](sysman-paramstore-su-create.md)\.

**To change a standard parameter to an advanced parameter**

1. Open the AWS Systems Manager console at [https://console\.aws\.amazon\.com/systems\-manager/](https://console.aws.amazon.com/systems-manager/)\.

1. In the navigation pane, choose **Parameter Store**\.

   \-or\-

   If the AWS Systems Manager home page opens first, choose the menu icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/systems-manager/latest/userguide/images/menu-icon-small.png)\) to open the navigation pane, and then choose **Parameter Store**\.

1. Choose a parameter, and then choose **Edit**\.

1. For **Description**, enter information about this parameter\.

1. Choose **Advanced**\.

1. For **Value**, enter the value of this parameter\. Advanced parameters have a maximum value limit of 8 KB\.

1. Choose **Save changes**\.