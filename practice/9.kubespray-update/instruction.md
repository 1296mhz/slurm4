# все просто

>исправляем значение переменной kube_version: в файле 
>group_vars/k8s-cluster/k8s-cluster.yml

```
kube_version: v1.14.1
```

>Исправляем путь к инвнтарю в скрипте _upgrade_cluster.sh (000 меняем на номер студента)
>inventory/s000/inventory.ini

>и запускем скрипт на обновление кластера

```
sh _upgrade_cluster.sh <свой логин>
```
