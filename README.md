Fork this repo, so you can make changes and open PR

### Infrastructure
Details for instance:
AMI - ami-0a90581127c6c8677

Security group - sg-0d712d9df362c2213

Region - eu-west-1

Type - t2.meduim

Key pair - devops-test

You need to tag instance during creation - tag key `Name` tag value `your IAM username`
Create an instance from that AMI (you have only console access)
Use t2.medium type 
SSH into that instance. In that instance we have Kubernetes cluster provisioned with minikube. It takes some time to start the cluster, so server can refuse connections for few minutes.

You need to build and deploy application (install any tools you need to accomplish that). Use `deploy` folder from repo for deployment.
Instance would be unavailable after 3 hours, it's ok if not everything was done. 
Our main goal is to check your basic Docker/Kubernetes knowledge and test your troubleshooting skills.

### Task 
- Application depends on mysql, please deploy it using https://bitnami.com/stack/mysql/helm
- Build docker image for that application (Dockerfile included in the project)
- Deploy application into that cluster (use image you've built)
- Application has some issues, we need to troubleshoot and fix it.
- Application should return node name on `/get-node` endpoint, it expects node name as NODE_NAME env variable. You need to configure that env variable
- Check with curl if application is available from host and `/get-node` returns node name
- Change deployment to wait for database connection to be up and running before start
- Change deployment, so all pods would be equally distributed across available nodes
- We want to provide limited access to the cluster for cluster group `developers`, they should have access only to default namespace and shouldn't see secrets values
All changes should be added into this repo, so we'll be able to use it as a GitOps repo
Create PR with those changes

Suggest how we can implement next items. Add your suggestions into this README file and include into PR 
- We want to manage secrets in external tool and want them to be synced into Kubernetes
- We need to add monitoring and logging (please explain a bit your choices)

### Suggestions

Managing Secrets in External Tool and Syncing into Kubernetes
1. Choose an External Secrets Management Tool:

- HashiCorp Vault: A widely used tool for secrets management that supports dynamic secrets, encryption as a service, and tight access control mechanisms.
- AWS Secrets Manager : Cloud provider solutions that offer secrets management as a service, with built-in integration for other cloud services.

2. Integrate the External Secrets Tool with Kubernetes:

- Use the Kubernetes External Secrets Operator: This operator allows you to automatically sync secrets from external secrets management tools into Kubernetes. It watches for changes in your external secrets storage and reflects those changes in your Kubernetes secrets.
- Inject Secrets into Pods: Use Vault's sidecar injector  to fetch secrets from Vault and inject them into your containers at runtime.

- The Vault Agent Sidecar pattern is a method used to securely introduce secrets into Kubernetes pods from HashiCorp Vault. It leverages the Vault Agent, which is a client daemon that automates the process of authentication with Vault and the management of secrets. When used as a sidecar container within a Kubernetes pod, the Vault Agent can continuously authenticate with Vault, retrieve secrets, and manage their lifecycle. 
The Vault Agent writes the retrieved secrets to a shared volume within the pod, usually a memory-based filesystem like tmpfs to reduce the risk of secret exposure. Applications within the pod can read secrets from this shared volume.

3. Configure Access and Policies:

Ensure that the tool you choose is properly configured to securely manage access to secrets. This involves setting up roles, policies, and identity brokering mechanisms.
Securely configure the communication between your Kubernetes cluster and the external secrets management tool, using mutual TLS or another secure method.

Monitoring:

Prometheus and Grafana: 
Prometheus for metrics collection and Grafana for visualization is a popular combination. Prometheus can scrape metrics from each Kubernetes node and service, while Grafana allows you to create dashboards for easy visualization of those metrics.
Why Prometheus and Grafana?
 Prometheus's design is a good fit for Kubernetes. It is cloud-native and designed for dynamic environments. Grafana's versatility in creating insightful, comprehensive dashboards enhances the value of the collected metrics.

Logging:

Elasticsearch, Fluentd, and Kibana (EFK Stack) or Fluentbit and CloudWatch: These stacks are widely used for logging. Fluentd can collect and parse logs, Elasticsearch as the search and analytics engine, and Kibana for visualization. The same is true for Fluentbit, a new generation of agents for collecting and parsing logs. CloudWatch for storing analyze logs and CloudWatch dashboard for visualization. 

I have experience with setting up a Grafana and Prometheus, and fluent bit together with Cloudwatch. But choosing tolls should depend on the projects needs( money, high load ) and of course on the current realisation of this tools ( if exist) 
