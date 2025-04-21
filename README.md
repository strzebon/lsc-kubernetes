# Kubernetes Application with NFS on AWS EKS

## Instructions

Using AWS Console create cluster in Elastic Kubernetes Service. Once it is done, add nodes to cluster.

```bash
aws configure set default.region us-east-1
aws configure set default.output table
aws eks --region us-east-1 update-kubeconfig --name my-cluster
```

Using Helm, install an NFS server and provisioner in the cluster

```bash
helm repo add nfs-ganesha-server-and-external-provisioner https://kubernetes-sigs.github.io/nfs-ganesha-server-and-external-provisioner/
helm install nfs-server-provisioner nfs-ganesha-server-and-external-provisioner/nfs-server-provisioner  --set storageClass.name=nfs-storage --set storageClass.defaultClass=true
```

Create a Persistent Volume Claim which will bind to a NFS Persistent Volume provisioned dynamically by the provisioner installed in the previous step

```bash
kubectl apply -f pvc.yaml
```

Create a Deployment with a HTTP server (e.g., apache or nginx). The web content directory should be mounted as a volume using the PVC created in the previous step

```bash
kubectl apply -f deployment.yaml
```

Create a Service associated with the Pod(s) of the HTTP server Deployment

```bash
kubectl apply -f service.yaml
```

Create a Job which mounts the PVC and copies a sample content through the shared NFS PV

```bash
kubectl apply -f job.yaml
```

Check EXTERNAL-IP of the HTTP server

```bash
kubectl get svc web-service
```
