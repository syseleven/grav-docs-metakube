---
title: AWS Clusters
published: false
---

With MetaKube you can manage Kubernetes clusters at multiple cloud providers, one of them [Amazon AWS](https://aws.amazon.com/).

In order to create clusters with AWS, you need to sign up for AWS separately. If you have any questions about this, please
contact our [Support](../../04.Support/default.en.md).

## Needed IAM permissions

Ensure that the user used to create clusters via MetaKube has (at least) the following IAM permissions:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "iam:ListInstanceProfiles",
            "Resource": "arn:aws:iam::YOUR_ACCOUNT_ID:instance-profile/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:DeleteRolePolicy",
                "iam:DetachRolePolicy",
                "iam:GetRole",
                "iam:ListAttachedRolePolicies",
                "iam:ListRolePolicies",
                "iam:PassRole"
            ],
            "Resource": [
                "arn:aws:iam::YOUR_ACCOUNT_ID:role/SpacesKubermatic",
                "arn:aws:iam::YOUR_ACCOUNT_ID:role/kubermatic-*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:AddRoleToInstanceProfile",
                "iam:CreateInstanceProfile",
                "iam:DeleteInstanceProfile",
                "iam:DeleteRole",
                "iam:GetInstanceProfile",
                "iam:RemoveRoleFromInstanceProfile"
            ],
            "Resource": "arn:aws:iam::YOUR_ACCOUNT_ID:instance-profile/kubermatic-*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:*",
                "elasticloadbalancing:*",
                "elasticloadbalancing:CreateListener",
                "elasticloadbalancing:CreateRule",
                "elasticloadbalancing:CreateTargetGroup",
                "elasticloadbalancing:DeleteListener",
                "elasticloadbalancing:DeleteRule",
                "elasticloadbalancing:DeleteTargetGroup",
                "elasticloadbalancing:DeregisterTargets",
                "elasticloadbalancing:DescribeListeners",
                "elasticloadbalancing:DescribeRules",
                "elasticloadbalancing:DescribeTargetGroupAttributes",
                "elasticloadbalancing:DescribeTargetGroups",
                "elasticloadbalancing:DescribeTargetHealth",
                "elasticloadbalancing:ModifyListener",
                "elasticloadbalancing:ModifyRule",
                "elasticloadbalancing:ModifyTargetGroup",
                "elasticloadbalancing:ModifyTargetGroupAttributes",
                "elasticloadbalancing:RegisterTargets",
                "elasticloadbalancing:RemoveListenerCertificates",
                "elasticloadbalancing:SetIpAddressType",
                "elasticloadbalancing:SetRulePriorities",
                "elasticloadbalancing:SetSecurityGroups",
                "elasticloadbalancing:SetSubnets",
                "elasticloadbalancing:SetWebAcl",
                "sts:GetFederationToken"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "iam:AttachRolePolicy",
                "iam:CreateRole",
                "iam:DeleteInstanceProfile",
                "iam:DeleteRole"
            ],
            "Resource": "arn:aws:iam::YOUR_ACCOUNT_ID:role/kubermatic-*"
        },
        {
            "Sid":"statement1",
            "Effect":"Allow",
            "Action":[
                 "s3:*"
            ],
            "Resource":[
                 "arn:aws:s3:::*"
            ]
         }
    ]
}
```

You can find more information on how to create and manage AWS IAM roles and access keys in the [AWS documentation](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html).
