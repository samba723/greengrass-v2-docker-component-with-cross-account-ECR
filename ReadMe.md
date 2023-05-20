# Logged in ECR
    $ aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 0987654321.dkr.ecr.us-east-1.amazonaws.com

# Tagged docker image
    $ docker tag docker-component:latest 0987654321.dkr.ecr.us-east-1.amazonaws.com/docker-component:latest

# Pushed docker image to ECR
    $ docker push 0987654321.dkr.ecr.us-east-1.amazonaws.com/docker-component:latest

# Greengrass V2 component recipe
    ---
    RecipeFormatVersion: '2020-01-25'
    ComponentName: DockerComponent.CrossAccount.ECR
    ComponentVersion: '1.0.0'
    ComponentDescription: 'docker component with sample applicaiton deployment using cross account ECR'
    ComponentPublisher: samba723
    ComponentDependencies:
    aws.greengrass.DockerApplicationManager:
        VersionRequirement: ">=2.0.8 <2.1.0"
        DependencyType: "HARD"
    aws.greengrass.TokenExchangeService:
        VersionRequirement: ">=2.0.3 <2.1.0"
        DependencyType: "HARD"
    Manifests:
    - Platform:
        os: linux
        Lifecycle:
            Run: docker run --publish 8000:5000 --rm 0987654321.dkr.ecr.us-east-1.amazonaws.com/docker-component:latest
        Artifacts:
        - URI: "docker:0987654321.dkr.ecr.us-east-1.amazonaws.com/docker-component:latest"

# Update GreengrassV2 TokenExchange IAM role to add access to the ECR
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "VisualEditor0",
                "Effect": "Allow",
                "Action": "ecr:GetAuthorizationToken",
                "Resource": "*"
            },
            {
                "Sid": "VisualEditor1",
                "Effect": "Allow",
                "Action": [
                    "ecr:BatchGetImage",
                    "ecr:GetDownloadUrlForLayer"
                ],
                "Resource": [
                    "arn:aws:ecr:us-east-1:0987654321:repository/docker-component"
                ]
            }
        ]
    }

# Update ECR permission to allow GreengrassV2 TokenExchange IAM role access ECR images
    {
        "Version": "2012-10-17",
        "Statement": [
            {
            "Sid": "AllowPull",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                "arn:aws:iam::1234567890:root",
                "arn:aws:iam::1234567890:role/GreengrassV2TokenExchangeRole"
                ]
            },
            "Action": [
                "ecr:BatchGetImage",
                "ecr:GetDownloadUrlForLayer"
            ]
            }
        ]
    }