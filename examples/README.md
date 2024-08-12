# Configuring a custom profile

You can configure a custom profile to use with LocalStack. Add the following profile to your AWS configuration file (by default, this file is at ~/.aws/config):


```
[profile localstack]
region=us-east-1
output=json
endpoint_url = http://localhost:4566
```

Add the following profile to your AWS credentials file (by default, this file is at ~/.aws/credentials):

```
[localstack]
aws_access_key_id=test
aws_secret_access_key=test
```

You can now use the localstack profile with the aws CLI:
```
aws s3 ls --profile localstack
```

Setting Default Profile 

```
export AWS_DEFAULT_PROFILE=localstack
```