# Деплой приложение

```
kubectl apply -f app
```

# Деплоим разные типы сервисов
## ClusterIP
```
kubectl apply -f clusterip.yaml
```

## Nodeport
```
kubectl apply -f nodeport.yaml
```

## ingress для nginx контроллер
```
kubectl apply -f nginx-ingress.yaml
kubectl get ing
```

```
curl my.s000.slurm.io
```

> Правим в файле host-ingress.yaml 's000' на свой номер студента
```
kubectl apply -f host-ingress.yaml
kubectl get ing
```

> работает
```
curl my.s000.slurm.io
```
> отдает 404
```
curl notmy.s000.slurm.io
```

# Переходим к установке cert-manager

```
cd cert-manager
cat instruction.md
```


# Удаляем все созданное

```
kubectl delete -f app
kubectl get ing
kubectl delete ing my-ingress-nginx

kubectl get svc
kubectl delete svc my-service
kubectl delete svc my-service-np
```
