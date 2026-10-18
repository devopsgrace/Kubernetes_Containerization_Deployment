
 # Kubernetes Containerization and Deployment: Monolith to Microservices



This project demonstrates the containerization and deployment of an existing monolithic application using **Docker, Kubernetes, AWS EKS, Terraform, Jenkins, Ansible, and GitHub**.

The objective is to modernize application deployment by packaging the monolithic application into Docker containers, deploying it to Kubernetes, implementing reliable rolling updates and rollbacks, and establishing a gradual transition toward a microservices architecture.

The solution automates infrastructure provisioning, container image creation, application deployment, health checks, scaling, and release management.

---

## 🚀 Project Overview

The project implements the following capabilities:

- Uses **Git and GitHub** for source control and collaboration.
- Uses **Terraform** to provision AWS infrastructure, including networking and an Amazon EKS cluster.
- Uses **Jenkins** to automate the CI/CD pipeline.
- Uses **Docker** to package the existing application into a portable container image.
- Uses **Ansible** to automate deployment tasks and configuration where appropriate.
- Uses **Kubernetes** to manage application Pods, Deployments, Services, health checks, scaling, and rolling updates.
- Implements controlled rollbacks to recover from unsuccessful releases.
- Introduces a gradual migration strategy from a monolithic application toward independently deployable microservices.
- Uses monitoring and deployment metrics to evaluate application reliability and release performance.

### 🎯 Project Objectives

1. Containerize an existing monolithic application.
2. Deploy the application to a Kubernetes cluster.
3. Automate the build and deployment process.
4. Minimize downtime during application updates.
5. Improve scalability and application availability.
6. Implement and test rollback procedures.
7. Prepare the application for incremental microservices migration.
8. Measure deployment reliability and release cadence.

### 📊 Target Outcomes

- **80% reduction** in rollback-related incidents, to be validated against a documented baseline.
- Improved application scalability through Kubernetes replica management and autoscaling.
- More consistent and repeatable deployments.
- Faster recovery from unsuccessful releases.
- A foundation for independently deploying selected application components.

---

## 🏗️ Architecture Overview

### Architecture Diagram
The architecture should illustrate the following workflow:

Developer
    |
    v
Git Repository (GitHub)
    |
    v
Jenkins CI/CD Pipeline
    |
    +------------------------+
    |                        |
    v                        v
Build and Test          Terraform
    |                        |
    v                        v
Docker Image            AWS Infrastructure
    |                    VPC / Subnets
    v                        |
Container Registry           v
(Docker Hub or ECR)      AWS EKS Cluster
    |                        |
    +------------------------+
                             |
                             v
                     Kubernetes Deployment
                             |
               +-------------+-------------+
               |             |             |
               v             v             v
             Pod 1         Pod 2         Pod 3
               |             |             |
               +-------------+-------------+
                             |
                             v
                    Kubernetes Service
                             |
                             v
                       Application Users
