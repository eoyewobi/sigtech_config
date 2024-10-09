# Sigtech Task Config

This repo contains the Argo CD deployment for both helm package and the more conventional manifests.

```commandline
.
├── README.md
├── application.yml
├── dev
│   ├── app-deployment.yml
│   ├── app-ingress.yml
│   ├── app-service.yml
│   ├── mysql-sealed-secret.yml
│   ├── mysql-service.yml
│   └── mysql-statefulset.yml
├── helm-application.yml
└── python-mysql-app
    ├── Chart.yaml
    ├── charts
    ├── templates
    │   ├── NOTES.txt
    │   ├── _helpers.tpl
    │   ├── app-deployment.yaml
    │   ├── app-ingress.yaml
    │   ├── app-service.yaml
    │   ├── mysql-sealed-secret.yaml
    │   ├── mysql-service.yaml
    │   ├── mysql-statefulset.yaml
    │   └── tests
    └── values.yaml

6 directories, 20 files


```
Getting Started

Prerequisites

Argo CD

Helm

kubectl

Sealed Secrets Controller

Deploying with Argo CD
There are two approaches for deploying the application using Argo CD:

1. Using Conventional Kubernetes Manifests
Apply the Argo CD Application Manifest:

Deploy the application defined in application.yml:

```commandline
kubectl apply -f application.yml
```

Synchronize the Argo CD Application:

In the Argo CD dashboard, find the application and click "Sync" to deploy the resources defined in the dev/ directory.

2. Using the Helm Chart
Apply the Argo CD Application Manifest for Helm:

Deploy the Helm-based application defined in helm-application.yml:

```commandline
kubectl apply -f helm-application.yml
```

Synchronize the Argo CD Application:

In the Argo CD dashboard, find the Helm-based application and click "Sync" to deploy the Helm chart from the python-mysql-app/ directory.

Configuring the Application
Environment-Specific Settings
The configuration for the application is defined in:

dev/ directory for conventional Kubernetes manifests
python-mysql-app/values.yaml for Helm deployments
Make sure to update the configuration values, such as database credentials and service ports, according to your environment.

Sealed Secrets
The project uses Sealed Secrets to manage sensitive information securely. Update the mysql-sealed-secret.yml or mysql-sealed-secret.yaml template to reflect your encrypted credentials, using the mycert.pem for encryption.

```commandline
kubectl create secret generic mysql-secret \
  --from-literal=MYSQL_ROOT_PASSWORD=your-root-password \
  --from-literal=MYSQL_DATABASE=your-database \
  --dry-run=client -o yaml | kubeseal --cert mycert.pem --format yaml > dev/mysql-sealed-secret.yml
```

Accessing the Application
The application is exposed via an Ingress resource (app-ingress.yml or Helm template). Configure the DNS or /etc/hosts file to route traffic to the service.
MySQL is exposed as a ClusterIP service (mysql-service.yml or Helm template), accessible within the cluster.
Customization
You can modify the following configurations:

Helm Chart Values: Customize python-mysql-app/values.yaml to adjust settings like the image version, database credentials, or resource requests/limits.
Kubernetes Manifests: Update files in the dev/ directory to change deployment strategies, ingress rules, or service types.
Troubleshooting
Argo CD Sync Issues:

Check the Argo CD application logs for details.
Ensure the repository is correctly configured with the required permissions.
Database Connection Errors:

Verify the MySQL service is running and accessible within the cluster.
Confirm the database credentials are correctly set in the sealed secrets.
Helm Template Errors:

Use helm template to render the chart and inspect the output for syntax or value issues.
Validate Kubernetes manifests with kubectl apply --dry-run=client -f <file>.

Useful Commands
Re-sync an Argo CD Application:

```commandline
argocd app sync <application-name>
```

Port Forwarding:
```commandline
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8081:80
```


Decrypt Sealed Secrets:

```commandline
kubeseal --recovery-unseal --cert mycert.pem < dev/mysql-sealed-secret.yml
```

