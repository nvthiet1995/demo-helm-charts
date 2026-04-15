## 1. Prerequisites:
- k8s cluster
- helm installed

```sh
helm version
```

## 2. Test Helm
### 2.1. Create Helm and install app to k8s cluster
```sh
helm create my-chart
helm install my-app ./my-chart
kubectl get all
```
👉 Can see Pod/Deployment → OK

### 2.2 Remove (delele app)

```
helm uninstall my-app
```
👉 Delete all resources
- Deployment
- Service
- Pod

👉 Delete `release` as well