```

**Supporting components**

- Ansible: deployment automation and configuration management.
- Kubernetes probes: application health monitoring.
- Kubernetes rolling updates: controlled application releases.
- Kubernetes rollout history: deployment recovery.
- Prometheus and Grafana: optional metrics collection and visualization.
- GitHub: source code, Dockerfile, Kubernetes manifests, and Terraform configuration.

**How the components interact**

1. A developer pushes code to GitHub.
2. Jenkins retrieves the source code and runs the build and tests.
3. Jenkins builds a Docker image and publishes it to a container registry.
4. Terraform provisions or updates the AWS infrastructure when required.
5. Jenkins deploys the selected image to Amazon EKS using Kubernetes manifests or Ansible.
6. Kubernetes schedules Pods, runs health checks, and exposes the application through a Service and, if required, an Ingress or load balancer.
7. If the deployment fails, the pipeline stops promotion and initiates a controlled recovery procedure.

For reference, consult the [Amazon EKS getting started guide](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html) and the [Kubernetes rolling update tutorial](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/).

---

## ✅ Prerequisites

### Accounts Required

- AWS account with permission to create the required infrastructure.
- GitHub account for source control.
- Docker Hub or Amazon Elastic Container Registry (Amazon ECR) account/repository.
- Jenkins access, either on a local machine or an EC2 instance.

### Tools Required

| Tool                   | Purpose                                    |
| ---------------------- | ------------------------------------------ |
| Git                    | Source control                             |
| GitHub                 | Repository hosting                         |
| Visual Studio Code     | Code and configuration editing             |
| Docker                 | Container image creation                   |
| AWS CLI                | AWS resource management                    |
| Terraform              | Infrastructure as Code                     |
| Jenkins                | CI/CD automation                           |
| Java and Maven         | Only if the selected application uses Java |
| kubectl                | Kubernetes cluster management              |
| Ansible                | Optional deployment automation             |
| Helm                   | Optional Kubernetes package management     |
| Amazon EKS             | Managed Kubernetes platform                |
| Prometheus and Grafana | Optional monitoring                        |

### Recommended Development Environment

A Windows computer can be used for development and Terraform execution. Use PowerShell or Git Bash for commands, and an EC2-based Linux server or containerized Jenkins environment for the CI/CD execution environment.

The commands in this README are examples. Replace the placeholder names, AWS Region, image names, ports, and application-specific commands with the values for your actual application.

---

## ⚙️ Step-by-Step Implementation

### 1. Prepare the Development Environment

Install Git, Docker Desktop, Visual Studio Code, AWS CLI, Terraform, and kubectl.

Verify the installations:

```bash
git --version
docker --version
aws --version
terraform --version
kubectl version --client
```

Configure AWS credentials using an approved authentication method. For local development, AWS CLI profiles or IAM Identity Center are preferable to embedding access keys in scripts.

```bash
aws configure
aws sts get-caller-identity
```

Do not commit AWS credentials, access tokens, private keys, or sensitive configuration files to GitHub.

### 2. Create the GitHub Repository

Create a repository named:

```text
kubernetes-containerization-deployment
```

Clone it locally:

```bash(CLI command) 
git clone https://github.com/YOUR_USERNAME/kubernetes-containerization-deployment.git
cd kubernetes-containerization-deployment
```

(OPTIONAL, Developper steps)Create the initial project structure in the repository or locally and push it to the repository GitHub: 

```text
kubernetes-containerization-deployment/
├── app/
├── Dockerfile
├── .dockerignore
├── .gitignore
├── docker-compose.yml
├── Jenkinsfile
├── ansible/
│   ├── inventory.ini
│   └── deploy.yml
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   └── versions.tf
├── kubernetes/
│   ├── namespace.yaml
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── hpa.yaml
│   └── ingress.yaml
├── tests/
└── README.md
```

The application code and build instructions depend on the monolith being migrated. The `app/` directory should contain the existing application's actual source code.

Commit the initial structure:

```bash
git add .
git commit -m "Initialize Kubernetes deployment project"
git push origin main
```

### 3. Inspect the Existing Monolithic Application

Before containerizing the application, identify:

- The programming language and runtime.
- The application entry point and build command.
- The port on which the application listens.
- Required environment variables.
- Database and external service dependencies.
- Persistent file storage requirements.
- Existing health-check endpoints.
- Authentication and secrets requirements.
- Current deployment and rollback procedures.

Document the current deployment process and baseline incident metrics.

This step is important because the Dockerfile, Kubernetes probes, resource limits, and deployment configuration must match the application's actual requirements for DevOps Environments configuration. 

### 4. Create a Dockerfile

Create a `Dockerfile` in the repository root(DevOps Roles)

The following example is a template for an application that already produces a runnable JAR file:

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

RUN useradd --system --uid 10001 appuser

COPY --chown=appuser:appuser app/target/app.jar app.jar

USER 10001

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

If the monolith is written in Node.js, Python, or another language, use an appropriate runtime image and its corresponding build process instead.

Create a `.dockerignore` file:

```text
.git
.github
.env
*.log
node_modules
target
.terraform
terraform.tfstate*
```

Adjust these exclusions to the actual application and build process. Do not exclude build artifacts that the Dockerfile needs to copy.

Build and test the image:

```bash
docker build -t monolith-app:1.0.0 .
docker run --rm -p 8080:8080 monolith-app:1.0.0
```

Verify the application locally at:(local browser)

```text
http://localhost(or IP adress of localhost):8080
```

Use the application's actual endpoint if the root URL does not serve a page.

### 5. Test the Container Locally

Before deploying to Kubernetes, verify that the container:

- Starts without errors.
- Listens on the expected port.
- Can reach required dependencies.
- Handles missing or invalid configuration correctly.
- Shuts down cleanly.
- Does not require secrets to be embedded in the image.

If the application depends on a database, configure a separate development database or an appropriate local dependency environment. Do not assume that packaging the monolith automatically packages its database.

### 6. Publish the Docker Image

Create a Docker Hub repository or an Amazon ECR repository.

For Docker Hub, use a unique version tag:

```bash
docker login
docker tag monolith-app:1.0.0 YOUR_DOCKERHUB_USERNAME/monolith-app:1.0.0
docker push YOUR_DOCKERHUB_USERNAME/monolith-app:1.0.0
```

For a team or AWS deployment, Amazon ECR is another suitable option.

Use immutable version tags or image digests for production releases rather than relying on the `latest` tag. This makes deployments reproducible and rollbacks easier to audit.

### 7. Provision AWS Infrastructure with Terraform

Create the AWS infrastructure needed by the application:

- A VPC.
- Public and private subnets across appropriate Availability Zones.
- Networking and routing.
- Required security groups.
- An Amazon EKS cluster.
- Managed node groups, if selected.
- IAM roles and permissions.
- Supporting load-balancing components, where required.

Follow the official [Terraform EKS tutorial](https://developer.hashicorp.com/terraform/tutorials/kubernetes/eks).

Initialize and validate the Terraform configuration:(on gitbash/powershell..)

```bash
cd terraform
terraform init
terraform fmt -check
terraform validate
terraform plan
```

Review the plan before making changes:

```bash
terraform apply
```

Use Terraform modules and variables to keep the configuration reusable. For a shared environment, configure remote state with an appropriate locking strategy and restricted access.

Do not hardcode AWS credentials or commit Terraform state files containing sensitive information.

### 8. Connect kubectl to Amazon EKS(on gitbash/powershell..)

After the EKS cluster is provisioned, configure kubectl:

```bash
aws eks update-kubeconfig \
  --region YOUR_AWS_REGION \
  --name YOUR_EKS_CLUSTER
