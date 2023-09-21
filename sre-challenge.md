# Designing and Implementing a Resilient and Scalable Platform

## Infrastructure Platform Selection

Choice: AWS (Amazon Web Services)

### Reasoning

**Security**: AWS offers a vast array of compliance certifications, fine-grained IAM policies, and VPC network isolation.

**Integration with DevOps tools**: AWS has tight integrations with popular CI/CD tools, simplifying the automation pipeline.

**Service Maturity**: AWS services, especially the compute services, are mature with a variety of features.

## Orchestration Technology Selection

Choice: EKS (Amazon Elastic Kubernetes Service)

Reasoning:

* EKS offers a managed Kubernetes cluster that can run containers without needing to manage the Kubernetes control plane.
* It's scalable, highly available, and integrates well with AWS services.
* Provides automatic patching, scaling, and updates.

## EKS Scalability

EKS can be scaled both at the node level and the pod level, allowing you to accommodate varying workloads and demands.

There are several ways scalabity is achieved in EKS. 

* Managed Node Groups
* Auto Scaling Groups
* Pod Scaling
  * Horizontal Pod Autoscaler (HPA): Kubernetes supports automatic scaling of the number of pods in a deployment or replica set based on observed CPU utilization or other select performance metrics.
  * Vertical Pod Autoscaler (VPA):
  VPA automatically adjusts the CPU and memory reservations for your pods, allowing them to scale vertically based on resource requirements.

* Karpenter: AWS Karpenter is a scalable, open-source cluster autoscaler built for Kubernetes.Karpenter differs from the traditional Kubernetes Cluster Autoscaler in a few key ways, offering several benefits and functionalities tailored for Amazon EKS (Elastic Kubernetes Service) and other Kubernetes environments

So, for this solution, we are going to use the following to achieve the scalability.

* Managed Node groups
* Karpenter
* HPA & VPA

## EKS Resilience and falt tolerance

In EKS resilience and falt tolerance is achieved in many ways.

The Control plane is managed by the AWS across multiple availability Zones(AZs) ensuring high availability and fault tolerance. Similarly the nodes and storage have in built resilience and high availability.


## Infrastructure Automation

All the necessary infrastructure will be created using Terraform. The infrastucture will following components

- EKS
- VPC

EKS and VPC will be deployed using prebuilt terraform modules which readily available to use. These prebuilt modules are battle tested and used by many teams. 

To automate the Terraform changes, we can either go with Terraform cloud or classic Jenkins pipelines or Github actions.

In this solution, I am going with Terraform cloud for the simplicity, state management and easy integration with github terraform repo. 

## Microservice Deployment Strategy

I assume each microservice has a Dockerfile. when a new commit is pushed to the git repo, The Github action will kick in and build the micro service image and push it to the ECR registry.

Helm charts will be used to deploy the microservices to the EKS cluster using the ArgoCD.

Deployment will be done through GitOps strategy with ArgoCD.

## Infrastructure Testing

The infrastructure is created with terraform so terratest is the best tool for the testing.

## Configuration Management

All the project configurations will be stored in git repo. sensitive data will be stored in AWS secrets manager.

use of full blown configuration tools like Ansible or chef is not required unless it is absolutely necessary. In this use case we do not need a separate configuration management tool

## Monitoring Approach

Prometheus, Grafana will be used as a monitoring solution. 

There is a git repo called Kube Prometheus and it has fully packaged prometheus and Grafana monitoring solution ready to be deployed as CRDs.

https://github.com/prometheus-operator/kube-prometheus

