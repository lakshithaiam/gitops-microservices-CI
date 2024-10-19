# 11-Tier Microservices Application - Online Boutique

This project showcases a DevOps pipeline implementation for deploying an 11-tier microservices application based on Google’s Online Boutique e-commerce demo. The pipeline automates the deployment and monitoring of microservices in a cloud-native environment using Kubernetes and AWS infrastructure.

## Acknowledgements

- The microservices source code used in this project is adapted from the [Online Boutique microservices-demo project](https://github.com/GoogleCloudPlatform/microservices-demo), which is licensed under the Apache 2.0 License.

## Microservice Architecture
![Architecture Diagram](https://github.com/lakshithaiam/MicroserviceApp/blob/main/architecture-diagram.jpg)


## CI/CD Pipeline

The CI/CD pipeline automates the entire lifecycle of building, deploying, and monitoring microservices. It utilizes:

- Git for source code management,
- Jenkins for continuous integration and deployment,
- Docker for containerization,
- Kubernetes for orchestration,
- Terraform for provisioning AWS infrastructure (EC2, VPCs, security groups, and EKS clusters),
- Prometheus and Grafana for monitoring.
Detailed steps are outlined in the workflow section.

## Detailed Workflow

### Code Commit to GitHub

- **Developer Action**: Developers push code changes to the respective branch for each microservice in the GitHub repository.
- **Branches**: There are 11 separate branches, one for each microservice (e.g., frontend, cartservice, checkoutservice, etc.). Each branch contains a Jenkinsfile for pipeline configuration.

### GitHub Webhook Triggers Jenkins

- **Trigger**: Upon each push, a GitHub webhook notifies Jenkins.
- **Jenkins**: Jenkins runs a multibranch pipeline that automatically detects changes in each branch and triggers a build process for the respective microservice.

### Jenkins CI Pipeline

- **Checkout Code**: Jenkins pulls the latest code from the relevant branch in the GitHub repository.
- **Build Docker Images**:
   - Jenkins uses Docker to build images for each microservice.
   - Each microservice is packaged into a container image, following the Dockerfile located in its branch.
- **Push Docker Images to Docker Hub**: 
   - Once the build succeed, the Docker image is tagged and pushed to Docker Hub. Each microservice has its own image repository.

### Infrastructure and Networking

- **Provisioning with Terraform**: 
  - Terraform is used to provision the necessary AWS infrastructure: EC2 instances, VPCs, security groups, and the EKS cluster.
  - Terraform ensures that the infrastructure is scalable and secure, utilizing auto-scaling groups for EC2 instances and network security groups for fine-tuned access control.
- **Load Balancing**: 
  - The application is exposed via a load balancer (managed by AWS), which distributes incoming traffic across multiple replicas of the frontend service.

### Kubernetes Deployment

- **Kubernetes Configuration**: Jenkins triggers the deployment process using Kubernetes manifests stored in the repository.
- **Deploy to EKS**:
  - Kubernetes pulls the Docker images from Docker Hub and deploys the microservices to the AWS EKS cluster.
  - **Namespace**: Microservices are deployed into a specific namespace within the Kubernetes cluster.
  - **Scaling**: Kubernetes manages automatic scaling (using the Horizontal Pod Autoscaler) based on load, ensuring the application can handle traffic spikes.

### Application Monitoring

- The application is monitored using Prometheus for metrics collection and Grafana for real-time visualization of system health. Dashboards provide insights into microservice performance, resource utilization, and system bottlenecks.

### Scaling and Self-Healing

- **Autoscaling**: Kubernetes automatically scales pods up or down based on resource utilization, ensuring the application remains responsive under high load.
- **Self-Healing**: In the event of a service failure or a pod crash, Kubernetes will automatically restart the failed pods, ensuring high availability and resilience.

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any improvements or suggestions. 

## License

This project is licensed under the MIT License. You can find more details in the [LICENSE](./LICENSE) file.