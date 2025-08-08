# AWS 권한 설정 가이드

## 🔐 권한 구조 개요

이 시스템은 **권한 분리 원칙**에 따라 두 가지 자격 증명을 사용합니다:

1. **Agent 기본 자격 증명**: AI 에이전트 실행을 위한 최소 권한
2. **CLI 실행 주체 권한**: AWS 리소스 조회를 위한 실제 작업 권한 (Assume Role로 위임)

## 🤖 Agent 기본 자격 증명 권한

### 필수 권한 (AWS_ACCESS_KEY_ID/AWS_SECRET_ACCESS_KEY)

#### 1. Bedrock AI 모델 호출 권한
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "bedrock:InvokeModel",
                "bedrock:InvokeModelWithResponseStream"
            ],
            "Resource": "arn:aws:bedrock:*:*:*"
        }
    ]
}
```

#### 2. STS Assume Role 권한 (범용)
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::*:role/*"
        },
        {
            "Effect": "Allow",
            "Action": "sts:GetCallerIdentity",
            "Resource": "*"
        }
    ]
}
```

## 👤 CLI 실행 주체 권한 (Cross-Account Role 기반)

### AWS CLI 명령어 실행을 위한 Cross-Account Role 설정

#### Role 신뢰 정책 (Trust Policy)
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Principal": {
                "AWS": "arn:aws:iam::[AGENT_ACCOUNT_ID]:root"
            },
            "Condition": {}
        }
    ]
}
```

#### Role 권한 정책 (Permission Policy)
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:Describe*",
                "s3:List*",
                "s3:Get*",
                "s3:GetBucketLocation",
                "s3:GetBucketVersioning",
                "lambda:List*",
                "lambda:Get*",
                "rds:Describe*",
                "iam:List*",
                "iam:Get*",
                "cloudformation:List*",
                "cloudformation:Describe*",
                "logs:Describe*",
                "logs:Get*",
                "cloudwatch:List*",
                "cloudwatch:Get*",
                "cloudwatch:Describe*",
                "sts:GetCallerIdentity"
            ],
            "Resource": "*"
        }
    ]
}
```

## 🛡️ 보안 모범 사례

### 권한 분리 원칙
- **Agent 자격 증명**: Bedrock 호출 + AssumeRole만 허용
- **CLI 실행 권한**: 실제 AWS 리소스 조회 권한은 Role로 분리
- **임시 자격 증명**: AssumeRole을 통한 시간 제한된 권한 사용
