
### Task Overview
- **Application**: Socks Shop example microservice application
- **Tools**: Ansible or Terraform, Prometheus for monitoring
- **Requirements**: HTTPS with Let’s Encrypt, network perimeter security, Ansible Vault for sensitive information

### Step-by-Step Solution

1. **Provision Infrastructure**
   - Choose an IaaS provider (e.g., AWS, Azure, GCP).
   - Use Terraform to define and provision the infrastructure.
   - Example Terraform configuration:
     ```hcl
     provider "aws" {
       region = "us-west-2"
     }

     resource "aws_instance" "example" {
       ami           = "ami-0c55b159cbfafe1f0"
       instance_type = "t2.micro"
     }
     ```

2. **Deploy Kubernetes Cluster**
   - Use Terraform to deploy a Kubernetes cluster.
   - Example configuration:
     ```hcl
     module "eks" {
       source          = "terraform-aws-modules/eks/aws"
       cluster_name    = "my-cluster"
       cluster_version = "1.21"
       subnets         = ["subnet-12345678", "subnet-87654321"]
     }
     ```

3. **Deploy Socks Shop Application**
   - Use Kubernetes manifests or Helm charts to deploy the Socks Shop application.
   - Example Kubernetes manifest:
     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: socks-shop
     spec:
       replicas: 3
       selector:
         matchLabels:
           app: socks-shop
       template:
         metadata:
           labels:
             app: socks-shop
         spec:
           containers:
           - name: socks-shop
             image: weaveworksdemos/sockshop
             ports:
             - containerPort: 80
     ```

4. **Setup Monitoring with Prometheus**
   - Deploy Prometheus using Helm:
     ```bash
     helm install prometheus prometheus-community/prometheus
     ```

5. **Configure HTTPS with Let’s Encrypt**
   - Use Cert-Manager to manage Let’s Encrypt certificates.
   - Example configuration:
     ```yaml
     apiVersion: cert-manager.io/v1
     kind: ClusterIssuer
     metadata:
       name: letsencrypt
     spec:
       acme:
         server: https://acme-v02.api.letsencrypt.org/directory
         email: your-email@example.com
         privateKeySecretRef:
           name: letsencrypt
         solvers:
         - http01:
             ingress:
               class: nginx
     ```

6. **Secure Infrastructure**
   - Use security groups or firewall rules to restrict access.
   - Example security group rule in Terraform:
     ```hcl
     resource "aws_security_group_rule" "allow_http" {
       type        = "ingress"
       from_port   = 80
       to_port     = 80
       protocol    = "tcp"
       cidr_blocks = ["0.0.0.0/0"]
     }
     ```

7. **Encrypt Sensitive Information with Ansible Vault**
   - Encrypt sensitive files:
     ```bash
     ansible-vault encrypt vars.yml
     ```
