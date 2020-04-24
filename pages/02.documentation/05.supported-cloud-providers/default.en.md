---
title: 'Supported Cloud Providers'
taxonomy:
    tag:
        - metakube
        - cluster
---

MetaKube currently supports the following cloud providers and regions for creating Kubernetes clusters:

## SysEleven OpenStack

Supported regions:

* cbk
* dbl

Note that every region has its own set of resource quotas. You can see the quota for every region in the [OpenStack Dashboard](https://dashboard.cloud.syseleven.net).

If you want to increase the quota for a region, please contact our [Support](../../05.support/default.en.md).

When creating a cluster you have to provide valid SysEleven OpenStack credentials for the OpenStack tenant that you want to create the cluster in, so that MetaKube can create and manage VMs, Networks, LoadBalancers and Volumes.

For more information see the [SysEleven Stack documentation](https://docs.syseleven.de/syseleven-stack/).

## Amazon Web Services

In order to create clusters with AWS, you need to sign up for AWS separately.

Supported regions:

* EU Frankfurt
* EU Ireland
* EU Stockholm
* EU London
* EU Paris

If you have any questions about the Account or require different regions, please contact our [Support](../../05.support/default.en.md).

When creating a cluster you have to provide valid SysEleven OpenStack credentials for the OpenStack tenant that you want to get the MetaKube Fee billed to and your AWS IAM information, so that MetaKube can create and manage VMs, Networks, LoadBalancers and Volumes.

### Needed IAM permissions

Ensure that the user used to create clusters via MetaKube has (at least) the following IAM permissions:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "iam:ListInstanceProfiles",
            "Resource": "arn:aws:iam::YOUR_ACCOUNT_ID:instance-profile/*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "iam:GetRole",
                "iam:PassRole",
                "iam:DetachRolePolicy",
                "iam:DeleteRolePolicy",
                "iam:ListAttachedRolePolicies",
                "iam:ListRolePolicies"
            ],
            "Resource": [
                "arn:aws:iam::YOUR_ACCOUNT_ID:role/SpacesMetakube",
                "arn:aws:iam::YOUR_ACCOUNT_ID:role/metakube-*"
            ]
        },
        {
            "Sid": "VisualEditor2",
            "Effect": "Allow",
            "Action": [
                "iam:CreateInstanceProfile",
                "iam:DeleteInstanceProfile",
                "iam:GetInstanceProfile",
                "iam:RemoveRoleFromInstanceProfile",
                "iam:DeleteRole",
                "iam:AddRoleToInstanceProfile"
            ],
            "Resource": [
                "arn:aws:iam::YOUR_ACCOUNT_ID:role/metakube-*",
                "arn:aws:iam::YOUR_ACCOUNT_ID:instance-profile/metakube-*"
            ]
        },
        {
            "Sid": "VisualEditor3",
            "Effect": "Allow",
            "Action": [
                "elasticloadbalancing:ModifyListener",
                "sts:GetFederationToken",
                "elasticloadbalancing:RegisterTargets",
                "elasticloadbalancing:SetIpAddressType",
                "elasticloadbalancing:SetRulePriorities",
                "elasticloadbalancing:RemoveListenerCertificates",
                "elasticloadbalancing:SetWebAcl",
                "elasticloadbalancing:CreateListener",
                "elasticloadbalancing:DescribeListeners",
                "elasticloadbalancing:CreateRule",
                "elasticloadbalancing:ModifyTargetGroupAttributes",
                "elasticloadbalancing:DeleteRule",
                "s3:*",
                "elasticloadbalancing:CreateTargetGroup",
                "elasticloadbalancing:*",
                "elasticloadbalancing:DeregisterTargets",
                "elasticloadbalancing:SetSubnets",
                "elasticloadbalancing:DeleteTargetGroup",
                "elasticloadbalancing:DescribeTargetGroupAttributes",
                "elasticloadbalancing:ModifyRule",
                "elasticloadbalancing:DescribeTargetHealth",
                "elasticloadbalancing:SetSecurityGroups",
                "elasticloadbalancing:DescribeTargetGroups",
                "ec2:*",
                "elasticloadbalancing:DescribeRules",
                "elasticloadbalancing:ModifyTargetGroup",
                "elasticloadbalancing:DeleteListener"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor4",
            "Effect": "Allow",
            "Action": [
                "iam:DeleteInstanceProfile",
                "iam:CreateRole",
                "iam:DeleteRole",
                "iam:AttachRolePolicy",
                "iam:PutRolePolicy",
                "iam:CreateServiceLinkedRole"
            ],
            "Resource": [
                "arn:aws:iam::YOUR_ACCOUNT_ID:role/metakube-*",
                "arn:aws:iam::YOUR_ACCOUNT_ID:instance-profile/metakube-*",
                "arn:aws:iam::YOUR_ACCOUNT_ID:role/aws-service-role/elasticloadbalancing.amazonaws.com/*"
            ]
        }
    ]
}
```

You can find more information on how to create and manage AWS IAM roles and access keys in the [AWS documentation](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html).

## Microsoft Azure

In order to create clusters with Azure, you need to sign up for Azure separately.

Supported regions:

* EU Netherlands
* EU Ireland
* EU France Central
* EU UK South
* EU UK West
* EU Germany West
* EU Switzerland North
* EU Norway East

If you have any questions about the Account or require different regions, please contact our [Support](../../05.support/default.en.md).

When creating a cluster you have to provide valid SysEleven OpenStack credentials for the OpenStack tenant that you want to get the MetaKube fee billed to and your AWS IAM information. So that MetaKube can create and manage VMs, Networks, LoadBalancers and Volumes.

### Creating App credentials

In order to create a cluster, you need to provide the following information:

* Client ID
* Client Secret
* Tenant ID
* Subscript ID

To get a Client ID, Client Secret and Tenant ID, you have to create an "App registration", in the "Azure Active Directory". For this go to "Azure Active Directory", "App registrations" and create a "New registration". You do not need to provide a Redirect URI. After the creation note download the "Application (client) ID" and "Directory (tenant) ID".
Then for this "App registration" go to "Certificates & secrets" and create a new "Client secret", this secret should either not expire or you have to update it in your MetaKube cluster before it expires since Kubernetes needs this information continuously to manage volumes, nodes and load balancers.

Next you have to give the new "App registration" permissions to manage the above resources. For this go to "Subscriptions", choose the subscription that you want to create your cluster in, take a note of the "Subscription ID" and then go to "Access control (IAM)" in your subscription.
There add a new "Role assignment" between the role "Contributor" and the "Azure AD user, group or service principal" that you just created.
