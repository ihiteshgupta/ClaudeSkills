---
name: aws-architect
description: Expert in AWS solutions architecture, services, and best practices. Use for AWS infrastructure design, deployment, optimization, and cloud-native applications.
allowed-tools: Read, Write, Edit, Grep, Glob, Bash
---

# AWS Solutions Architect Expert

## Purpose
Provide expert guidance on AWS architecture, service selection, deployment strategies, cost optimization, and cloud-native application design on Amazon Web Services.

## When to Use This Skill
- Designing AWS infrastructure
- Selecting appropriate AWS services
- Migrating applications to AWS
- Implementing serverless architectures
- Setting up CI/CD on AWS
- Cost optimization
- Security and compliance
- Disaster recovery planning

## Core AWS Services

### Compute
- **EC2** - Virtual servers
- **Lambda** - Serverless functions
- **ECS/EKS** - Container orchestration
- **Fargate** - Serverless containers
- **Elastic Beanstalk** - PaaS

### Storage
- **S3** - Object storage
- **EBS** - Block storage
- **EFS** - File storage
- **Glacier** - Archive storage
- **Storage Gateway** - Hybrid storage

### Database
- **RDS** - Relational databases (PostgreSQL, MySQL, etc.)
- **DynamoDB** - NoSQL key-value
- **Aurora** - High-performance relational
- **ElastiCache** - In-memory cache (Redis, Memcached)
- **DocumentDB** - MongoDB-compatible

### Networking
- **VPC** - Virtual Private Cloud
- **CloudFront** - CDN
- **Route 53** - DNS
- **API Gateway** - API management
- **Load Balancers** - ALB, NLB, CLB

### Security & Identity
- **IAM** - Identity and Access Management
- **Cognito** - User authentication
- **Secrets Manager** - Secrets storage
- **KMS** - Key Management Service
- **WAF** - Web Application Firewall

### DevOps & Monitoring
- **CloudWatch** - Monitoring and logs
- **CloudFormation** - Infrastructure as Code
- **CodePipeline** - CI/CD
- **Systems Manager** - Operations management
- **X-Ray** - Distributed tracing

## Architecture Patterns

### 1. Three-Tier Web Application
```
Architecture:
┌──────────────────────────────────────────┐
│           CloudFront (CDN)               │
│        S3 (Static Assets)                │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│      Application Load Balancer           │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   EC2 Auto Scaling Group                 │
│   (Application Servers)                  │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   RDS Multi-AZ (Database)                │
│   + Read Replicas                        │
└──────────────────────────────────────────┘

Infrastructure as Code (CloudFormation):
```yaml
Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: MyVPC

  PublicSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: !Select [0, !GetAZs '']
      MapPublicIpOnLaunch: true

  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Scheme: internet-facing
      Subnets:
        - !Ref PublicSubnet1
        - !Ref PublicSubnet2
      SecurityGroups:
        - !Ref ALBSecurityGroup

  AutoScalingGroup:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      MinSize: 2
      MaxSize: 10
      DesiredCapacity: 2
      LaunchTemplate:
        LaunchTemplateId: !Ref LaunchTemplate
        Version: !GetAtt LaunchTemplate.LatestVersionNumber
      TargetGroupARNs:
        - !Ref TargetGroup
      VPCZoneIdentifier:
        - !Ref PrivateSubnet1
        - !Ref PrivateSubnet2

  RDSInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      Engine: postgres
      EngineVersion: '14.7'
      DBInstanceClass: db.t3.medium
      AllocatedStorage: 100
      MultiAZ: true
      BackupRetentionPeriod: 7
```

### 2. Serverless Application
```
Architecture:
┌──────────────────────────────────────────┐
│   CloudFront + S3 (Frontend)             │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│        API Gateway                       │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Lambda Functions                       │
│   (Node.js/Python)                       │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   DynamoDB + S3                          │
└──────────────────────────────────────────┘

