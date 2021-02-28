# Advanced IAM

## AWS STS - Security Token Service

- Allows to grant limited and temporary access to AWS resources
- Token valid for up to one hour (must be refreshed)
- STS most important APIs:
    - **AssumeRole**:
        - Usage within our account: for temporary enhanced security
        - Cross account: assume role on target account to perform actions there
    - **AssumeRoleWithSAML**:
        - Return credentials for user logging in with SAML
    - **AssumeRoleWithWebIdentity**:
        - Return credentials for users logged in with IdP (Facebook login, Google login, OIDC)
        - AWS recommends against using this, use Cognito instead
    - **GetSessionToken**:
        - Fro MFA, from an user or AWS account root user

## Using STS to Assume a Role

1. Define an IAM Role within an account or cross-account
2. Define which principals can access this IAM Role
3. Use AWS STS to retrieve credentials and impersonate the IAM Role you have access to (AssumeRole API)
4. Temporary credentials will be valid for 15 minutes up to 1 hour