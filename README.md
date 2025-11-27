# Kubernetes Deployment for Three-Tier Web Application

This repository contains the Kubernetes manifest files for deploying a three-tier web application. The application consists of a frontend, a backend, and a database, all orchestrated within a Kubernetes cluster.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Deployment Steps](#deployment-steps)
- [Accessing the Application](#accessing-the-application)
- [Project Structure](#project-structure)
- [Cleanup](#cleanup)
- [Contributing](#contributing)
- [License](#license)

## Overview

These Kubernetes manifests define the necessary resources (Deployments, Services, Persistent Volume Claims, etc.) to run a three-tier web application. The goal is to provide a scalable, resilient, and easily manageable deployment using Kubernetes.

## Architecture

The application follows a standard three-tier architecture:

1.  **Frontend**: User interface, typically served by a web server.
2.  **Backend**: Application logic, communicating with the frontend and the database.
3.  **Database**: Data storage for the application.

Each tier is deployed as separate Kubernetes Deployments and exposed via Services.

```
  +----------------------+      +----------------------+      +------------------+
  |                      |      |                      |      |                  |
  |   Frontend Pods      | <--> |   Backend Pods       | <--> |    Database Pod  |
  |   (Deployment)       |      |   (Deployment)       |      |    (Deployment)  |
  |                      |      |                      |      |                  |
  +----------+-----------+      +----------+-----------+      +----------+-------+
             |                              |                              |
  +----------v-----------+      +----------v-----------+      +----------v-------+
  |   Frontend Service   | <--> |   Backend Service    | <--> |   Database Service |
  | (e.g., LoadBalancer) |      |   (e.g., ClusterIP)  |      |   (e.g., ClusterIP) |
  +----------------------+      +----------------------+      +------------------+
```

## Prerequisites

Before deploying, ensure you have the following installed and configured:

-   **`kubectl`**: The Kubernetes command-line tool, configured to connect to your cluster.
-   **A Kubernetes Cluster**: This can be:
    -   **Minikube**: For local development.
    -   **Kind**: For local development with Docker.
    -   A cloud-managed Kubernetes service (e.g., GKE, EKS, AKS).
-   **Docker (or compatible container runtime)**: If you need to build images locally.
-   **Helm (Optional)**: If the deployment uses Helm charts. (Adjust this section if Helm is not used).

## Deployment Steps

Follow these steps to deploy the application to your Kubernetes cluster:

1.  **Clone the repository:**
    ```bash
    git clone <your-repository-url>
    cd tree-tier-web-app
    ```

2.  **Create Namespace (Optional but Recommended):**
    It's good practice to deploy applications into their own namespace.
    ```bash
    kubectl create namespace my-app-namespace
    ```

3.  **Apply Kubernetes Manifests:**
    From the root of the project directory, apply all the manifest files.
    ```bash
    # If using a specific namespace
    kubectl apply -f . -n my-app-namespace
    # Or if not using a specific namespace (deploys to default)
    # kubectl apply -f .
    ```
    This command will create all the Deployments, Services, PersistentVolumeClaims, and other resources defined in the YAML files.

4.  **Verify Deployment:**
    Check the status of your pods and services:
    ```bash
    kubectl get pods -n my-app-namespace
    kubectl get services -n my-app-namespace
    kubectl get deployments -n my-app-namespace
    ```

## Accessing the Application

Once deployed, you can access the application. The method depends on how your frontend service is exposed (e.g., LoadBalancer, NodePort, Ingress).

1.  **For LoadBalancer Service (Cloud Environments):**
    ```bash
    kubectl get service frontend-service -n my-app-namespace
    ```
    Look for the `EXTERNAL-IP` of the `frontend-service`. It might take a few minutes for the external IP to be provisioned.

2.  **For NodePort Service (Local/Minikube/Kind):**
    If your frontend service is of type `NodePort`, you can find the port:
    ```bash
    kubectl get service frontend-service -n my-app-namespace
    ```
    Then, access it via `http://<Node-IP>:<NodePort>`.
    If using Minikube, you can often use:
    ```bash
    minikube service frontend-service -n my-app-namespace
    ```

3.  **For Ingress (if configured):**
    If you have an Ingress controller and Ingress resources defined, you'll access the application via the Ingress's external IP or hostname.
    ```bash
    kubectl get ingress -n my-app-namespace
    ```

## Project Structure

```
/tree-tier-web-app
├── /k8s             # Directory containing all Kubernetes manifest files
│   ├── fe-app.yaml
│   ├── db-app.yaml
│   ├── mongo-express.yaml
    └── README.md
```

*(Note: Adjust the file names and directory structure above to match your actual project.)*

## Cleanup

To remove all deployed Kubernetes resources:

```bash
kubectl delete -f ./k8s/ --namespace my-app-namespace
kubectl delete namespace my-app-namespace # If you created a dedicated namespace
```

## Contributing

Contributions are welcome! Please feel free to open an issue or submit a pull request.

## License

This project is licensed under the MIT License. See the `LICENSE` file for more details.
