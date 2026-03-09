# Lab 2: Exposing Your Deployment and Basic Troubleshooting

## Scenario
The development team wants to test the web-app Deployment from inside the cluster. You need to expose it as a service and then intentionally break it to practice your troubleshooting skills on a non-responsive application.

## Lab Description
The student will expose a Deployment using a Service and then practice Deployment troubleshooting by introducing common failures and diagnosing them.

## Tasks to Implement

1. Use the `web-app` Deployment from Lab 1 (with 3 replicas).

2. Expose the Deployment using a ClusterIP service named `web-service` that targets port 80. Use an imperative command to create the service.

3. Verify the service endpoints correctly list all 3 pod IPs.

4. **Troubleshooting Task 1:** One of the pods becomes unresponsive. Simulate this by `exec`-ing into one pod and stopping Nginx (e.g., `kill 1` or `nginx -s stop`). Observe the Pod Lifecycle and note what the Deployment does. Check the service endpoints again.

5. **Troubleshooting Task 2:** Introduce a configuration error. Edit the Deployment to use a non-existent image tag: `nginx:broken`. Watch the rollout status and the new pods that get stuck.

6. Use `kubectl rollout status`, `kubectl describe pod <broken-pod>`, and `kubectl logs <broken-pod>` to diagnose the image pull failure. Then, fix the image tag to `nginx:latest` and verify the rollout succeeds.