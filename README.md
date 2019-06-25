# An Okta SAML Identity Provider (IdP) provisioner

This CloudFormation template creates a SAML identity provider in Amazon Web Services' (AWS) Identity and Access Management (IAM) configuration and an AWS user so that Okta can dynamically fetch a list of available roles from your account.

https://saml-doc.okta.com/SAML_Docs/How-to-Configure-SAML-2.0-for-Amazon-Web-Service#scenarioA

In order to use it, you'll need:

  * An AWS account
  * Rights within that AWS account to create, update, and delete:
    * CloudFormation stacks
    * IAM Users, Roles and Policies
    * Lambda functions
    * Identity Providers
  * An Okta SAML Identity Provider (IdP)
  * The Federation metadata (an XML document) from Okta

## Preparing metadata

  1. Download metadata from your IdP
  1. Make it all be on one line and escape the double-quote (`"`) character:
     `tr -d '\n' metadata.xml | sed -e 's/"/\"/g' > out.xml`
  1. Copy the `out.xml` into the `ParameterValue` field of the `params.json`

## Configuration

You can set the name of your identity provider via the `SamlProviderName` parameter to the stack; this can be
configured in the `params.json`. It defaults to `MyProvider`

## Returned Values from the stack

The `ProviderCreator` custom resource returns the ARN and name of the SAML provider as as its physical resource ID, which is passed to an output.

The `OktaSSOUser` has its ARN and logical name output, but not access keys. It is recommended you create these manually, as creating and outputting them in CloudFormation stores in them in plaintext.

All of these values are output as exports, using the stack name:

```yaml
Name: !Sub "${AWS::StackName}-identity-provider-name"
Name: !Sub "${AWS::StackName}-identity-provider-arn"
Name: !Sub "${AWS::StackName}-user-name"
Name: !Sub "${AWS::StackName}-user-arn"
```

## Updating the stack

The stack can be updated, though the only changes you can make to the Identity Provider is to change the SAML Metadata document, in case you need to update the trust relationship.
