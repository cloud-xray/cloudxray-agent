# Deployment notes for CloudXray Kubernetes agent

This software component (agent) is a part of CloudXray Engineering Knowledge Generation Platform.

Prerequisites:
1. Tools: git, kubectl, helm
2. Permissions to deploy a new Helm chart in the target Kubernetes cluster
3. A logical name (like "eks-prod1") assigned to the target K8s cluster. While K8s does not have the concept of a cluster name, we use this name to identify the cluster in the CloudXray console UI.

Please use the following procedure to deploy the agent in your Kubernetes cluster:
1. If you don't already have a CloudXray customer account then go to CloudXray console UI https://app.cloudxray.co/ to sign up for the service
2. In your CloudXray user account, create a new K8s token by heading to "Admin -> Integrations -> K8s Integration" menu and clicking "Add New K8s Account Configuration" button. Specify a meaningful token name and click "Create" button. Please create new K8s token for each K8s cluster that you want to connect to CloudXray.
3. Save the generated unique token in a secure place
4. Create a local clone of the repository and change directory to it

```
git clone https://github.com/we-will-rename-it-soon/cloudxray-agent.git

cd cloudxray-agent
```

5. Deploy the CloudXray K8s agent using the following command (replace <AGENT_TOKEN> with the token created in the CloudXray console UI, <CLUSTER_NAME> with the unique logical name to be associated with configuration data collected from the K8s cluster):
```
helm install k8s-agent helm/k8s-agent --set general.agentToken="<AGENT_TOKEB>" \
 --set general.clusterName=<CLUSTER_NAME> --create-namespace -n cloudxray
```

For example:

```
helm install k8s-agent helm/k8s-agent --set \
general.agentToken="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9eyJlbWFpbCI6InNodWJtZSI6Ik5ld0VLUyJ9Fa8uiafE" \
 --set general.clusterName=cxr-eks --create-namespace -n cloudxray
 ```

6. The Helm chart should be installed in a new namespace named "cloudxray". Please verify that the namespace has one running pod called "k8s-agent". For example:

```
$ kubectl get pods -n cloudxray
NAME                         READY   STATUS    RESTARTS   AGE
k8s-agent-6f7484cdc7-6snvf   1/1     Running   0          8m
$
```

7. After 5-10 minutes head to the CloudXray console UI and start asking questions about your K8s cluster.

# Notes
- The agent does not collect K8s "secret" resources
- K8s data is collected on agent startup and after that every one hour