# Устанавливаем cert-manger, выпускаем тестовый сертификат

```
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.7/deploy/manifests/00-crds.yaml

kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

helm repo add jetstack https://charts.jetstack.io
helm repo update

helm install \
 --name cert-manager \
 --namespace cert-manager \
 --version v0.7.2 \
 --set ingressShim.defaultIssuerName=letsencrypt \
 --set ingressShim.defaultIssuerKind=ClusterIssuer \
 jetstack/cert-manager
```

## Проверяем работу выпустив самоподписанный сертификат
```
kubectl apply -f test-resources.yaml
```

## Убеждаемся что сертификат создан

> сертификат
```
kubectl -n cert-manager-test describe certificate selfsigned-cert
kubectl -n cert-manager-test get certificate selfsigned-cert -o yaml
```

> Секрет с содержимым сертификата
```
kubectl -n cert-manager-test describe secret selfsigned-cert-tls
kubectl -n cert-manager-test get secret selfsigned-cert-tls -o yaml
```

## Каким образом сертификат был выпущен - issuer
```
kubectl -n cert-manager-test get issuer
```

# Удаляем тестовый ns
```
kubectl delete ns cert-manager-test
```

# Создаем выпускальщик сертификатов
```
kubectl apply -f clusterissuer-stage.yaml
```

# Добавляем в ingress информацию о TLS
> Правим в файле tls-ingress.yaml 's000' на свой номер студента
```
kubectl apply -f tls-ingress.yaml -n default
```

## посмотрели на сертификат
```
kubectl get certificate my-tls -o yaml
```

## посмотрели на секрет
```
kubectl get secret my-tls -o yaml
```

## Убедились что сертификат от issuer: CN=Fake LE Intermediate X1

```
curl https://my.s000.slurm.io -k -v
```

# Удаляем cert-manager

```
helm delete --purge cert-manager
```

# Вернулись назад
```
cd ..
```
