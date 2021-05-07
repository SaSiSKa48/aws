### Create new Bucket
```aws s3api create-bucket --bucket michael.personal.bucket --create-bucket-configuration LocationConstraint=us-west-1```

### Clone files to Bucket
```aws s3 sync website-folder s3://michael.personal.bucket/```

### Set Bucket to Static Website Hosting
```aws s3 website s3://michael.personal.bucket/ --index-document index.html --error-document error.html```

### Create JSON file with Policy for Bucket
```
{
    "Id": "BucketPolicy",
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowAccess",
            "Action": [
                "s3:GetObject"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::michael.personal.bucket/*",
            "Principal": "*"
        },
        {
            "Sid": "DenyAccess",
            "Action": [
                "s3:GetObject"
            ],
            "Effect": "Deny",
            "Resource": "arn:aws:s3:::michael.personal.bucket/*",
            "Principal": "*",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": ["50.31.252.0/24"]
                }
            }
        }
    ]
}
```

### Set Policy to Bucket
```aws s3api put-bucket-policy --bucket michael.personal.bucket --policy file://BucketPolicy.json```