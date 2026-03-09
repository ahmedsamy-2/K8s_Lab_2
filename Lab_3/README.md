# Lab 3: Multi-Environment Deployments with Namespace Isolation

## Scenario
Your team now manages applications across Development (dev) and Staging (staging) environments within the same cluster. You need to deploy a two-tier application (frontend and backend) in both environments using Deployments, expose them appropriately, and ensure proper isolation and internal communication patterns.

## Lab Description
The student will create `dev` and `staging` namespaces, deploy a frontend and backend application using Deployments, and expose them with Services. They will also configure the frontend to communicate with the backend via DNS.

## Tasks to Implement

1. Create two namespaces: `dev` and `staging`.

2. In each namespace:
   - Create a backend Deployment (e.g., `redis:alpine` or a custom Python HTTP server) with 1 replica.
   - Expose the backend Deployment internally with a ClusterIP service named `backend`.
   - Create a frontend Deployment (e.g., `nginx`) with 2 replicas.
   - Expose the frontend Deployment internally with a ClusterIP service named `frontend`.

3. Configure the frontend pods (e.g., via ConfigMap or environment variable) to connect to the backend service using the name `backend` within its namespace.

4. **Pod Networking Challenge:** From a temporary debug pod in the `dev` namespace, demonstrate that you can resolve `backend` to the dev backend service, and `backend.staging.svc.cluster.local` to the staging backend service.

5. Perform a rolling update on the frontend Deployment in `staging` only, changing the Nginx page to say "Staging Environment". Verify the `dev` frontend remains unchanged.