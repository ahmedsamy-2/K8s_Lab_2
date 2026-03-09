# Lab 1: From Pods to Deployments - My First Application

## Scenario
You've proven you can run a single pod. Now your tech lead wants you to deploy a web application that can handle failures automatically. You need to move from a single pod to a Deployment to ensure high availability.

## Lab Description
The student will create an Nginx Deployment, understand how it manages pods through ReplicaSets, and perform basic scaling and updates.

## Tasks to Implement

1. Using declarative YAML, create a Deployment named `web-app` with the `nginx:1.23` image. Start with 1 replica. Add a label `app: web`.

2. Use `kubectl` commands to observe the Pod Lifecycle as the Deployment creates the pod. Identify the ReplicaSet created by the Deployment.

3. Scale the Deployment to 3 replicas using an imperative command. Verify the new pods are created and spread across the cluster's pod networking.

4. Update the Deployment to use the `nginx:1.24` image. Watch the rolling update process and observe how new pods are created and old ones are terminated.

5. Roll back the Deployment to the previous version (`nginx:1.23`) using `kubectl rollout undo`.