SAM Template:
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31

Globals:
  Function:
    Runtime: nodejs18.x
    Timeout: 30
    Environment:
      Variables:
        TABLE_NAME: !Ref DynamoDBTable

Resources:
  ApiGateway:
    Type: AWS::Serverless::Api
    Properties:
      StageName: prod
      Auth:
        DefaultAuthorizer: CognitoAuthorizer
        Authorizers:
          CognitoAuthorizer:
            UserPoolArn: !GetAtt UserPool.Arn

  CreateItemFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: src/handlers/
      Handler: create.handler
      Events:
        CreateItem:
          Type: Api
          Properties:
            RestApiId: !Ref ApiGateway
            Path: /items
            Method: POST
      Policies:
        - DynamoDBCrudPolicy:
            TableName: !Ref DynamoDBTable

  GetItemFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: src/handlers/
      Handler: get.handler
      Events:
        GetItem:
          Type: Api
          Properties:
            RestApiId: !Ref ApiGateway
            Path: /items/{id}
            Method: GET
      Policies:
        - DynamoDBReadPolicy:
            TableName: !Ref DynamoDBTable

  DynamoDBTable:
    Type: AWS::DynamoDB::Table
    Properties:
      BillingMode: PAY_PER_REQUEST
      AttributeDefinitions:
        - AttributeName: id
          AttributeType: S
      KeySchema:
        - AttributeName: id
          KeyType: HASH

  UserPool:
    Type: AWS::Cognito::UserPool
    Properties:
      UserPoolName: MyUserPool
      AutoVerifiedAttributes:
        - email
```

### 3. Microservices on ECS Fargate
```
Architecture:
┌──────────────────────────────────────────┐
│   Route 53 (DNS)                         │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   Application Load Balancer              │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   ECS Cluster (Fargate)                  │
│   ┌──────────┐  ┌──────────┐            │
│   │ Service 1│  │ Service 2│            │
│   └──────────┘  └──────────┘            │
└────────────┬─────────────────────────────┘
             │
┌────────────▼─────────────────────────────┐
│   RDS + ElastiCache + S3                 │
└──────────────────────────────────────────┘

Terraform Configuration:
```hcl
resource "aws_ecs_cluster" "main" {
  name = "microservices-cluster"

  setting {
    name  = "containerInsights"
    value = "enabled"
  }
}

resource "aws_ecs_task_definition" "user_service" {
  family                   = "user-service"
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  cpu                      = "256"
  memory                   = "512"
  execution_role_arn       = aws_iam_role.ecs_execution_role.arn
  task_role_arn           = aws_iam_role.ecs_task_role.arn

  container_definitions = jsonencode([
    {
      name  = "user-service"
      image = "123456789.dkr.ecr.us-east-1.amazonaws.com/user-service:latest"
      portMappings = [
        {
          containerPort = 3000
          protocol      = "tcp"
        }
      ]
      environment = [
        {
          name  = "DATABASE_URL"
          value = "postgresql://..."
        }
      ]
      secrets = [
        {
          name      = "JWT_SECRET"
          valueFrom = aws_secretsmanager_secret.jwt_secret.arn
        }
      ]
      logConfiguration = {
        logDriver = "awslogs"
        options = {
          "awslogs-group"         = "/ecs/user-service"
          "awslogs-region"        = "us-east-1"
          "awslogs-stream-prefix" = "ecs"
        }
      }
    }
  ])
}

resource "aws_ecs_service" "user_service" {
  name            = "user-service"
  cluster         = aws_ecs_cluster.main.id
  task_definition = aws_ecs_task_definition.user_service.arn
  desired_count   = 2
  launch_type     = "FARGATE"

  network_configuration {
    subnets         = aws_subnet.private[*].id
    security_groups = [aws_security_group.ecs_tasks.id]
  }

  load_balancer {
    target_group_arn = aws_lb_target_group.user_service.arn
    container_name   = "user-service"
    container_port   = 3000
  }

  deployment_configuration {
    maximum_percent         = 200
    minimum_healthy_percent = 100
  }

  enable_execute_command = true
}

resource "aws_appautoscaling_target" "ecs_target" {
  max_capacity       = 10
  min_capacity       = 2
  resource_id        = "service/${aws_ecs_cluster.main.name}/${aws_ecs_service.user_service.name}"
  scalable_dimension = "ecs:service:DesiredCount"
  service_namespace  = "ecs"
}

resource "aws_appautoscaling_policy" "ecs_policy" {
  name               = "cpu-scaling"
  policy_type        = "TargetTrackingScaling"
  resource_id        = aws_appautoscaling_target.ecs_target.resource_id
  scalable_dimension = aws_appautoscaling_target.ecs_target.scalable_dimension
  service_namespace  = aws_appautoscaling_target.ecs_target.service_namespace

  target_tracking_scaling_policy_configuration {
    predefined_metric_specification {
      predefined_metric_type = "ECSServiceAverageCPUUtilization"
    }
    target_value = 70.0
  }
}
```

