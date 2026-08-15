---
title: Centralize Backup Vault with AWS Backup
date: 2026-01-16 9:25:30 +0700
categories: [aws, backup, security, portofolio]
tags: [cloud, infrastructure, security, portofolio]     # TAG names should always be lowercase
---

![TEXT](/assets/img/post/aws-backup.jpeg)

AWS Control Tower now offers built-in capabilities to streamline your backup management at scale with direct AWS Backup integration. This capability automatically provisions a central backup vault in each AWS Region within a dedicated central backup account.

AWS Backup offers a powerful solution to these challenges with a centralized, fully-managed service that streamlines data protection at scale. Enterprises can leverage AWS Organizations alongside AWS Backup to implement automated, consistent backup policies across their entire cloud environment. The integration with AWS Control Tower further simplifies this process by enabling organizations to incorporate enterprise-wide backup management directly into their well-architected multi-account landing zone.

In this post, me demonstrate how to implement AWS Backup using AWS Control Tower integration. Me explore the architecture, prerequisites, and step-by-step implementation process. By following this guide, you’ll learn how to automatically deploy and manage backup policies across your organization, helping to meet compliance requirements, protect critical resources, and reduce administrative overhead.

## Architecture Overview

The high-level architecture consists of several AWS accounts managed through AWS Organizations and governed by AWS Control Tower.

A simplified architecture looks like this:

![TEXT](/assets/img/post/architecture-backup-centralize.png)

The important design principle is that backup data is separated from the workload accounts. If a production account is compromised, an attacker should not automatically have the same level of control over the backup environment.


## Prerequisites

Before implementing the AWS Control Tower and AWS Backup integration, make sure that you have the following foundational elements in place:

1. An existing AWS Organization governed by AWS Control Tower
2. Administrative access to the AWS Organizations management account
3. Enable AWS Backup in your AWS Organization
4. Enable AWS Backup Policy in your AWS Organization
5. Use custom Customer Managed Keys (CMK), and IAM roles for AWS Backup


## Step 1 — Create a Dedicated Backup Account

The first step is to create a dedicated AWS account for Centralize Backup infrastructure.
For example:
backup-prod

Purpose:
Centralized AWS Backup infrastructure

The Backup Account should not host normal application workloads.
Its primary purpose is to host:
1. AWS Backup
2. AWS Backup Vault (example-local-backup-vault)
3. AWS KMS CMK
4. Backup policies
5. Backup monitoring

## Step 2 — Create the Customer Managed Key For Source Account

The backup vault should be encrypted using an AWS KMS Customer Managed Key.

Example CMK for Source Account (alias/example-prod-workload-cmk):

This separation provides an additional security boundary.

```bash
{
  "Version": "2012-10-17",
  "Id": "cmk-workload-template",
  "Statement": [
    {
      "Sid": "EnableRootPermissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<SOURCE_ACCOUNT_ID>:root"
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "AllowEC2EBSUsage",
      "Effect": "Allow",
      "Principal": { "AWS": "*" },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "kms:CallerAccount": "<SOURCE_ACCOUNT_ID>",
          "kms:ViaService": "ec2.<REGION>.amazonaws.com"
        }
      }
    },
    {
      "Sid": "AllowEC2EBSGrant",
      "Effect": "Allow",
      "Principal": { "AWS": "*" },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "kms:CallerAccount": "<SOURCE_ACCOUNT_ID>",
          "kms:ViaService": "ec2.<REGION>.amazonaws.com"
        },
        "Bool": {
          "kms:GrantIsForAWSResource": "true"
        }
      }
    },
    {
      "Sid": "AllowRDSUsage",
      "Effect": "Allow",
      "Principal": { "AWS": "*" },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "kms:CallerAccount": "<SOURCE_ACCOUNT_ID>",
          "kms:ViaService": "rds.<REGION>.amazonaws.com"
        }
      }
    },
    {
      "Sid": "AllowRDSGrant",
      "Effect": "Allow",
      "Principal": { "AWS": "*" },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "kms:CallerAccount": "<SOURCE_ACCOUNT_ID>",
          "kms:ViaService": "rds.<REGION>.amazonaws.com"
        },
        "Bool": {
          "kms:GrantIsForAWSResource": "true"
        }
      }
    },
    {
      "Sid": "AllowEFSUsage",
      "Effect": "Allow",
      "Principal": { "AWS": "*" },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "kms:CallerAccount": "<SOURCE_ACCOUNT_ID>",
          "kms:ViaService": "elasticfilesystem.<REGION>.amazonaws.com"
        }
      }
    },
    {
      "Sid": "AllowEFSGrant",
      "Effect": "Allow",
      "Principal": { "AWS": "*" },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants"
      ],
      "Resource": "*",
      "Condition": {
        "StringEquals": {
          "kms:CallerAccount": "<SOURCE_ACCOUNT_ID>",
          "kms:ViaService": "elasticfilesystem.<REGION>.amazonaws.com"
        },
        "Bool": {
          "kms:GrantIsForAWSResource": "true"
        }
      }
    },
    {
      "Sid": "AllowAWSBackupSourceDescribeKey",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<SOURCE_ACCOUNT_ID>:role/aws-service-role/backup.amazonaws.com/AWSServiceRoleForBackup"
      },
      "Action": "kms:DescribeKey",
      "Resource": "*"
    },
    {
      "Sid": "AllowAWSBackupSourceCryptoOps",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<SOURCE_ACCOUNT_ID>:role/aws-service-role/backup.amazonaws.com/AWSServiceRoleForBackup"
      },
      "Action": [
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowAWSBackupSourceGrant",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<SOURCE_ACCOUNT_ID>:role/aws-service-role/backup.amazonaws.com/AWSServiceRoleForBackup"
      },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "kms:GrantIsForAWSResource": "true"
        }
      }
    },
    {
      "Sid": "AllowVaultAccountDecrypt",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<VAULT_ACCOUNT_ID>:role/aws-service-role/backup.amazonaws.com/AWSServiceRoleForBackup"
      },
      "Action": [
        "kms:DescribeKey",
        "kms:Decrypt",
        "kms:ReEncryptFrom",
        "kms:GenerateDataKey*"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowVaultAccountGrant",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<VAULT_ACCOUNT_ID>:role/aws-service-role/backup.amazonaws.com/AWSServiceRoleForBackup"
      },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants",
        "kms:RevokeGrant"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "kms:GrantIsForAWSResource": "true"
        }
      }
    }

  ]
}
```

