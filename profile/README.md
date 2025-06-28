# Team Awareness Kit for New Zealand (TAK.NZ)

## Background

The [Team Awareness Kit (TAK)](https://tak.gov/solutions/emergency) provides Fire, Emergency Management, and First Responders an operationally agnostic tool for improved situational awareness and a common operational picture. 

TAK.NZ provides multiple repos to deploy TAK infrastructure on AWS. These repos are based on the work of the [Colorado Centre of Excellence for Advanced Technology Aerial Firefighting](https://github.com/dfpc-coe), the driving force behind [COTAK](https://cotak.gov/) - Colorado’s free real-time location service for emergency responders. 

### Architecture Layers

The TAK infrastructure is provided through multiple layers, each in a distinct GitHib repo. Layers can be deployed in multiple independent environments. As an example:

```
        PRODUCTION ENVIRONMENT                DEMO/TESTING ENVIRONMENT              DEVELOPMENT ENVIRONMENT
        Domain: tak.nz                        Domain: demo.tak.nz                   Domain: dev.tak.nz

┌─────────────────────────────────┐    ┌─────────────────────────────────┐    ┌─────────────────────────────────┐
│         CloudTAK                │    │         CloudTAK                │    │         CloudTAK                │
│    CloudFormation Stack         │    │    CloudFormation Stack         │    │    CloudFormation Stack         │
└─────────────────────────────────┘    └─────────────────────────────────┘    └─────────────────────────────────┘
                │                                        │                                     │
                ▼                                        ▼                                     ▼
┌─────────────────────────────────┐    ┌─────────────────────────────────┐    ┌─────────────────────────────────┐
│        VideoInfra               │    │        VideoInfra               │    │        VideoInfra               │
│    CloudFormation Stack         │    │    CloudFormation Stack         │    │    CloudFormation Stack         │
└─────────────────────────────────┘    └─────────────────────────────────┘    └─────────────────────────────────┘
                │                                        │                                     │
                ▼                                        ▼                                     ▼
┌─────────────────────────────────┐    ┌─────────────────────────────────┐    ┌─────────────────────────────────┐
│         TakInfra                │    │         TakInfra                │    │         TakInfra                │
│    CloudFormation Stack         │    │    CloudFormation Stack         │    │    CloudFormation Stack         │
└─────────────────────────────────┘    └─────────────────────────────────┘    └─────────────────────────────────┘
                │                                        │                                     │
                ▼                                        ▼                                     ▼
┌─────────────────────────────────┐    ┌─────────────────────────────────┐    ┌─────────────────────────────────┐
│        AuthInfra                │    │        AuthInfra                │    │        AuthInfra                │
│    CloudFormation Stack         │    │    CloudFormation Stack         │    │    CloudFormation Stack         │
└─────────────────────────────────┘    └─────────────────────────────────┘    └─────────────────────────────────┘
                │                                        │                                     │
                ▼                                        ▼                                     ▼
┌─────────────────────────────────┐    ┌─────────────────────────────────┐    ┌─────────────────────────────────┐
│        BaseInfra                │    │        BaseInfra                │    │        BaseInfra                │
│    CloudFormation Stack         │    │    CloudFormation Stack         │    │    CloudFormation Stack         │
└─────────────────────────────────┘    └─────────────────────────────────┘    └─────────────────────────────────┘
```

| Layer | Repository | Description | Availability | CI/CD pipeline |
|-------|------------|-------------|--------------|----------------|
| **BaseInfra** | [`base-infra`](https://github.com/TAK-NZ/base-infra)  | Foundation: VPC, ECS, S3, KMS, ACM | ✅ Available as CDK project | [![Demo Testing](https://github.com/TAK-NZ/base-infra/actions/workflows/demo-deploy.yml/badge.svg)](https://github.com/TAK-NZ/base-infra/actions/workflows/demo-deploy.yml) [![Production](https://github.com/TAK-NZ/base-infra/actions/workflows/production-deploy.yml/badge.svg)](https://github.com/TAK-NZ/base-infra/actions/workflows/production-deploy.yml) |
| **AuthInfra** | [`auth-infra`](https://github.com/TAK-NZ/auth-infra) | SSO via Authentik, LDAP | ✅ Available as CDK project | [![Demo Testing](https://github.com/TAK-NZ/auth-infra/actions/workflows/demo-deploy.yml/badge.svg)](https://github.com/TAK-NZ/auth-infra/actions/workflows/demo-deploy.yml) [![Production](https://github.com/TAK-NZ/auth-infra/actions/workflows/production-deploy.yml/badge.svg)](https://github.com/TAK-NZ/auth-infra/actions/workflows/production-deploy.yml) |
| **TAKInfra** | [`tak-infra`](https://github.com/TAK-NZ/tak-infra) | TAK Server | ✅ Available as CDK project |
| **VideoInfra** | [`video-infra`](https://github.com/TAK-NZ/video-infra) | Video Server based on Mediamtx | ❌ Not yet available |
| **CloudTAK** | [`CloudTAK`](https://github.com/TAK-NZ/CloudTAK) | CloudTAK web interface and ETL | 🚧 CDK project in progress |

**Deployment Order**: BaseInfra must be deployed first, followed by AuthInfra, TakInfra, VideoInfra, and finally CloudTAK. Each layer imports outputs from the layer below via CloudFormation exports.

Each stacks is provided in a `Prod` type with full resiliency built-in and a `Dev-Test` type with reduced resiliency for optimized cost. 

### Estimated Cost Breakdown (BaseInfra + AuthInfra + TakInfra)
Estimated for ap-southeast-2 in USD.

#### Development Environment (~$220/month)
- **BaseInfra**: ~$44/month (VPC, ECS cluster, S3, KMS, ACM)
- **AuthInfra**: ~$85/month (Authentik, LDAP, Aurora, Redis)
- **TakInfra**: ~$91/month (TAK Server, Aurora, EFS)

#### Production Environment (~$778/month)
- **BaseInfra**: ~$143/month (VPC, ECS cluster, S3, KMS, ACM, VPC endpoints)
- **AuthInfra**: ~$245/month (Authentik HA, LDAP, Aurora Multi-AZ, Redis cluster)
- **TakInfra**: ~$390/month (TAK Server HA, Aurora Multi-AZ, enhanced monitoring)
