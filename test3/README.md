Solution: A high available, fault tolerant and scalable cloud based solution for an e-commerce sit.
Notations: AWS cloud resource notations are used. Cloud is prefereable as easy resource management,
quick start, reliability, less troubleshooting. 

Architecture description: (please lookinto the visual diagram as well for better understanding)

VPC info:
 There will be a vpc with 6 subnets, 3 will be private and rest 3 will be public where the selected region has 3 availibility zones. Each AZ should have one public and one private subnet. Cross zone
 load balancing should be enabled for high availibility and reliability.


App architecture:
 There will be two segments of the main application, frontend and middlware. 

 Frontend will be served by an S3 bucket with cloudfront in front of it. This will ensure faster
 loading of the pages.

The middleware should have in total 7 modules which will be served by 2 load balancers.
 Modules:
  - proxy
  - authentication
  - inventory
  - checkout
  - cart
  - notification
  - payment

Public alb and proxy module will be in public subnets.
The proxy module is responsible for forwarding teh requests to the private alb. Also there will be
path based routing as well.

Private alb and rest 5 modules will be in private subnets.
Module descriptions:
 authentication: It is responsible for authenticate an existing user or registering a new user. The
 user info will be stored in dynamodb.
 
 inventory: Will be managing the inventory by connecting the RDS, mainly CRUD operation. RDS will be served as the main inventory database.
 
 checkout: Checkout is responsible for completing an e-commerce operation. It will call inventory module to update inventory, payment module to complete payment and notification module to notify the
 user, may be email or sms etc.

 cart: Will be managing carts, it will call redis to store the session.
 
 notification: For sending email and sms notifications.

 payment: For completing payments by connecting external payment gateways.

Reliability/High availibity:
 Load balancers are used for traffic distributions. It will be a aws autoscale based solution for high
 availibility of the modules, which also ensures fault tolerancy and scalability as well.
 A lot of managed services are used like RDS,dynamodb,redis for reliability and high performance.

Cloud resource management: Infrastructure as code solution will be incorporated and for that
'terraform' will be used. terreform is preferable as it is easy to use, easy to learn and lightweight.
 Also an unique feature, terraform keeps a statefile to track the defined resources, it checks the cloud resources against the statefile before making any changes which sometimes helps to avoid anomalies. 

CI/CD
 Tool: jenkins. It is flexible, platform independant and easy to deploy.
 Deployment strategy(In descending order):
  Two seperate repo will be used for infra code and mw code. Different branches should be kept in 
  infra repo for maintaining different env (prod, dev, stg etc), MW code will be checked out from a
  release tag. Blue green deployment strategy will be followed. For rollback, terraform can be used
  in a different rollback pipeline by maintaining launch template versions.
  - In warmup stage both infra and mw code will be checked out.
  - MW code will be built by appropriate tools.
  -* Frontend built artifact will be pushed to S3.
  - AMI will be built to deploy artifacts and for that 'packer' will be used where ansible will be 
  used as the provisioner.
  - Infra will be updated by terraform.
  - Frontend artifacts will be pushed to S3 and old servers(ec2-instances) will be recycled.
  - A notification can be sent.

Monitoring:
 Tools like newrelic can be used for monitoring solution. Both infra and application can be monitored
 with newrelic. For newrelic monirting, the newrelic agent have to be installed in each module, which
 can be easily configured by ansible.

Logging: 
 Cloudwatch can be used as a logging solution for both applications and the servers.

Alerting: 
 There are lots ways for alerting solutions, newrelic can be integrated in day to day used
 communication medium like slack. Also aws lambda can be used with boto3 library to check
 periodically if the infra is in desired state and send email/sms/slack notifications when needed,
 also the lambda can be invoked by aws eventbridge on specific events.

Secrets management: 
 Secrets and configs can be easily managed by AWS Systems Manager (ssm), it is fairly easy while
 using IAC. For example an RDS and an ssm paramter resource can be defined by terraform where the
 endpoint of the rds will be the value of the ssm parameter. ssm parameters can be encrypted by kms
 for better security which also can be managed by terraform without any manual intervention. In fact
 rds, dynamodb data also can be encrypted with kms. 

                                                                   END