## Step 3 — Create a Central Backup Vault

In the Backup Account, create a dedicated AWS Backup Vault.
Example:
CentralBackupVault

The vault becomes the central destination for recovery points created from workload accounts.
The backup vault should have a vault access policy that restricts which AWS accounts and IAM principals can perform operations against it. This is an important security control.

The goal is not simply to centralize the vault, but also to ensure that workload accounts cannot arbitrarily delete or manipulate centralized recovery points.

## Step 4 — Create the Customer Managed Key For Vault Account

Example CMK for Source Account (alias/example-prod-workload-cmk):

This separation provides an additional security boundary.

```bash
{
  "Version": "2012-10-17",
  "Id": "example-prod-backup-vault-cmk-policy",
  "Statement": [
    {
      "Sid": "EnableRootPermissions",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<VAULT_ACCOUNT_ID>:root"
      },
      "Action": "kms:*",
      "Resource": "*"
    },
    {
      "Sid": "AllowAWSBackupServiceUseOfKey",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<VAULT_ACCOUNT_ID>:role/aws-service-role/backup.amazonaws.com/AWSServiceRoleForBackup"
      },
      "Action": [
        "kms:Encrypt",
        "kms:Decrypt",
        "kms:ReEncrypt*",
        "kms:GenerateDataKey*",
        "kms:DescribeKey"
      ],
      "Resource": "*"
    },
    {
      "Sid": "AllowAWSBackupServiceToManageGrants",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<VAULT_ACCOUNT_ID>:role/aws-service-role/backup.amazonaws.com/AWSServiceRoleForBackup"
      },
      "Action": [
        "kms:CreateGrant",
        "kms:ListGrants",
        "kms:RevokeGrant"
      ],
      "Resource": "*",
      "Condition": {
        "Bool": {
          "kms:GrantIsForAWSResource": "true"
        }
      }
    }
  ]
}
```

## Step 5 — Configure AWS Backup Policies On AWS Organization

AWS Organizations backup policies allow backup configuration to be managed centrally.

A policy sample json : 


The exact retention period should be based on the organization's business and compliance requirements.

