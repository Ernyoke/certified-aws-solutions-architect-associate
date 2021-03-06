# CloudFormation

## Infrastructure as Code

- Infrastructure is described with code
- This can be deployed in the cloud in order to create/update/delete the necessary resources

## CloudFormation Introduction

- CloudFormation is a declarative way of outlining AWS infrastructure for any resources
- In the CloudFormation templates we specify the resources needed to be instantiated
- CloudFormation will create those in the right order with exact configuration that we specify

## Benefits

- Infrastructure as code:
    - No resources are manually created, which is excellent for control
    - Code can be version controlled
    - Changes to the infrastructure are reviewed through code
- Cost:
    - Each resource within a CloudFormation stack is tagged with an identifier, meaning we can easily see how much a stack costs
    - We can estimate costs by using a CloudFormation template
- Productivity:
    - Ability to destroy and re-create an infrastructure on the cloud on the fly
    - Automated generation of Diagram for the templates
    - Declarative programming, meaning no need to figure out ordering and orchestration
- Separation of concerns:
    - We can create as many stacks as we need, example: VPC stack, Network stack, App stack

## How CloudFormation Works

- Templates have to be uploaded in S3 and then references in CloudFormation
- We can edit previous templates. We have to reupload a new version of the template to S3
- Stack are identified by a name
- Deleting a stack will delete every resource created

## Deploy CloudFormation Templates

- Manual way:
    - Editing templates in the CloudFormation Designer
    - Using the console to input parameters
- Automated way:
    - Editing templates in a YAML file
    - Using the AWS CLI to deploy templates

## CloudFormation Building Blocks

- Template components:
    - Resources: declared AWS resources in the template (mandatory)
    - Parameters: dynamic inputs for the template
    - Mappings: static variables in the templates
    - Outputs: references to what has been created
    - Conditionals: list of conditions to perform resource creation
    - Metadata
- Template helpers:
    - References
    - Functions

## StackSets

- Create, update or delete stacks across multiple accounts and regions with a single operation
- An administration account needs to create a stack set
- Trusted accounts can create, update delete stack instances from the stack set
- When a stack set is updated, all the associated stack instances are updated through all accounts and regions