```

Check the connection:

```bash
kubectl get nodes
kubectl get namespaces
kubectl get services
```

If the cluster is not ready, check AWS IAM permissions, cluster access configuration, networking, and node-group health before proceeding.

### 9. Create a Kubernetes Namespace

Create `kubernetes/namespace.yaml`:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: monolith-app
```

Apply it:

```bash
kubectl apply -f kubernetes/namespace.yaml
kubectl get namespaces
```

A dedicated namespace helps separate the application's Kubernetes resources from other workloads.

### 10. Configure the Kubernetes Deployment

Create `kubernetes/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: monolith-app
  namespace: monolith-app
spec:
  replicas: 3

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0

  selector:
    matchLabels:
      app: monolith-app

  template:
    metadata:
      labels:
        app: monolith-app

    spec:
      containers:
        - name: monolith-app
          image: YOUR_DOCKERHUB_USERNAME/monolith-app:1.0.0

          ports:
            - containerPort: 8080

          resources:
            requests:
              cpu: "100m"
              memory: "128Mi"
            limits:
              cpu: "500m"
              memory: "512Mi"

          readinessProbe:
            httpGet:
              path: /health
              port: 8080
            initialDelaySeconds: 10
            periodSeconds: 5

          livenessProbe:
            httpGet:
              path: /health
              port: 8080
            initialDelaySeconds: 20
            periodSeconds: 10
```

Replace the image with the image you published and confirm that `/health` exists and returns a successful response. Otherwise, configure a suitable health endpoint or probe type.

Apply the Deployment:

```bash
kubectl apply -f kubernetes/deployment.yaml
kubectl rollout status deployment/monolith-app -n monolith-app
kubectl get pods -n monolith-app
```

Three replicas are an initial example, not a universal production setting. Size replicas and resources according to load testing and application requirements.

### 11. Expose the Application with a Kubernetes Service

Create `kubernetes/service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: monolith-app-service
  namespace: monolith-app
spec:
  type: ClusterIP

  selector:
    app: monolith-app

  ports:
    - name: http
      port: 80
      targetPort: 8080
```

Apply the Service:

```bash
kubectl apply -f kubernetes/service.yaml
kubectl get services -n monolith-app
```

The `ClusterIP` Service exposes the application internally within the cluster.

To make the application externally accessible, configure an appropriate Ingress with an installed ingress controller or an AWS load balancer integration. Add HTTPS using a properly configured TLS certificate.

### 12. Configure Horizontal Pod Autoscaling(OPTIONAL)

Create `kubernetes/hpa.yaml`:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: monolith-app-hpa
  namespace: monolith-app
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: monolith-app

  minReplicas: 3
  maxReplicas: 10

  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

