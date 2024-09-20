# cloudformation-backup-template
AWS Backup daily CloudFormation for all resources with specified tag,this template effectively sets up a daily backup solution for resources tagged with "backup": "daily" using AWS Backup with encryption and a dedicated IAM role.
The provided CloudFormation template is well-structured and configures a daily backup solution for AWS resources using AWS Backup. Here's a breakdown of the resources it defines:

## 1. Backup Plan (BackupPlan):

Creates a backup plan named "default" that defines the schedule and rules for backups.
Includes a single backup rule named "daily-backups" that runs daily at 5:00 AM (cron(0 15 ? * * *)).
Specifies a StartWindowMinutes of 60, allowing the backup to start within 60 minutes of the scheduled time. moves backup to cold storage aws managed 
Sets a Lifecycle with DeleteAfterDays of 91, meaning backups are automatically deleted after 91 days.

## 2. Backup Vault (BackupVault):

Creates a backup vault named "default" to store the backed-up data.
Enforces encryption for the vault by referencing the BackupVaultKey.Arn in the EncryptionKeyArn property.

## 3. Backup Vault Key (BackupVaultKey):

Creates a KMS key named after its description ("Encryption key for daily").
Enables the key for use with the backup vault.
Defines a key policy allowing the AWS root user to perform all KMS actions on the key.

## 4. Backup Vault Key Alias (BackupVaultKeyAlias):

Creates an alias named "alias/cmk/backup" that points to the BackupVaultKey.
This alias can be used for easier reference and management.
## 5. Backup Role (BackupRole):

Creates an IAM role named "BackupRole".
Defines an AssumeRolePolicyDocument that allows the AWS Backup service (backup.amazonaws.com) to assume the role.
Attaches the managed policy arn:aws:iam::aws:policy/service-role/AWSBackupServiceRolePolicyForBackup, which grants the role necessary permissions to perform backup operations.

## 6. Backup Selection (BackupSelection):

Creates a backup selection named "daily-backup-tag".
References the BackupRole.Arn for IAM permissions.
Defines a ListOfTags filter with a single tag condition:
ConditionType: STRINGEQUALS
ConditionKey: backup
ConditionValue: daily

## This tag selection ensures only resources with the tag "backup": "daily" will be included in the daily backup rule.
Links this selection to the BackupPlan using the BackupPlanId property.

