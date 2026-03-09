# Lab 4: Deployment Strategies and Storage Troubleshooting

## Scenario
The staging environment requires a stateful logging application that must retain logs across pod restarts but not across pod recreation. Additionally, you need to test a blue-green deployment strategy for a critical update to the logging application.

## Lab Description
The student will create a Deployment with `emptyDir` storage, test its behavior, and then perform a blue-green deployment using labels and services. They will troubleshoot a storage-related failure.

## Prerequisites
- Minikube or Kubernetes cluster running
- `staging` namespace created (`kubectl create namespace staging`)

## Container Images and Details

| Component | Image | Purpose |
|-----------|-------|---------|
| logger v1 | `busybox:latest` | Writes timestamp to file every 10 seconds |
| logger v2 | `busybox:latest` | Writes timestamp + hostname to file every 10 seconds |

## Tasks to Implement

### Part 1: Initial Logger Deployment with emptyDir

Create the initial logger Deployment in the `staging` namespace:

- **Name:** `logger`
- **Replicas:** 1
- **Container image:** `busybox:latest`
- **Container command:**
  ```bash
  /bin/sh -c "while true; do date >> /log/output.txt; sleep 10; done"
  ```
- **Add label:** `app: logger`, `version: v1`
- **Add an `emptyDir` volume mounted at `/log`**

### Part 2: Test Storage Behavior

1. **Simulate a container crash:**
   - Exec into the pod and kill the main process (PID 1)
   - Observe what happens to the pod
   - Check if the log file persists after container restart

2. **Delete the pod manually:**
   - Delete the specific pod running the logger
   - Wait for the Deployment to recreate it
   - Check if the old logs are present in the new pod

### Part 3: Blue-Green Deployment Setup

1. **Create a service for the logger:**
   - **Name:** `logger-service`
   - **Type:** ClusterIP
   - **Port:** 80 (target port doesn't matter since this is a logging app, but we need a service for the exercise)
   - **Selector:** `version: v1`

2. **Prepare the new version (v2) Deployment:**
   - **Name:** `logger-v2`
   - **Replicas:** 2 (to ensure at least one works during troubleshooting)
   - **Container image:** `busybox:latest`
   - **Container command (includes hostname):**
     ```bash
     /bin/sh -c "while true; do echo \"\$(date) - Host: \$(hostname)\" >> /log/output.txt; sleep 10; done"
     ```
    - **Add label:** `app: logger`, `version: v2`
    - **INTENTIONAL BUG:** Mount the `emptyDir` volume at `/var/log/app` instead of `/log`

### Part 4: Blue-Green Switch and Troubleshooting

1. **Perform the blue-green switch:**
   - Update the `logger-service` selector to `version: v2`
   - Observe that one of the `logger-v2` pods enters `CrashLoopBackOff`

2. **Troubleshoot and fix the issue:**
   - Diagnose why the pod is crashing
   - Fix the volume mount path in the `logger-v2` Deployment
   - Verify both v2 pods are running successfully
   - Confirm the service is now sending traffic to v2 pods

### Part 5: Verification

- **Verify the fix:**
  - Check logs from a v2 pod to confirm timestamps and hostnames are being written
  - Exec into a v2 pod and verify the log file exists and contains data
  - Test service discovery by running a temporary busybox pod and curling the service (optional)

---

# Required Deliverables:
- Students must push the following to their public GitHub repository:

## README Content Requirements

The README must contain the following sections:

| Section | Required Content |
|---------|------------------|
| Lab Summary | Brief overview of what was accomplished in this lab |
| Storage Behavior Explanation | Clear explanation of what happens to `emptyDir` data when:<br>- A container crashes and restarts<br>- A pod is deleted and recreated by the Deployment |
| Blue-Green Deployment Steps | Step-by-step process followed to perform the blue-green switch |
| Troubleshooting Section | Description of the `logger-v2` pod failure:<br>- What caused the `CrashLoopBackOff`<br>- How it was diagnosed (commands used)<br>- How it was fixed |
| Commands Used | Complete list of all `kubectl` commands executed during the lab |

## Manifests Directory Requirements

| Path | What to Include |
|------|-----------------|
| `manifests/v1/logger-deployment.yaml` | Original v1 Deployment with `emptyDir` volume mounted at `/log` |
| `manifests/v2/logger-deployment.yaml` | Fixed v2 Deployment (after troubleshooting) with correct volume mount |
| `manifests/service/logger-service.yaml` | Service manifest showing both v1 and v2 selector states (or final version with v2 selector) |