Apply the autoscaler:

```bash
kubectl apply -f kubernetes/hpa.yaml
kubectl get hpa -n monolith-app
```

CPU-based autoscaling requires the relevant metrics infrastructure, commonly Metrics Server. Configure node capacity and resource requests so that the cluster can accommodate additional Pods.

HPA scales application replicas; it does not automatically guarantee that sufficient cluster nodes are available.

### 13. Configure Jenkins for CI/CD

Install Jenkins on a suitable Linux host or run it in an appropriately configured container environment.

Install and configure the required Jenkins plugins and tools, including:

- Git integration.
- Pipeline support.
- Docker build and registry access.
- AWS authentication.
- Kubernetes deployment access.

Store credentials in Jenkins Credentials rather than in the Jenkinsfile.

Create a `Jenkinsfile` at the repository root:

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'YOUR_DOCKERHUB_USERNAME/monolith-app'
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Replace with application-specific tests"'
            }
        }

        stage('Build Image') {
            steps {
                sh '''
                    docker build \
                      -t ${IMAGE_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" |
                          docker login -u "$DOCKER_USER" --password-stdin

                        docker push ${IMAGE_NAME}:${IMAGE_TAG}
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    sed "s|IMAGE_PLACEHOLDER|${IMAGE_NAME}:${IMAGE_TAG}|g" \
                      kubernetes/deployment.yaml |
                      kubectl apply -f -

                    kubectl rollout status \
                      deployment/monolith-app \
                      -n monolith-app \
                      --timeout=180s
                '''
            }
        }
    }

    post {
        always {
            echo 'CI/CD pipeline completed. Review stage results.'
        }
    }
}
```

This is an illustrative starting point. Replace the test stage with real application tests, configure the Jenkins agent with Docker and kubectl, and provide a securely managed kubeconfig or supported AWS/EKS authentication.

For a production-quality pipeline, add image scanning, manifest validation, automated smoke tests, release approval, and failure-handling logic. A failed deployment must prevent release promotion; rollback should be triggered only through a tested and controlled process.

### 14. Automate Deployment Tasks with Ansible (Optional)

Ansible can coordinate deployment steps, validation, configuration, and operational tasks. It is not required when Jenkins can deploy directly to Kubernetes.

Create `ansible/deploy.yml`:

```yaml
---
- name: Deploy monolith application to Kubernetes
  hosts: localhost
  connection: local
  gather_facts: false

  tasks:
    - name: Apply Kubernetes manifests
      ansible.builtin.command:
        argv:
          - kubectl
          - apply
          - -f
          - kubernetes/
      changed_when: true

    - name: Wait for the application rollout
      ansible.builtin.command:
        argv:
          - kubectl
          - rollout
          - status
          - deployment/monolith-app
          - -n
          - monolith-app
          - --timeout=180s
      changed_when: false
```

Run the playbook from the repository root:

```bash
ansible-playbook ansible/deploy.yml
```

This playbook assumes that kubectl is installed and authenticated to the correct cluster. In a more advanced implementation, use versioned manifests, explicit image promotion, and appropriate Ansible Kubernetes modules where available.

---

## 🧩 Transition from Monolith to Microservices(Developper Part) 

### 17. Identify Components for Extraction

Do not split the entire application into microservices in one step. First identify logical business capabilities that can be separated with limited coupling.

For example, a sample business application might contain:

| Component        | Initial architecture | Potential target     |
| ---------------- | -------------------- | -------------------- |
| User management  | Monolith module      | User service         |
| Product catalog  | Monolith module      | Catalog service      |
| Order processing | Monolith module      | Order service        |
| Notifications    | Monolith module      | Notification service |

These are illustrative components. The actual services must be selected after examining the existing application's domain and dependencies.

### 18. Extract One Service at a Time

For each selected component:

1. Define its responsibilities and API contract.
2. Identify database ownership and dependencies.
3. Extract the component while preserving existing behavior.
4. Build a separate Docker image.
5. Create a Kubernetes Deployment and Service.
6. Add automated tests and health checks.
7. Deploy the service to a test environment.
8. Route a controlled portion of traffic to the new service, where appropriate.
9. Monitor errors, latency, and resource usage.
10. Retire the corresponding monolith functionality only after validation.

This incremental strategy reduces the risks of a large-scale rewrite and makes it easier to isolate deployment problems.

---

## 🧪 Testing and Validation(Promethus and Grafana to automate)

### 19. Validate the Kubernetes Deployment

Run the following commands:

```bash
kubectl get deployments -n monolith-app
kubectl get pods -n monolith-app
kubectl get services -n monolith-app
kubectl get hpa -n monolith-app
kubectl describe deployment monolith-app -n monolith-app
```

Test the application through port forwarding if an external endpoint is not configured:

```bash
kubectl port-forward \
  service/monolith-app-service 8080:80 \
  -n monolith-app