```bash
{
    "plans": {
        "example-prod-standard-daily-tiered-backup": {
            "regions": {
                "@@assign": [
                    "ap-southeast-1"
                ]
            },
            "rules": {
                "daily-tier0-local-backup": {
                    "schedule_expression": {
                        "@@assign": "cron(10 17 ? * * *)"
                    },
                    "start_backup_window_minutes": {
                        "@@assign": "60"
                    },
                    "complete_backup_window_minutes": {
                        "@@assign": "300"
                    },
                    "lifecycle": {
                        "delete_after_days": {
                            "@@assign": "2"
                        }
                    },
                    "target_backup_vault_name": {
                        "@@assign": "example-local-backup-vault"
                    },
                    "recovery_point_tags": {
                        "example:cost-center": {
                            "tag_key": {
                                "@@assign": "example:cost-center"
                            },
                            "tag_value": {
                                "@@assign": "Backup"
                            }
                        },
                        "example:backup-type": {
                            "tag_key": {
                                "@@assign": "example:backup-type"
                            },
                            "tag_value": {
                                "@@assign": "Daily"
                            }
                        },
                        "example:backup-region": {
                            "tag_key": {
                                "@@assign": "example:backup-region"
                            },
                            "tag_value": {
                                "@@assign": "SG"
                            }
                        },
                        "example:backup-tier": {
                            "tag_key": {
                                "@@assign": "example:backup-tier"
                            },
                            "tag_value": {
                                "@@assign": "Tier0"
                            }
                        },
                        "example:env": {
                            "tag_key": {
                                "@@assign": "example:env"
                            },
                            "tag_value": {
                                "@@assign": "prod"
                            }
                        }
                    },
                    "copy_actions": {
                        "arn:aws:backup:ap-southeast-1:centralize-id-account:backup-vault:example-prod-central-vault": {
                            "target_backup_vault_arn": {
                                "@@assign": "arn:aws:backup:ap-southeast-1:centralize-id-account:backup-vault:example-prod-central-vault"
                            },
                            "lifecycle": {
                                "delete_after_days": {
                                    "@@assign": "7"
                                }
                            }
                        }
                    }
                }
            },
            "backup_plan_tags": {
                "example:backup-tiering": {
                    "tag_key": {
                        "@@assign": "example:backup-tiering"
                    },
                    "tag_value": {
                        "@@assign": "daily"
                    }
                }
            },
            "selections": {
                "tags": {
                    "tiered-selection": {
                        "iam_role_arn": {
                            "@@assign": "arn:aws:iam::$account:role/service-role/AWSBackupDefaultServiceRole"
                        },
                        "tag_key": {
                            "@@assign": "example:priority-backup"
                        },
                        "tag_value": {
                            "@@assign": [
                                "yes"
                            ]
                        }
                    }
                }
            },
            "advanced_backup_settings": {
                "ec2": {
                    "windows_vss": {
                        "@@assign": "enabled"
                    }
                },
                "s3": {
                    "backup_acls": {
                        "@@assign": "enabled"
                    },
                    "backup_object_tags": {
                        "@@assign": "enabled"
                    }
                }
            }
        }
    }
}
```

## Step 6 — Implementation

After preparing the AWS Organization, AWS Control Tower, AWS Backup, and the centralized Backup Vault, we need to configure the required IAM roles, backup plan, resource tagging, and vault access policy.

The following configuration is required for the source accounts and the centralized Backup Vault account.

1. Required IAM Roles in Source Accounts

Each source account that will be protected by AWS Backup needs the required AWS Backup service roles. There are two important roles that need to be available.
**AWSServiceRoleForBackup**
AWS Backup uses the service-linked role:

AWSServiceRoleForBackup
with the managed policy:

*AWSBackupServiceLinkedRolePolicyForBackup*

This service-linked role allows AWS Backup to perform operations on supported AWS resources on behalf of the account.
The role should use the AWS-managed service-linked policy:

*AWSBackupServiceLinkedRolePolicyForBackup*


## Step 7 — Tag Resources That Need to Be Backed Up**

The backup plan does not necessarily need to target every resource in the AWS environment.
For this implementation, I use resource tagging to identify which resources should be protected.

The required tag is:

**Tag Key   : example:priority-backup**
**Tag Value : yes****

```text
EC2 Instance
|
+-- Name: production-web-01
+-- Tag: example:priority-backup = yes
```

Another example:

```text
RDS Instance
|
+-- Name: production-db
+-- Tag: example:priority-backup = yes
```
The backup selection can then use the tag:

example:priority-backup = yes


This gives us a simple way to control which resources are included in the backup plan. The objective of this implementation is not simply to create backups.
The architecture is designed to establish a separation between the workload account and the backup account.

```text
+------------------------+       +--------------------------+
|     Source Account     |       |       Vault Account      |
|                        |       |                          |
| EC2 / RDS / EBS / etc. |       | Centralized Backup Vault |
|                        |------>|                          |
| AWS Backup             |       | KMS CMK                  |
+------------------------+       +--------------------------+
```

This provides an additional security boundary for backup data. If a workload account is compromised, the centralized Backup Vault should remain protected by its own account-level and vault-level access controls. 

This is particularly important for ransomware scenarios, accidental deletion, and compromised administrator credentials. The next step is to test not only whether the backup succeeds, but whether the recovery point can actually be restored successfully.

**A backup strategy is only as good as its ability to recover the workload when it is needed.**

## References

1. [Build a centralized, cross-Region backup architecture with AWS Control Tower](https://aws.amazon.com/blogs/storage/build-centralized-cross-region-backup-architecture-with-aws-control-tower/)
2. [AWS Backup Documentation](https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html)