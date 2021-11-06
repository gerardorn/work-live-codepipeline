# Work-Live CodePipeline

CodePipeline to deploy changes from a Work account to a Live account

## Preparation

Create the Service Linked Role for RDS on both accounts

```
aws iam create-service-linked-role --aws-service-name rds.amazonaws.com
```

## Deployment

### 1. Live Account

Create the CodePipeline Service Roles

```
cd live
```


```
aws cloudformation package --template-file main.json --use-json --s3-bucket {LIVE_ACCT_CF_BUCKET} --s3-prefix templates --output-template-file packaged.json
```

```
aws cloudformation deploy --template-file packaged.json --stack-name pipeline --parameter-overrides ArtifactBucketName={WORK_ACCT_ARTIFACTS_BUCKET} WorkAccountNumber={WORK_ACCT_NUMBER} --capabilities CAPABILITY_NAMED_IAM
```

### 2. Work Account

Create the CodePipeline Service Roles and the Pipeline

```
cd ../work
```

```
aws cloudformation package --template-file main.json --use-json --s3-bucket {WORK_ACCT_ARTIFACTS_BUCKET} --s3-prefix templates --output-template-file packaged.json
```

```
aws cloudformation deploy --template-file packaged.json --stack-name pipeline --parameter-overrides ArtifactBucketName={WORK_ACCT_ARTIFACTS_BUCKET} LiveAccountNumber={LIUVE_ACCT_NUMBER} RepositoryName={APP_REPO_NAME} --capabilities CAPABILITY_NAMED_IAM
```