## Security Best Practices

### 1. IAM Policies
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-bucket/*",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "203.0.113.0/24"
        }
      }
    }
  ]
}
```

### 2. VPC Security
- Use private subnets for applications
- NAT Gateway for outbound internet
- Security Groups as stateful firewalls
- Network ACLs for subnet-level control
- VPC Flow Logs for monitoring

### 3. Encryption
- Enable S3 bucket encryption
- RDS encryption at rest
- EBS volume encryption
- TLS/SSL for data in transit
- KMS for key management

## Cost Optimization

1. **Right-Sizing**
   - Use Cost Explorer
   - Monitor CloudWatch metrics
   - Downsize underutilized instances

2. **Reserved Instances & Savings Plans**
   - Commit to 1 or 3 years
   - Save up to 72%

3. **Spot Instances**
   - Up to 90% savings
   - Good for fault-tolerant workloads

4. **Auto Scaling**
   - Scale based on demand
   - Schedule scaling for predictable patterns

5. **S3 Lifecycle Policies**
   - Move to S3-IA after 30 days
   - Archive to Glacier after 90 days

## Monitoring & Operations

### CloudWatch Alarms
```yaml
Resources:
  HighCPUAlarm:
    Type: AWS::CloudWatch::Alarm
    Properties:
      AlarmDescription: Alert when CPU exceeds 80%
      MetricName: CPUUtilization
      Namespace: AWS/EC2
      Statistic: Average
      Period: 300
      EvaluationPeriods: 2
      Threshold: 80
      ComparisonOperator: GreaterThanThreshold
      AlarmActions:
        - !Ref SNSTopic
```

### X-Ray Tracing
```javascript
const AWSXRay = require('aws-xray-sdk-core');
const AWS = AWSXRay.captureAWS(require('aws-sdk'));

exports.handler = async (event) => {
  const segment = AWSXRay.getSegment();
  const subsegment = segment.addNewSubsegment('database-query');

  try {
    // Your code
  } finally {
    subsegment.close();
  }
};
```

## Deployment Strategies

### Blue-Green Deployment
- Maintain two identical environments
- Switch traffic using Route 53
- Instant rollback capability

### Canary Deployment
- Route small % of traffic to new version
- Monitor metrics
- Gradually increase traffic

### Rolling Deployment
- Update instances gradually
- Maintain capacity during update

## Well-Architected Framework

1. **Operational Excellence** - Run and monitor systems
2. **Security** - Protect information and systems
3. **Reliability** - Recover from failures
4. **Performance Efficiency** - Use resources efficiently
5. **Cost Optimization** - Avoid unnecessary costs
6. **Sustainability** - Minimize environmental impact

This skill ensures well-architected, secure, and cost-effective AWS solutions.
