# Team Awareness Kit for New Zealand (TAK.NZ)

## Background

The [Team Awareness Kit (TAK)](https://tak.gov/solutions/emergency) provides Fire, Emergency Management, and First Responders an operationally agnostic tool for improved situational awareness and a common operational picture. 

TAK.NZ provides multiple repos to deploy TAK infrastructure on AWS. These repos are based on the work of the [Colorado Centre of Excellence for Advanced Technology Aerial Firefighting](https://github.com/dfpc-coe), the driving force behind [COTAK](https://cotak.gov/) - Colorado’s free real-time location service for emergency responders. 


A TAK infrasructure is deployed as a stack consisting of multiple layers: 

| Layer Name            | Function | Github repo |
| --------------------- | ----- | ----- |
| `coe-base-<name>`     | VPC, ECS cluster, and ECR repository | [github.com/TAK-NZ/base-infra](https://github.com/TAK-NZ/base-infra) |
| `coe-auth-<name>`     | Authentication layer using Authentik | [github.com/TAK-NZ/auth-infra](https://github.com/TAK-NZ/auth-infra) |
| `coe-tak-<name>`      | TAK Server layer | [github.com/TAK-NZ/tak-infra](https://github.com/TAK-NZ/tak-infra) |

Each stacks is provided in a `Production` type with full resiliency built-in and a `Dev-Test` type with reduced resiliency for optimized cost. 
