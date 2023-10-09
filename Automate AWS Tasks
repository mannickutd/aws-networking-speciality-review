*** Infrastructure as Code
  - Define configurations of our networks and infrastructure in a single document or groups of docuemnts.
  - Store these documents in source control.
  - Deploy different versions of the document for each environment (dev, test, prod).
  - Verify the proper configurations and operations in testing environments.
  - Not only can we provision infrastructure but also deploy software on the newly created infrastructure.
  - Agile development cycles.
  - Standardization and reuse.
  - Resource utilization.

*** CloudFormation
  - Process
    1. Code templates in JSON or YAML.
    2. Upload templates from local files or from S3.
    3. Create a stack using API, CLI or console.
    4. Stacks, infrastructure, and resources are created.
  - Components
    1. Templates, documents taht describe teh environment that you want to create and launch in AWS.
    2. Stacks, a stack is deployed from a template adn represents the resources and infrastructure that are connected together and belong to an application.
    3. Once a stck has been successfully launched, we can use change sets to perform updates on the deployed infrastructure.

*** Using the Amazon CDK
  - AWS Cloud Development Kit (AWS CDK) allows you to define your AWS Infrastructure as Code in one of many supported programming languages.
  - Audience, moderately to highly experienced AWS users.
  - The output of an AWS CDK program is an AWS CloudFormation template.
*** Dynamically Mapping Resources with AWS Cloud Map
  - AWS Cloud Map
    1. Managed service to create and maintain maps of backend services/resources.
    2. Tightly integrates with Amazon ECS. Works with Amazon EKS via Kubernetes ExternalDNS connector.
    3. Containers automatically register with the service as they spin up.
    4. Supports other resources like DynamoDB, S3, SQS, and others.
  - How it works
    1. Create a namespace for locating resources.
    2. Create a Cloud Map service fore each resource type.
    3. Applications call RegisterInstance API to craete a service instance.
    4. Application uses DiscoverInstances with namespace and service information to connection to resources.
