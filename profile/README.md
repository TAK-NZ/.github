# Team Awareness Kit for New Zealand (TAK.NZ)

## Background

The [Team Awareness Kit (TAK)](https://tak.gov/solutions/emergency) provides Fire, Emergency Management, and First Responders an operationally agnostic tool for improved situational awareness and a common operational picture. 

TAK.NZ provides multiple repos to deploy TAK infrasructure on AWS. These repos are heavily influence by the work of the [Colorado Centre of Excellence for Advanced Technology Aerial Firefighting](https://github.com/dfpc-coe), the driving force behind [COTAK](https://cotak.gov/) - Colorado’s free real-time location service for emergency responders. 


A TAK infrasructure is deployed as a stack consisting of multiple layers: 

| Layer Name            | Function |
| --------------------- | ----- |
| `coe-base-<name>`     | VPC, ECS cluster, and ECR repository - [repo](https://github.com/TAK-NZ/base-infra)      |
| `coe-auth-<name>`     | Authentication layer using Authentik - [repo](https://github.com/TAK-NZ/auth-infra)      |
| `coe-tak-<name>`      | TAK Server layer - [repo](https://github.com/TAK-NZ/tak-infra)      |

Multiple stacks, e.g. for `Staging` and `Production` use can be deployed in parallel. 
