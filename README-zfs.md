# Create pool
```sh
zpool create talos-pool /dev/sda
zpool list
zpool status
```

# Get csi-nodes
```sh
kubectl get csinodes
```

# Get zv
```sh
kubectl get zv -A
```

# Get storageClass
```sh
kubectl get storageclass
```

# Get pvc
```sh
kubectl get pvc -A
```

# Manifests
В идеале, надо перенести в репозиторий:
```yaml

---
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: openebs-zfspv
parameters:
  fstype: "zfs"
  poolname: "talos-pool"
provisioner: zfs.csi.openebs.io
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: "talos-pool-test"
spec:
  storageClassName: "openebs-zfspv"
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

# Links
* https://github.com/openebs/zfs-localpv/issues/545
* https://openebs.io/docs/user-guides/local-storage-user-guide/local-pv-zfs/zfs-installation