```

Then access `http://localhost:8080`.

Promethus and Grafana will automate the Deployment testing listed above. 

### 20. Execute the Test Plan

- **Container tests:** verify that the image builds and the application starts.
- **Functional tests:** confirm that core business operations work.
- **Deployment tests:** verify that the expected Pods become ready.
- **Rolling-update tests:** verify that a new version replaces the previous version.
- **Rollback tests:** confirm recovery from an unsuccessful deployment.
- **Scaling tests:** generate controlled load and observe replica changes.
- **Resilience tests:** terminate a Pod and confirm that Kubernetes restores the desired replica count.
- **Security tests:** scan images, restrict permissions, and verify secrets are not committed.
- **Regression tests:** confirm that application behavior remains consistent after containerization.

Record the results and keep screenshots, test logs, and deployment histories as evidence for your GitHub portfolio.

---

## 📈 Monitoring and Performance Measurement(Grafana)

### 21. Add Monitoring

For an extended implementation, integrate Prometheus and Grafana or an AWS-native monitoring solution.

Monitor:

- Pod restarts and failures.
- CPU and memory usage.
- Request latency and error rates.
- Deployment success and failure rates.
- Rollback frequency.
- Mean time to recovery.
- Release frequency and deployment duration.

Use monitoring results to identify reliability problems and evaluate the impact of the migration.

### 22. Measure the 80% Rollback-Incident Reduction

Establish a baseline before claiming an improvement.

A possible measurement is:

[
\text{Reduction (%)} =
\frac{\text{Baseline incidents} - \text{New incidents}}
{\text{Baseline incidents}}\times100
]

For example, if the baseline is 10 rollback-related incidents per comparable measurement period and the new process records 2, the reduction is 80%.

Compare equivalent periods and deployment volumes. Document the measurement window, incident definition, data source, and deployment frequency so that the result is reproducible.

---

## 🛠️ Tools and Technologies

| Category                 | Technology                                  |
| ------------------------ | ------------------------------------------- |
| Version Control          | Git, GitHub                                 |
| Containerization         | Docker                                      |
| Container Registry       | Docker Hub or Amazon ECR                    |
| Infrastructure as Code   | Terraform                                   |
| Cloud Platform           | AWS                                         |
| Kubernetes Platform      | Amazon EKS                                  |
| CI/CD                    | Jenkins                                     |
| Configuration Management | Ansible                                     |
| Kubernetes Management    | kubectl                                     |
| Application Build        | Maven, if using Java                        |
| Monitoring               | Prometheus, Grafana or AWS monitoring tools |
| Security                 | IAM, image scanning, least-privilege access |

---

## 📌 Summary

This project demonstrates a practical DevOps workflow for modernizing an existing monolithic application.

- Docker standardizes application packaging.
- Terraform provisions the cloud infrastructure.
- Amazon EKS provides managed Kubernetes orchestration.
- Jenkins automates build, test, and deployment stages.
- Ansible can coordinate deployment and operational tasks.
- Kubernetes supports health checks, scaling, rolling updates, and rollback.
- A gradual microservices migration provides a path toward independently deployable application components.

The final result should be a reproducible deployment workflow supported by automated tests, deployment evidence, and measurable reliability metrics.

---

## 📚 Official Documentation

- [Amazon EKS Getting Started](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html)
- [Terraform: Provision an EKS Cluster](https://developer.hashicorp.com/terraform/tutorials/kubernetes/eks)
- [Kubernetes Rolling Updates and Rollbacks](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/)
- [Docker Documentation](https://docs.docker.com/)
- [Jenkins Pipeline Documentation](https://www.jenkins.io/doc/book/pipeline/)
- [Ansible Documentation](https://docs.ansible.com/)

**Repository:** `https://github.com/YOUR_USERNAME/kubernetes-containerization-deployment`

**Project status:** Update this README with the actual application, architecture diagram, test results, deployment screenshots, and verified performance metrics as the implementation progresses.
