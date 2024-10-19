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

# ZFS import
Например, ты перезагрузился, видишь zfs диски/разделы, но в `zpool list` они не отображаются.
Требуется произвести import:
```sh
zpool import talos-pool -f
```

# Монтирование legacy datastore
```sh
mkdir /mnt/pvc-test
mount.zfs -o ro talos-pool/pvc-990804c9-977e-4d4f-8af4-bea296b20938 /mnt/pvc-test
ls /mnt/pvc-test/
```

Бывает, что надо добавить опцию -f, если массив использовался на другой системе.

# Получение информации
```sh
zfs get all talos-pool/pvc-990804c9-977e-4d4f-8af4-bea296b20938
```

# Создание и удаление пула
```sh
zfs create  talos-pool/home
zfs destroy talos-pool/home
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
---
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  containers:
    - name: alpine
      image: alpine
      command: [ "sleep" ]
      args: [ "infinity" ]
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: talos-pool-test
```

# Links
* https://github.com/openebs/zfs-localpv/issues/545
* https://openebs.io/docs/user-guides/local-storage-user-guide/local-pv-zfs/zfs-installation
* https://github.com/gruberdev/homelab/blob/main/config/talos/controller-patch.yaml
