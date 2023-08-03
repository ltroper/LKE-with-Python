# LKE-with-Python

Welcome to the comprehensive guide on using the Kubernetes (k8s) client for Python to interact with Linode Kubernetes Engine (LKE). In this guide, we'll explore how to utilize the k8s client for Python to harness the full potential of LKE. You'll learn the basics, set up your Python environment, and perform various operations, such as creating and managing pods and more.

### 1. Create LKE Cluster

Go to the Linode Kubernetes Engine (LKE) page in your Linode dashboard. Click on the "Create a Cluster" button to initiate the cluster creation process. Set a name for your cluster, select a region where the cluster will be hosted, and choose the Kubernetes version you want to use. Choose the number of nodes you want, their type (Standard, Dedicated CPU, or GPU), and the desired node size. Click the "Create" button to deploy the LKE cluster.

Once the cluster is created, download the Kubeconfig.

### 2. Download Python and kubectl

```
brew install python3
brew install kubectl
```

### 3. Create a python file kube.py to define a deploment and service

```
from kubernetes import client, config

config.load_kube_config("<path to kubeconfig>")
api_client = client.ApiClient()

deployment = client.V1Deployment(
    metadata=client.V1ObjectMeta(name="my-app"),
    spec=client.V1DeploymentSpec(
        replicas=3,
        selector=client.V1LabelSelector(
            match_labels={"app": "my-app"}
        ),
        template=client.V1PodTemplateSpec(
            metadata=client.V1ObjectMeta(
                labels={"app": "my-app"}
            ),
            spec=client.V1PodSpec(
                containers=[
                    client.V1Container(
                        name="my-container",
                        image="nginx",
                        ports=[client.V1ContainerPort(container_port=80)]
                    )
                ]
            )
        )
    )
)

api_instance = client.AppsV1Api(api_client)
api_instance.create_namespaced_deployment(
    namespace="default",
    body=deployment
)

service=client.V1Service(
    metadata=client.V1ObjectMeta(name="my-service"),
    spec=client.V1ServiceSpec(
        selector={"app": "my-app"},
        ports=[client.V1ServicePort(port=5000)]
    )
)


api_instance = client.CoreV1Api(api_client)
api_instance.create_namespaced_service(
    namespace="default",
    body=service
)
```

### 4. Run the file to deploy 
```
python3 kube.py
```

