# Deleting and Restoring a Secret<a name="manage_delete-restore-secret"></a>

Because of the critical nature of secrets, AWS Secrets Manager intentionally makes deleting a secret a difficult thing to do\. Secrets cannot be *immediately* deleted\. Instead, they are immediately made inaccessible and scheduled for deletion after a recovery window of a *minimum* of seven days\. Until the recovery window ends, you can recover a secret that you previously deleted\.

You also cannot *directly* delete a version of a secret\. Instead, you remove all staging labels from the secret\. This marks the secret as deprecated and enables Secrets Manager to automatically delete the version in the background\.

This section includes procedures and commands that describe how to delete a secret and how to restore a deleted secret:
+ [Delete a secret with all versions](#proc-delete-secret)
+ [Restore a secret that is scheduled for deletion](#proc-restore-secret)
+ [Delete a version of a secret](#proc-delete-version)<a name="proc-delete-secret"></a>

**To delete a secret and all of its versions**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-delete-secret-console"></a>

When you delete a secret it is immediately deprecated but is not actually deleted until the number of days specified in the recovery window has gone by\. A deprecated secret cannot be accessed\. If you must access a secret that has been scheduled for deletion, you must restore the secret\. Then you can access the secret and its encrypted secret information\.

**Minimum permissions**  
To delete a secret in the console you must have these permissions:  
`secretsmanager:ListSecrets` – to navigate to the secret that you want to delete\.
`secretsmanager:DeleteSecret` – to deprecate the secret and schedule it for permanent deletion\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Navigate to the list of secrets that you currently manage in Secrets Manager, and choose the name of the secret that you want to delete\.

1. In the **Secret details** section, choose **Delete secret**\.

1. In the **Schedule secret deletion** dialog box, specify the number of days in the recovery window\. This represents the number of days that you want to wait before the deletion is permanent\. Secrets Manager attaches a field called `DeletionDate` and sets it to the current date and time plus the number of days specified for the recovery window\.

1. Choose **Schedule deletion**\.

1. If the option to show Deleted items is enabled in the console, then secret continues to display\. You can optionally choose to view the Deleted date field in the list\.

   1. Choose the **Preferences** icon \(the gear ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/preferences-gear.png)\) in the upper\-right corner\.

   1. Choose **Show secrets scheduled for deletion**\.

   1. Enable the switch for **Deleted on**\.

   1. Choose **Save**\.

   Your deleted secrets now appear in the list with the date on which you deleted each\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-delete-secret-api"></a>

You can use the following commands to retrieve a secret stored in AWS Secrets Manager:
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteSecret.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteSecret.html)
+ **CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-secret.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-secret.html)

You must identify the secret that you want to delete by its friendly name or ARN in the `SecretId` field\.

**Example**  
The following example of the CLI command deprecates the secret named "MyTestDatabase" and schedules it to be deleted after a recovery window of 14 days\.  

```
$ aws secretsmanager delete-secret --secret-id development/MyTestDatabase --recovery-window-in-days 14
{
    "SecretARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "SecretName": "development/MyTestDatabase",
    "DeletionDate": 1510089380.309
}
```
At any time after the date and time specified in the `DeletionDate` field, AWS Secrets Manager permanently deletes the secret\.

------<a name="proc-restore-secret"></a>

**To restore a secret that is scheduled for deletion**  
Follow the steps under one of the following tabs:

------
#### [ Using the console ]<a name="proc-restore-secret-console"></a>

A secret that is scheduled for deletion is considered deprecated and can no longer be directly accessed\. After the recovery window has passed, the Secrets Manager deletes the secret permanently\. Once deleted, it is not recoverable\. Before the end of the recovery window, you can recover the secret and make it accessible again\. This removes the `DeletionDate` field which cancels the scheduled permanent deletion\.

**Minimum permissions**  
To restore a secret and its metadata in the console you must have these permissions:  
`secretsmanager:ListSecrets` – to navigate to the secret that you want to restore\.
`secretsmanager:RestoreSecret` – to delete any versions that are still associated with the secret\.

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Navigate to the list of secrets that you currently manage in Secrets Manager\.

1. To view deleted secrets, you must enable it in the console\. If it is not already enabled, perform these steps:

1. 

   1. Choose the **Preferences** icon \(the gear ![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/preferences-gear.png)\) in the upper\-right corner\.

   1. Choose **Show secrets scheduled for deletion**\.

   1. Enable the switch for **Deleted on**\.

   1. Choose **Save**\.

   Your deleted secrets now appear in the list with the date on which you deleted each\.

1. Choose the name of the deleted secret that you want to restore\.

1. In the **Secret details** section, choose **Cancel deletion**\.

1. In the **Cancel secret deletion** confirmation dialog box, choose **Cancel deletion**\.

   AWS Secrets Manager removes the `DeletionDate` field from the secret which cancels the scheduled deletion and restores access to the secret\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-restore-secret-api"></a>

You can use the following commands to retrieve a secret stored in AWS Secrets Manager:
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RestoreSecret.html)
+ **CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/restore-secret.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/restore-secret.html)

You must identify the secret that you want to restore by its friendly name or ARN in the `SecretId` field\.

**Example**  
The following example of the CLI command restores a previously deleted secret named "MyTestDatabase" which cancels the scheduled deletion and restores access to the secret\.  

```
$ aws secretsmanager restore-secret --secret-id development/MyTestDatabase
{
    "SecretARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "SecretName": "development/MyTestDatabase"
}
```

------<a name="proc-delete-version"></a>

**To delete one version of a secret**  
Follow the steps under one of the following tabs:

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-delete-version-api"></a>

You cannot delete one version of a secret using the Secrets Manager console\. You must use the AWS CLI or AWS API\.

You can't directly delete a version of a secret\. Instead, you remove all its staging labels which effectively marks it as deprecated\. Secrets Manager can then delete it in the background\.

You can use the following commands to deprecate a version of a secret stored in AWS Secrets Manager:
+ **API/SDK:** [http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html](http://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetSecretValue.html)
+ **CLI:** [http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html](http://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-secret-value.html)

You must identify the secret by its friendly name or ARN\. You also specify the staging labels that you want to add, move, or remove\.

You can specify the FromSecretVersionId and the MoveToSecretId in the following combinations:
+ `FromSecretVersionId` only: This deletes staging labels completely from the specified version\. If the specified staging label is not present on that version, then an error occurs\.
+ `MoveToVerionId` only: This adds the staging labels to the specified version\. If any of the staging labels are already attached other versions, they are automatically removed from those versions\.
+ `MoveToVersionId` and `RemoveFromVersionId`: This explicitly moves a label\. The staging label must already be present on the `RemoveFromVersionId` version of the secret, or an error occurs\.

**Example**  
The following example of the CLI command removes the `AWSPREVIOUS` staging label from a version of the secret named "MyTestDatabase"\. You can retrieve the version ID of the version you want to delete by using the [ListSecretVersionIds](AWS Secrets Manager API ReferenceAPI_ListSecretVersionIds.html) command\.  

```
$ aws secretsmanager update-secret-version-stage \
        --secret-id development/MyTestDatabase \
        --from-secret-version-id EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE 
        --secret-version-stage AWSPREVIOUS
{
    "ARN": "arn:aws:secretsmanager:region:accountid:secret:development/MyTestDatabase-AbCdEf",
    "Name": "development/MyTestDatabase"
}
```

------