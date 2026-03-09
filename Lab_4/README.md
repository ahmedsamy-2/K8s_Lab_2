# Lab 4: Deployment Strategies and Storage Troubleshooting

## Scenario
The staging environment requires a stateful logging application that must retain logs across pod restarts but not across pod recreation. Additionally, you need to test a blue-green deployment strategy for a critical update to the logging application.

## Lab Description
The student will create a Deployment with `emptyDir` storage, test its behavior, and then perform a blue-green deployment using labels and services. They will troubleshoot a storage-related failure.

## Tasks to Implement

1. In the `staging` namespace, create a Deployment named `logger` with 1 replica. The container should write a timestamp to `/log/output.txt` every 10 seconds. Use an `emptyDir` volume mounted at `/log`.

2. Simulate a container crash (kill the main process). This reinforces Pod Lifecycle and storage behavior.

3. Now delete the pod manually. When the Deployment recreates it, check if the old logs are present. Explain the behavior.

4. **Blue-Green Deployment:** 
   - Create a new service named `logger-service` that selects the `logger` Deployment (using a label like `version: v1`).
   - Prepare a new Deployment (`logger-v2`) in the same namespace with a different label (`version: v2`) and the same application but with an additional feature (e.g., it also writes the hostname).

5. Perform a blue-green switch by updating the `logger-service` selector to point to `version: v2`. Verify traffic now goes to the new pods.

6. **Troubleshooting:** During the switch, one of the new `logger-v2` pods enters `CrashLoopBackOff`. Use troubleshooting skills to diagnose and fix it (e.g., the container tries to write to a different path that doesn't have the volume mount).

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