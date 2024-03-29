## Все работы проводим под рутом на master-1
> С sbox Заходим на master-1

```bash
ssh master-1.s000
sudo -s
```

> Клонируем кубеспрей в каталог /srv
```bash
cd /srv
git clone https://github.com/centosadmin/kubespray
```

> Устанавливаем ansible и зависимости
```bash
cd kubespray
yum install -y sshpass
pip install -r requirements.txt
```

> Исправляем инвентарь
```bash
cd inventory
```
> переименовываем каталог с инвентарем
```bash
mv s000 s<номер своего логина> 
cd s<номер своего логина> 

vi inventory.ini
```
> Изменяем s000 на s<номер своего логина> 
> Во всех строчках!!!!
> Изменяем третий октет ip адреса в переменных ansible_host и в ip на номер своего логина

```bash
cd group_vars/k8s-cluster

vi k8s-cluster.yml
```
> Исправляем 
> cluster_name: s000.local на s<номер своего логина>.local

```bash
vi k8s-net-flannel.yml
```
> Исправлем 
```
flannel_interface_regexp: '172\\.21\\.0\\.\\d{1,3}'
на
flannel_interface_regexp: '172\\.21\\.<номер своего логина>\\.\\d{1,3}'
```
> Возвращаемся в корневой каталог кубспрея

```bash
cd ../../../..
vi _deploy_cluster.sh
```
> Исправлем путь к инвентарю в строке запуска ansible-playbook
> inventory/s000/inventory.ini на inventory/<номер своего логина>/inventory.ini

> Запускаем сценарий
> например  sh  _deploy_cluster.sh student001

```bash
sh  _deploy_cluster.sh <свой логин>
```
> Следим за выводом на экран. ждем отчета об успешном завершении
> Если при завершении есть красные надписи fail= смотрим вывод, ищем ошибки в инвентаре.

> Если все прошло хорошо, то команда 
```bash
kubectl get nodes 
```
> покажет 6 узлов со статусом Ready (3 master, 2 node и 1 ingress)
