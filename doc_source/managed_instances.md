# Managed Instances<a name="managed_instances"></a>

A *managed instance* is any machine configured for AWS Systems Manager\. You can configure Amazon Elastic Compute Cloud \(Amazon EC2\) instances or on\-premises machines in a hybrid environment as managed instances\. Systems Manager supports various distributions of Linux, including Raspberry Pi devices, macOS, and Microsoft Windows Server\.

**Note**  
In the Systems Manager console, any machine prefixed with "mi\-" is an on\-premises server or virtual machine \(VM\) managed instance\. 

AWS Systems Manager offers a standard\-instances tier and an advanced\-instances tier for servers and VMs in your hybrid environment\. The standard\-instances tier allows you to register a maximum of 1,000 servers or VMs per AWS account per AWS Region\. If you need to register more than 1,000 servers or VMs in a single account and Region, then use the advanced\-instances tier\. You can create as many instances as you like in the advanced\-instances tier, but all instances configured for Systems Manager are priced on a pay\-per\-use basis\. For more information about turning on advanced instances, see [Turning on the advanced\-instances tier](systems-manager-managedinstances-advanced.md)\. For more information about pricing, see [AWS Systems Manager Pricing](https://aws.amazon.com/systems-manager/pricing/)\.

**Note**  
Advanced instances also allow you to connect to your hybrid machines by using AWS Systems Manager Session Manager\. Session Manager provides interactive shell access to your instances\. For more information, see [AWS Systems Manager Session Manager](session-manager.md)\.
The standard\-instances quota also applies to EC2 instances that use a Systems Manager on\-premises activation \(which isn't a common scenario\)\.
To patch applications released by Microsoft on virtual machines \(VMs\) on\-premises instances, activate the advanced\-instances tier\. There is a charge to use the advanced\-instances tier\. There is no additional charge to patch applications released by Microsoft on Amazon Elastic Compute Cloud \(Amazon EC2\) instances\. For more information, see [About patching applications released by Microsoft on Windows Server](about-windows-app-patching.md)\.

**Display managed instances**  
If you don't see your managed instances listed in the console, then do the following:

1. Verify that the console is open in the AWS Region where you created your managed instances\. You can switch Regions by using the list in the top, right corner of the console\. 

1. Verify that your instances meet Systems Manager requirements\. For information, see [Systems Manager prerequisites](systems-manager-prereqs.md)\.

1. For servers and VMs in a hybrid environment, verify that you completed the activation process\. For more information, see [Setting up AWS Systems Manager for hybrid environments](systems-manager-managedinstances.md)\.

**Note**  
Systems Manager requires accurate time references in order to perform its operations\. If your instance's date and time aren't set correctly, they might not match the signature date of your API requests\. For more information, see [Use cases and best practices](systems-manager-best-practices.md)\.

**Note**  
When instance tags are created or edited, it takes up to an hour for the table filter to reflect those changes\.

**Verify Systems Manager support on an instance**  
AWS Config provides AWS Managed Rules, which are predefined, customizable rules that AWS Config uses to evaluate whether your AWS resource configurations comply with common best practices\. AWS Config Managed Rules include the [ec2\-instance\-managed\-by\-systems\-manager](https://docs.aws.amazon.com/config/latest/developerguide/ec2-instance-managed-by-ssm.html) rule\. This rule checks whether the Amazon EC2 instances in your account are managed by Systems Manager\. For more information, see [AWS Config Managed Rules](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config_use-managed-rules.html)\. 

**Verify Systems Manager prerequisites**  
For information about Systems Manager prerequisites, see [Systems Manager prerequisites](systems-manager-prereqs.md)\. For information about configuring on\-premises servers and VMs as managed instances, see [Setting up AWS Systems Manager for hybrid environments](systems-manager-managedinstances.md)\.

**Increase security posture on managed instances**  
For information about increasing your security posture against unauthorized root\-level commands on your instances, see [Restricting access to root\-level commands through SSM Agent](ssm-agent-restrict-root-level-commands.md)\.

**Deregister managed instances**  
You can deregister managed instances at any time\. For example, if you are managing multiple instances with the same AWS Identity and Access Management \(IAM\) role and you notice any kind of malicious behavior, you can deregister any number of machines at any point\. For information about deregistering managed instances, see [Deregistering managed instances in a hybrid environment](systems-manager-managed-instances-advanced-deregister.md)\.

**Topics**
+ [Configuring instance tiers](systems-manager-managed-instances-tiers.md)
+ [Resetting passwords on managed instances](managed-instances-password-reset.md)
+ [Deregistering managed instances in a hybrid environment](systems-manager-managed-instances-advanced-deregister.md)
+ [Troubleshooting Amazon EC2 managed instance availability](troubleshooting-managed-instances.md)