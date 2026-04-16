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

## 3. Test rollback with Helm
### 3.1. Create new folder structrue like `test-chart/helm-rollback-lab`
### 3.1.1 Pull docker image to local first to prevent ImagePullBackoff
```sh
docker pull nginx:1.25
docker pull nginx:1.27
docker image ls
docker images
```
### 3.2. Install version 1
```sh
helm install my-app ./helm-rollback-lab
```
Result as below:
```
NAME: my-app
LAST DEPLOYED: Thu Apr 16 23:33:58 2026
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```
### 3.3 Update tag in values.yaml from `1.25` to `1.27`
### 3.4 Apply upgrade
```sh
helm upgrade my-app ./helm-rollback-lab
```
Result as below:
```
Release "my-app" has been upgraded. Happy Helming!
NAME: my-app
LAST DEPLOYED: Thu Apr 16 23:37:15 2026
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
```

### 3.5 Verify pod with new tag
```sh
kubectl get pods
kubectl describe pod <pod-name>
# kubectl describe pod my-app-68bcb9db9f-gj4mb
```
👉 Image = nginx:1.27

### 3.6 Rollback
List History

```sh
helm history my-app
```

Result as below

```
REVISION        UPDATED                         STATUS          CHART                   APP VERSION     DESCRIPTION     
1               Thu Apr 16 23:47:31 2026        superseded      rollback-demo-0.1.0                     Install complete
2               Thu Apr 16 23:47:49 2026        deployed        rollback-demo-0.1.0                     Upgrade complete
```
👉 Pod is rollout to new version

### 3.7 Rollback to version 1:

```sh
helm rollback my-app 1
```

Output:
```
Rollback was a success! Happy Helming!
```

Verify:

```
kubectl describe pod <pod-name>
# kubectl describe pod my-app-7d4d46c969-vt78b
```

👉 Image: nginx:1.25

```sh
helm history my-app
```

Output:

```
REVISION        UPDATED                         STATUS          CHART                   APP VERSION     DESCRIPTION     
1               Thu Apr 16 23:47:31 2026        superseded      rollback-demo-0.1.0                     Install complete
2               Thu Apr 16 23:47:49 2026        superseded      rollback-demo-0.1.0                     Upgrade complete
3               Thu Apr 16 23:52:13 2026        deployed        rollback-demo-0.1.0                     Rollback to 1
```

👉 Helm didnot delete old version → just create new revision