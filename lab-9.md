### Create your own cluster with a unique name
```aws ecs create-cluster --cluster-name application-cluster```


### Register a Task Definition
```
{
    "family": "sample-fargate", 
    "networkMode": "awsvpc", 
    "containerDefinitions": [
        {
            "name": "fargate-app", 
            "image": "httpd:2.4", 
            "portMappings": [
                {
                    "containerPort": 80, 
                    "hostPort": 80, 
                    "protocol": "tcp"
                }
            ], 
            "essential": true, 
            "entryPoint": [
                "sh",
		"-c"
            ], 
            "command": [
                "/bin/sh -c \"echo '<html> <head> <title>Amazon ECS Sample App</title> <style>body {margin-top: 40px; background-color: #333;} </style> </head><body> <div style=color:white;text-align:center> <h1>Amazon ECS Sample App</h1> <h2>Congratulations!</h2> <p>Your application is now running on a container in Amazon ECS.</p> </div></body></html>' >  /usr/local/apache2/htdocs/index.html && httpd-foreground\""
            ]
        }
    ], 
    "requiresCompatibilities": [
        "FARGATE"
    ], 
    "cpu": "256", 
    "memory": "512"
}
```

```aws ecs register-task-definition --cli-input-json file://def.json```

### Create Security Group
```aws ec2 create-security-group --group-name ecs-sg --description "ECS Security Group" --vpc-id vpc-b61498cb```

### Set Up HTTP Port from anywhere for Our Security Group
```aws ec2 authorize-security-group-ingress --group-id sg-04887b2e6a7fe81cc --protocol tcp --port 80 --cidr 0.0.0.0/0```

### Create a Service
```aws ecs create-service --cluster application-cluster --service-name my-service --task-definition sample-fargate:1 --desired-count 1 --launch-type "FARGATE" --network-configuration "awsvpcConfiguration={subnets=[subnet-1e4b7e10],securityGroups=[sg-04887b2e6a7fe81cc],assignPublicIp=ENABLED}"```

### Get Container Info
```ecs-cli ps --cluster application-cluster```
