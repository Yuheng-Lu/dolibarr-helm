# Dolibarr Helm Chart

This repository contains a Helm chart for deploying Dolibarr, an open-source ERP and CRM software, along with a MariaDB database as its backend. The MariaDB dependency is managed through Bitnami's Helm chart repository for easy deployment and configuration.

## Deployment Instructions

### Add the Bitnami Repository (if not already added)

Before deploying, ensure you have added the Bitnami Helm repository and updated it:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

### Deploy the Chart

To deploy Dolibarr along with its required MariaDB instance, run the following command:

```bash
helm install dolibarr ./chart
```

This will deploy Dolibarr with a MariaDB instance in your Kubernetes cluster.

## Customization

You can customize various aspects of the Dolibarr deployment by editing the `values.yaml` file. This includes settings such as:

- **Resource limits and requests**: Specify memory and CPU allocations for the Dolibarr and MariaDB containers.
- **Service configuration**: Adjust the service type (e.g., ClusterIP, NodePort), port, and nodePort values.
- **Persistence options**: Enable or disable persistence for both Dolibarr and MariaDB, and specify storage class and size requirements.

### Creating the MariaDB Secret

If you prefer to use an existing Kubernetes secret for MariaDB credentials, specify the name of this secret in the `existingSecret` field in `values.yaml`. The example below demonstrates the structure of a Kubernetes secret to be referenced in `existingSecret`:

#### Example `mariadb-secret.yaml`

Replace `base64-encoded-root-password`, `base64-encoded-user-password`, and `base64-encoded-username` with your base64-encoded values.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-mariadb-secret
type: Opaque
data:
  mariadb-root-password: base64-encoded-root-password
  mariadb-password: base64-encoded-user-password
  mariadb-username: base64-encoded-username
```

> **Note**: Use the command `echo -n "your-password" | base64` to generate base64-encoded passwords.

Once the secret is created and applied to your Kubernetes cluster:

```bash
kubectl apply -f mariadb-secret.yaml
```

Specify this secret’s name in `values.yaml` under `mariadb.auth.existingSecret`:

```yaml
mariadb:
  auth:
    existingSecret: "my-mariadb-secret"
```

If you choose to define the password, username, and other details directly in `values.yaml`, leave `existingSecret` as blank or do not include it.

## Dependency

This Helm chart relies on the MariaDB Helm chart from the Bitnami repository, version `^11.5.2`. This dependency is defined in `Chart.yaml` as follows:

```yaml
dependencies:
  - name: mariadb
    version: ^11.5.2
    repository: https://charts.bitnami.com/bitnami
```

Make sure the dependency is available in your Helm environment to ensure a successful deployment.
