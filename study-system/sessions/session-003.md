# AWS SAP Session 3

Date: 2026-06-05
Status: Completed
Cycle: 1

## Source Files

- `01-accounts/mfa.md`
- `01-accounts/iam.md`

## Result

- Question Count: 10
- Correct: 9
- Wrong: 1
- Accuracy: 90%
- Main Weakness: MFA factor classification

## Covered Topics

### MFA

- MFA stands for Multi-Factor Authentication.
- A factor is a different kind of evidence used to prove a user's identity.
- Knowledge factor means something the user knows, such as a username, password, or PIN.
- Possession factor means something the user has, such as a bank card, OTP token, authenticator app, or FIDO2 security key.
- Inherent factor means something the user is or biologically has, such as fingerprint, face, voice, or iris.
- Location factor means the user's access location or network context, such as a physical location, internal Wi-Fi, or VPN.
- Combining more different factors improves security because stealing one factor, such as a password, is not enough to authenticate.

### IAM Overview

- The AWS root account should not be used for normal operations.
- People and systems should use IAM users or roles with only the permissions they need.
- IAM is the central AWS service for authentication and authorization.

### IAM Components

- IAM User usually represents one actual person.
- IAM Group is a way to group multiple users, such as by function or team.
- IAM Role is suitable for AWS resources, services, applications, and workloads that should use temporary credentials instead of fixed long-term access keys.
- Cross-account Role is a role that a principal from another AWS account assumes to access resources in the target account.
- IAM Policy is a JSON permission document defining what users, groups, or roles can and cannot do.

### IAM Policy Types

- AWS Managed Policy is managed and updated by AWS.
- Customer Managed Policy is created by the customer and can be reused.
- Inline Policy is attached directly to a specific user, group, or role and is not suitable for broad reuse or centralized management.
- Resource-based Policy is attached to the resource itself, such as an S3 bucket policy or SQS queue policy.
- Resource-based Policies are often important for cross-account access.

### IAM Role vs Resource-based Policy

- When a principal assumes an IAM Role, it acts with the assumed role's permissions using temporary credentials.
- The original permissions are not simply combined with the role permissions.
- With a resource-based policy, the requester can keep its original principal context while the target resource explicitly allows that principal.
- In cross-account designs, this distinction matters when the caller must keep permissions in the source account while writing to a resource in another account.

### IAM Best Practices

- Use one IAM User per person.
- Use one IAM Role per application or workload where appropriate, such as EC2, Lambda, or ECS.
- Never share IAM credentials.
- Never hard-code credentials in source code.
- Avoid root account usage except for necessary initial or exceptional tasks.
- Grant least privilege.

## Quiz Review

### Score

- Correct: 9/10
- Wrong: 1/10
- Accuracy: 90%

### Wrong Questions

#### Q3

- My Answer: B
- Correct Answer: C
- Weak Tag: MFA factor classification
- Why Wrong: Face recognition is an inherent factor, not a possession factor. Possession means something the user has, such as an OTP token, authenticator app, or security key.
- Learned Point: Knowledge = password/PIN, Possession = OTP/authenticator/security key, Inherent = fingerprint/face/iris, Location = network or physical location.

## User Questions Logged

No additional user questions were logged after the quiz in this session.

## Next Session

- Not assigned yet
