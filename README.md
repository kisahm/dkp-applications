# DKP Application

## Included applications:
* Chaos Mesh
* Rook Operator
* Rook Ceph Cluster
* Gatekeeper Policy Manager

## How to deploy this bundle

1. Define base variables
```
export KUBECONFIG=<path/to/kubeconfig>
export GITSERVER=github.com
export GITREPO=kisahm/dkp-applications
export BRANCH=main
```

2. Define workspace which should contains the catalog
```
export WORKSPACE_NAMESPACE=<workspace namespace>
```

4. Create catalog manifest
````
cat > catalog.yaml <<EOF
apiVersion: source.toolkit.fluxcd.io/v1beta1
kind: GitRepository
metadata:
  name: dkp-catalog-applications
  namespace: ${WORKSPACE_NAMESPACE}
  labels:
    kommander.d2iq.io/gitapps-gitrepository-type: catalog
    kommander.d2iq.io/gitrepository-type: catalog
spec:
  interval: 1m0s
  ref:
    branch: ${BRANCH}
  timeout: 20s
  url: https://${GITSERVER}/${GITREPO}
EOF
````

5. Apply catalog
```
kubectl apply -f catalog.yaml
```

6. Verify GitRepository
````
kubectl get GitRepository -n ${WORKSPACE_NAMESPACE}
````

7. Verify application catalog via cli
````
kubectl get apps -n ${WORKSPACE_NAMESPACE}
````

## ROOK
Ceph cluster operator.

### Requirements
All worker nodes needs minimum one (1) empty, additiona, non used block storage disk for Ceph OSDs.

### How to deploy
1. Deploy the "Rook Operator" to your workspace
2. Deploy the "Rook Ceph Cluster" to your workspace
3. Enjoy block, file and object storage

### Access the dashboard
To access the dashboard the password for the user "admin" is needed.
During the cluster creation Rook creates a secret with the password

#### How to get the password:
```` 
kubectl get secret -n ${WORKSPACE_NAMESPACE} rook-ceph-dashboard-password -o jsonpath='{.data.password}' | base64 -d && echo
````

## Chaos Mesh

### Dashboard
Currently the dashboard will not work because the Chaos Mesh dashboard is not able to run in a sub path.
There is an open issue: https://github.com/chaos-mesh/chaos-mesh/issues/2697
