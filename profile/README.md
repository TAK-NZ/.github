# Team Awareness Kit for New Zealand (TAK.NZ)

## Background

The [Team Awareness Kit (TAK)](https://tak.gov/solutions/emergency) provides Fire, Emergency Management, and First Responders an operationally agnostic tool for improved situational awareness and a common operational picture. 

TAK.NZ provides multiple repos to deploy TAK infrastructure on AWS. These repos are based on the work of the [Colorado Centre of Excellence for Advanced Technology Aerial Firefighting](https://github.com/dfpc-coe), the driving force behind [COTAK](https://cotak.gov/) - Colorado’s free real-time location service for emergency responders. 

## Architecture Layers

The TAK infrastructure is provided through multiple layers, each in a distinct GitHib repo. Layers can be deployed in multiple independent environments. As an example:

```
        PRODUCTION ENVIRONMENT                DEMO/TESTING ENVIRONMENT              DEVELOPMENT ENVIRONMENT
        Domain: tak.nz                        Domain: demo.tak.nz                   Domain: dev.tak.nz
        Deployed via CI/CD                    Deployed via CI/CD                    Deployed manually

┌─────────────────────────────────┐    ┌─────────────────────────────────┐    ┌─────────────────────────────────┐
│         MediaInfra              │    │         MediaInfra              │    │         MediaInfra              │
│    CloudFormation Stack         │    │    CloudFormation Stack         │    │    CloudFormation Stack         │
└─────────────────────────────────┘    └─────────────────────────────────┘    └─────────────────────────────────┘
                │                                        │                                     │
                ▼                                        ▼                                     ▼
┌─────────────────────────────────┐    ┌─────────────────────────────────┐    ┌─────────────────────────────────┐
│         CloudTAK                │    │         CloudTAK                │    │         CloudTAK                │
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

| Layer | Description | Availability | CI/CD pipeline |
|-------|-------------|--------------|----------------|
| [**BaseInfra**](https://github.com/TAK-NZ/base-infra)  | Foundation: VPC, ECS, S3, KMS, ACM | ✅ Available as CDK project | [![Demo Testing](https://github.com/TAK-NZ/base-infra/actions/workflows/demo-deploy.yml/badge.svg)](https://github.com/TAK-NZ/base-infra/actions/workflows/demo-deploy.yml) [![Production](https://github.com/TAK-NZ/base-infra/actions/workflows/production-deploy.yml/badge.svg)](https://github.com/TAK-NZ/base-infra/actions/workflows/production-deploy.yml) |
| [**AuthInfra**](https://github.com/TAK-NZ/auth-infra) | SSO via Authentik, LDAP | ✅ Available as CDK project | [![Demo Testing](https://github.com/TAK-NZ/auth-infra/actions/workflows/demo-deploy.yml/badge.svg)](https://github.com/TAK-NZ/auth-infra/actions/workflows/demo-deploy.yml) [![Production](https://github.com/TAK-NZ/auth-infra/actions/workflows/production-deploy.yml/badge.svg)](https://github.com/TAK-NZ/auth-infra/actions/workflows/production-deploy.yml) |
| [**TAKInfra**](https://github.com/TAK-NZ/tak-infra) | TAK Server | ✅ Available as CDK project | [![Demo Testing](https://github.com/TAK-NZ/tak-infra/actions/workflows/demo-deploy.yml/badge.svg)](https://github.com/TAK-NZ/tak-infra/actions/workflows/demo-deploy.yml) [![Production](https://github.com/TAK-NZ/tak-infra/actions/workflows/production-deploy.yml/badge.svg)](https://github.com/TAK-NZ/tak-infra/actions/workflows/production-deploy.yml) |
| [**CloudTAK**](https://github.com/TAK-NZ/CloudTAK) | CloudTAK web interface, ETL | ✅ Available as CDK project | [![Demo Testing](https://github.com/TAK-NZ/CloudTAK/actions/workflows/demo-deploy.yml/badge.svg)](https://github.com/TAK-NZ/CloudTAK/actions/workflows/demo-deploy.yml) [![Production](https://github.com/TAK-NZ/CloudTAK/actions/workflows/production-deploy.yml/badge.svg)](https://github.com/TAK-NZ/CloudTAK/actions/workflows/production-deploy.yml) |
| [**MediaInfra**](https://github.com/TAK-NZ/media-infra) | Media Streaming | 🚧 CDK project in progress | |

**Deployment Order**: BaseInfra must be deployed first, followed by AuthInfra, TakInfra, CloudTAK, and finally MediaInfra. Each layer imports outputs from layers below via CloudFormation exports.

Each stacks is provided in a `Prod` type with full resiliency built-in and a `Dev-Test` type with reduced resiliency for optimized cost. 

## Extract, Transform, Load (ETL)

TAK.NZ uses CloudTAK's ETL capabilities to extract, transform and load 3rd party data into the TAK ecosystem. The following ETL are currently in use:

| ETL | Description | Data Source | Availability | CI/CD pipeline |
|-----|-------------|-------------|--------------|----------------|
| [**ETL-ADSBX**](https://github.com/TAK-NZ/etl-adsbx)  | Aircraft location data via Automatic Dependent Surveillance-Broadcast (ADS-B) | [ADSBExchange.com](https://www.adsbexchange.com/) | ✅ | [![Build and Deploy ETL](https://github.com/TAK-NZ/etl-adsbx/actions/workflows/etl-deploy.yml/badge.svg)](https://github.com/TAK-NZ/etl-adsbx/actions/workflows/etl-deploy.yml) |
| [**ETL-AISHub**](https://github.com/TAK-NZ/etl-aishub) | Vessel locations based on automatic identification system (AIS) data | [AISHub](https://www.aishub.net/) | ✅ | [![Build and Deploy ETL](https://github.com/TAK-NZ/etl-aishub/actions/workflows/etl-deploy.yml/badge.svg)](https://github.com/TAK-NZ/etl-aishub/actions/workflows/etl-deploy.yml) |
| [**ETL-GeoNet-Quakes**](https://github.com/TAK-NZ/etl-geonet-quakes)  | Earthquake epicenter locations | [GeoNet New Zealand](https://www.geonet.org.nz/) | ✅ | [![Build and Deploy ETL](https://github.com/TAK-NZ/etl-geonet-quakes/actions/workflows/etl-deploy.yml/badge.svg)](https://github.com/TAK-NZ/etl-geonet-quakes/actions/workflows/etl-deploy.yml) |
| [**ETL-GeoJSON**](https://github.com/TAK-NZ/etl-geojson)  | Any GeoJSON data from an HTTP(S) endpoint | [NZ Transport Agency Waka Kotahi](https://nzta.govt.nz/), [GeoNet New Zealand](https://www.geonet.org.nz/) | ✅ | [![Build and Deploy ETL](https://github.com/TAK-NZ/etl-geojson/actions/workflows/etl-deploy.yml/badge.svg)](https://github.com/TAK-NZ/etl-geojson/actions/workflows/etl-deploy.yml) |
| [**ETL-InReach**](https://github.com/TAK-NZ/etl-inreach)  | Garmin InReach satellite messenger tracking | [Garmin InReach](https://www.garmin.com/en-NZ/c/outdoor-recreation/satellite-communicators/) | ✅ | [![Build and Deploy ETL](https://github.com/TAK-NZ/etl-inreach/actions/workflows/etl-deploy.yml/badge.svg)](https://github.com/TAK-NZ/etl-inreach/actions/workflows/etl-deploy.yml) |


## Try this yourself

Deploy the TAK.NZ stack (Team Awareness Kit) on AWS in ~1 hour with our [One-line installer](https://tak.nz/tak-deploy/).

## Examples

TAK.NZ CloudTAK web based TAK client interface. Various real-time data imported with CloudTAK Extract/Transform/Load (ETL) functions are displayed. 

![TAK.NZ CloudTAK Screenshot](images/TAK-NZ-CloudTAK-Example.png)
