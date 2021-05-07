### Create new Security Group for Load Balancer
```aws ec2 create-security-group --group-name lab4-application-sg --description "Security group for load balancer" --vpc-id vpc-b61498cb```

### Set Up HTTP, Port from anywhere for Our Security Group
```aws ec2 authorize-security-group-ingress --group-id sg-05681fdf57bb23597 --protocol tcp --port 80 --cidr 0.0.0.0/0```

### Create a Load Balancer. You must specify two subnets that are not from the same Availability Zone
```aws elbv2 create-load-balancer --name ELB-NAME --subnets subnet-1e4b7e10 subnet-1f770b40 --security-groups sg-05681fdf57bb23597```

### Create Security Group for Instances to Filter All Traffic
```aws ec2 create-security-group --group-name lab4-applicationsg --description "Security group for application" --vpc-id vpc-b61498cb```

### Set Up HTTP only from ELB
```aws ec2 authorize-security-group-ingress --group-id sg-049be501169455332 --port 80 --protocol tcp --source-group sg-05681fdf57bb23597```

### Launch Instance
```aws ec2 run-instances --image-id ami-09e67e426f25ce0d7 --count 1 --instance-type t2.micro --key-name michael.pem --subnet-id subnet-1e4b7e10```

### Create Custom Image from existing Instances
```aws ec2 create-image --instance-id i-0b12b574c721b4fr5 --name lab2-application-ami```

### Create Target Group
```aws elbv2 create-target-group --name lab2-application-tg --protocol HTTP --port 80 --vpc-id vpc-b61498cb```

### Create Listener
```aws elbv2 create-listener --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:911064173354:loadbalancer/app/ELB-NAME/b8db373f4a351a31 --protocol HTTP --port 80 --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:us-east-1:911064173354:targetgroup/lab2-application-tg/1d37e4648204f423```

## Configuring ASG

### Create Launch Configuration
```aws autoscaling create-launch-configuration --launch-configuration-name lab2-application-lc-v1.1 --image-id ami-02652141f7a8abead --instance-type t2.micro --security-groups sg-049be501169455332```

### Create Auto Scaling Group (ASG)
```aws autoscaling create-auto-scaling-group --auto-scaling-group-name lab2-application-asg --min-size 2 --max-size 2 --desired-capacity 2 --target-group-arns arn:aws:elasticloadbalancing:us-west-1:282301773767:targetgroup/MyTargetGroup/3d30af7c2745b15f --launch-configuration-name lab2-application-lc-v1.1 --vpc subnet-1e4b7e10,subnet-1f770b40```
