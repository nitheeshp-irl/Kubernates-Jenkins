This repository contains the configuration files to deploy a Jenkins master as a Kubernetes StatefulSet with an integrated Horizontal Pod Autoscaler (HPA).

Overview

This configuration deploys a Jenkins master node using the following Kubernetes resources:

StatefulSet: Ensures consistent pod identity and persistent storage for Jenkins data.

Horizontal Pod Autoscaler (HPA): Monitors CPU utilization and adjusts the number of replicas accordingly.

Prerequisites

A Kubernetes cluster with kubectl configured.

A PersistentVolumeClaim (PVC) named jenkins-home to store Jenkins data.

A service account named jenkins-service-account with the necessary permissions.

Deployment Details

StatefulSet Configuration

Name: jenkins-standalone-statefulset

Service Name: jenkins-service

Image: jenkins/jenkins:lts

Replicas: 1

Volume: Persistent storage mounted at /var/jenkins_home.

Probes:

Liveness Probe: Ensures the Jenkins master pod is running.

Readiness Probe: Ensures the pod is ready to serve traffic.

Resource Requests and Limits:

Requests: 512Mi memory, 500m CPU

Limits: 1Gi memory, 1 CPU

Security Context:

fsGroup: 1000

runAsUser: 1000

Horizontal Pod Autoscaler Configuration

Name: jenkins-hpa

Target Resource: The Jenkins StatefulSet.

Scaling Parameters:

Minimum replicas: 1

Maximum replicas: 1 (can be increased based on workload requirements).

Target CPU Utilization: 80%

Deployment Steps

Apply the PersistentVolumeClaim (PVC) for Jenkins data

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: jenkins-home
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi

Save this to a file (e.g., pvc.yaml) and apply it:

kubectl apply -f pvc.yaml

Deploy the StatefulSet and HPA

Save the provided YAML configuration to a file (e.g., jenkins-deployment.yaml) and apply it:

kubectl apply -f jenkins-deployment.yaml

Verify the deployment

kubectl get statefulsets
kubectl get pods
kubectl get hpa

Accessing Jenkins

Expose the Jenkins service

Expose the service using a LoadBalancer or Ingress (not included in this configuration).

Access Jenkins

Access Jenkins through the browser at the service URL.

Retrieve the initial admin password

kubectl exec -it <jenkins-pod-name> -- cat /var/jenkins_home/secrets/initialAdminPassword

Customization

Adjust resource requests and limits based on your cluster capacity.

Modify the HPA parameters for scaling according to your needs.

Update the jenkins-home PVC definition for custom storage requirements.

Troubleshooting

Check pod logs for errors

kubectl logs <jenkins-pod-name>

Describe the StatefulSet for debugging

kubectl describe statefulset jenkins-standalone-statefulset

Validate HPA metrics

kubectl top pod

Contributing

Contributions are welcome! Please open an issue or submit a pull request.

License

This repository is licensed under the MIT License. See the LICENSE file for details.