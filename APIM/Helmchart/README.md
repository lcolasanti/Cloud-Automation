# Helmchart for Amplify API-Management

## Introduction

Helmcharts are compatible with Axway Amplify API Management 7.7. 

This helmchart uses Helm V3 and is compliant a minimal Kubernetes version of 1.11.

## Prerequisite

This helmchart requires the following capabilities on the Kubernetes cluster:
- A minimal kubernetes version 1.11
- An nginx ingress-controller like nginx or Azure Application Gateway.
- A storage class with in RWM.
- A storage class with in RWO.
- A total resources of 6vcpu and 8Go memory spread on 2 nodes.
- A container registry with API-Gateway and API-Portal images

## Documentation TBD.