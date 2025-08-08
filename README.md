# 🤖 AWS 전문 AI 어시스턴트

> **Model Context Protocol (MCP)** 기반의 AWS 전문 AI 어시스턴트

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![Streamlit](https://img.shields.io/badge/Streamlit-1.28+-red.svg)
![AWS](https://img.shields.io/badge/AWS-Bedrock-orange.svg)
![MCP](https://img.shields.io/badge/MCP-Protocol-green.svg)

## 📋 프로젝트 개요

Model Context Protocol을 활용하여 AWS 리소스 관리와 문서 검색을 자동화하는 지능형 챗봇 시스템입니다. Claude 3.5 Sonnet과 Cross-Account Role을 통해 안전하고 효율적인 AWS 운영을 지원합니다.

### 🎯 핵심 기능
- **AWS CLI 자동화**: 자연어로 AWS 리소스 조회 및 관리
- **지능형 문서 검색**: AWS 공식 문서 실시간 검색 및 분석
- **Cross-Account 보안**: Assume Role을 통한 안전한 권한 관리
- **실시간 진행 표시**: AI 작업 과정 시각화

## 🔧 동작 원리

```
사용자 질문 → Streamlit UI → LangChain Agent (MCP Client)
                                      ↓
                              Claude 3.5 Sonnet
                                      ↓
                            (어떤 MCP 도구를 사용할지 판단)
                                      ↓
                              MCP Server → AWS API/문서
                                      ↓
                              결과 데이터 반환
                                      ↓
                              Claude 3.5 Sonnet
                                      ↓
                            사용자 친화적 답변 생성
                                      ↓
                              Streamlit UI → 사용자
```

## 🚀 빠른 시작

### 1. 설치
```bash
git clone <repository-url>
cd mcp_test
pip install -r requirements.txt
```

### 2. 환경 설정
`.env` 파일 생성:
```env
AWS_ACCESS_KEY_ID=your-access-key-here
AWS_SECRET_ACCESS_KEY=your-secret-key-here
AWS_REGION=ap-northeast-2
```

### 3. AWS 권한 설정

#### Agent 기본 자격 증명 권한
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
        },
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::*:role/*"
        }
    ]
}
```

#### Cross-Account Role 설정
**Trust Policy:**
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::AGENT_ACCOUNT_ID:user/YOUR_AGENT_USER_NAME"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

**Permission Policy:**
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
                "sts:GetCallerIdentity"
            ],
            "Resource": "*"
        }
    ]
}
```

### 4. 실행
```bash
streamlit run streamlit_chat_server.py
```

### 5. 사용
1. 사이드바에서 **Role ARN** 입력 (필수)
2. AWS CLI 또는 문서 검색 탭 선택
3. 자연어로 질문 입력

## 💡 사용 예시

### AWS CLI 명령어
- "현재 실행 중인 EC2 인스턴스를 테이블로 보여줘"
- "S3 버킷별 용량과 암호화 상태를 확인해줘"
- "Lambda 함수들의 메모리 사용량을 비교해줘"

### AWS 문서 검색
- "ECS Fargate 서비스 생성 방법을 단계별로 알려줘"
- "Lambda 환경 변수 설정 모범 사례 문서 찾아줘"
- "S3 버킷 정책 작성 가이드를 검색해줘"

## 🛠️ 기술 스택

- **Frontend**: Streamlit
- **AI Model**: Claude 3.5 Sonnet (AWS Bedrock)
- **Protocol**: Model Context Protocol (MCP)
- **Backend**: Python 3.10+
- **Integration**: LangChain
- **MCP Servers**: 
  - `awslabs.aws-api-mcp-server` (AWS CLI)
  - `awslabs.aws-documentation-mcp-server` (AWS 문서)

## 🔐 보안 특징

- **권한 분리**: Agent 기본 자격 증명과 CLI 실행 권한 분리
- **Cross-Account**: Assume Role을 통한 안전한 계정 간 접근
- **읽기 전용**: 모든 AWS 작업은 조회(Describe/List/Get)만 허용
- **임시 자격 증명**: 시간 제한된 권한 사용

## 📊 주요 성과

- ✅ **MCP 프로토콜 활용**: 최신 AI-시스템 통합 표준 적용
- ✅ **보안 강화**: Cross-Account Role 기반 권한 관리
- ✅ **실시간 처리**: 비동기 처리를 통한 응답성 향상
- ✅ **사용자 경험**: 직관적 UI와 진행 상황 시각화
