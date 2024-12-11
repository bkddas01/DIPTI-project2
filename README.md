# Task 2

## 1. Create a PersistentVolume (PV)
Create PersistentVolume definition that uses local storage\
pv.yaml:




To deploy this project run

```yaml
  apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

## 2. Create a PersistentVolumeClaim (PVC)
A PersistentVolumeClaim is a request for storage by a user. This is how you specify the volume size, access mode, and other parameters.\
pvc.yaml

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi

```
## 3. Deploy an Alpine-based Application
Now that you have a PersistentVolume and a PersistentVolumeClaim in place, you can deploy your Alpine application, ensuring it uses the PVC for persistent storage.

Deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: project-alpine
spec:
  replicas: 2
  selector:
    matchLabels:
      app: alpine
  template:
    metadata:
      labels:
        app: alpine
    spec:
      containers:
        - name: alpine
          image: alpine:latest
          command: ["sleep", "3600"]  # Keeps the container running
          volumeMounts:
            - name: alpine-storage
              mountPath: /data  # Mount the volume at this path in the container
      volumes:
        - name: alpine-storage
          persistentVolumeClaim:
            claimName: pv-claim  # PVC that was defined earlier
```

## 4. Apply the Configurations

```bash
kubectl apply -f pv.yaml
kubectl apply -f pvc.yaml
kubectl apply -f Deployment.yaml

```
## 5. Verify the Deployment
After applying the configurations, check if everything is running correctly:

* Check the PV and PVC:
```bash
kubectl get pv
kubectl get pvc
```
* Verify the pod:
```bash
kubectl get pods
```
* Also need To check the volume is correctly mounted by describing the pod:

```bash
kubectl describe pod <pod-name>

```
