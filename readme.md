### EKS Cluster Creation
---
```yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: eks-demo
  region: ${AWS_REGION}
  version: "1.33"

vpc:
  cidr: "10.0.0.0/16"
  nat:
    gateway: Single

managedNodeGroups:
  - name: node-group
    instanceType: m5.large
    desiredCapacity: 3
    volumeSize: 20
    privateNetworking: true
    iam:
      withAddonPolicies:
        imageBuilder: true
        cloudWatch: true
        autoScaler: true
        ebs: true

cloudWatch:
  clusterLogging:
    enableTypes: ["*"]
```
---
🔹 apiVersion: eksctl.io/v1alpha5  
This specifies the version of the eksctl configuration format.  
  
v1alpha5 is the latest supported version that includes all the current features.

🔹 kind: ClusterConfig  
This tells eksctl that the file describes a cluster configuration.

🔹 metadata  
Basic metadata for the EKS cluster.

---
```yaml
name: eks-demo
region: ${AWS_REGION}
version: "1.33"
```

- `name`: Name of the EKS cluster.  
- `region`: AWS region to deploy the cluster in (e.g., us-east-1, ap-south-1). Here, it uses a placeholder `${AWS_REGION}` — ensure this is set in your shell before running eksctl.  
- `version`: Kubernetes version (1.33 here).
🔹 vpc

```yaml
cidr: "10.0.0.0/16"
nat:
  gateway: Single
```

Specifies custom VPC settings:

- `cidr`: IP range for the VPC.  
- `nat.gateway: Single`: A single NAT Gateway will be used to allow private subnets to access the internet (needed for `privateNetworking: true`).

🔹 managedNodeGroups

```yaml
- name: node-group
  instanceType: m5.large
  desiredCapacity: 3
  volumeSize: 20
  privateNetworking: true
  iam:
    withAddonPolicies:
      imageBuilder: true
      cloudWatch: true
      autoScaler: true
      ebs: true
```

Defines a Managed Node Group for the EKS cluster (i.e., a group of EC2 instances that will run your Kubernetes workloads):

- `name`: Identifier for this node group.  
- `instanceType`: EC2 instance type (`m5.large` = 2 vCPU, 8 GB RAM).  
- `desiredCapacity`: Number of EC2 worker nodes to start with (here 3).  
- `volumeSize`: Size of the root EBS volume (in GB).  
- `privateNetworking: true`: Nodes will not have public IPs; they’ll be placed in private subnets.  
- `iam.withAddonPolicies`: Enables IAM policies for common add-ons:  
  - `imageBuilder`: For building container images.  
  - `cloudWatch`: To allow node logs to go to Amazon CloudWatch.  
  - `autoScaler`: To allow the Kubernetes Cluster Autoscaler to function.  
  - `ebs`: Allows the use of Amazon EBS volumes in pods.

🔹 cloudWatch

```yaml
clusterLogging:
  enableTypes: ["*"]
```

Enables cluster-level logging via Amazon CloudWatch.

- `enableTypes: ["*"]`: Enables all types of cluster logs (`api`, `audit`, `authenticator`, `controllerManager`, `scheduler`, etc.).

```
eksctl create cluster -f eks-demo-cluster.yaml
```

✅ **Summary:**  
This config will:

- Create an EKS cluster named `eks-demo` in the specified region.  
- Use Kubernetes version 1.33.  
- Set up a VPC with NAT gateway and private subnets.  
- Launch a node group with 3 `m5.large` instances, all in private subnets.  
- Enable IAM policies for various AWS add-ons.  
- Enable full cluster logging to CloudWatch.