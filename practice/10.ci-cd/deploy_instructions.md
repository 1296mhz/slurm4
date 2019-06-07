## Все работы проводим под рутом на master-1

### Если ключ SSH еще не добавлен в Gitlab.

Добавляем свой публичный SSH ключ в Gitlab.
Для этого заходим в gitlab.slurm.io
В правом верхнем углу нажимаем на значок своей учетной записи.
В выпадающем меню нажимаем Settings.
Дальше в левом меню выбираем раздел SSH Keys
И в поле key вставляем свой ПУБЛИЧНЫЙ SSH ключ.

### Форк проекта

Открываем в браузере репозиторий slurm-api. Он находится по адресу
```bash
https://gitlab.slurm.io/slurm/slurm-api
```

Справа на одной линии с названием проекта видим кнопку fork. Нажимаем ее.
Выбираем в качестве нэймспэйса в следующем окне свой логин.
И дожидаемся окончания процесса форка.
Последним шагом клонируем получившийся форк к себе на админбокс или master-1.
> С репозиторием можно работать даже с локального компьютера. Это не принципиально.
> Главное команды Kubernetes выполнять с master-1.
```bash
git clone git@gitlab.slurm.io:student<номер своего логина>/slurm-api.git
```
и переходим в директорию проекта
```bash
cd slurm-api
```

### Подготовка CI

Копируем файл из репозитория c практиками в директорию slurm-api

```bash
cp ~/slurm/practice/10.ci-cd/step_5_deploy/.gitlab-ci.yml .
```

Открываем скопированный файл и в нем правим
```yaml
  K8S_API_URL: https://172.21.<номер вашего логина>.2:6443
```
> Обратите внимание, что если ваш номер логина 001,
> то IP адрес это 172.21.1.2, а не 172.21.001.2

НИЧЕГО ПОКА НЕ КОММИТИМ И НЕ ПУШИМ!

Далее подготавливаем namespace и нужные RBAC объекты.
Для этого запускаем скрипт setup.sh
```bash
~/slurm/practice/11.ci-cd/step_5_deploy/setup.sh student<номер своего логина> production
```
В конце своего выполнения скрипт выдаст нам токен.
Его нужно скопировать.

После этого открываем в браузере свой форк приложения.
```bash
https://gitlab.slurm.io/student<номер своего логина>/slurm-api
```
В левом меню находим Settings, далее CI/CD и далее Variables и нажимаем Expand

В левое поле вводим имя переменной
```bash
K8S_CI_TOKEN
```
В правое поле вводим скопированный токен из вывода команды setup.sh
Protected не включаем!
Masked выключаем!

И нажимаем Save variables

Далее в том же левом меню в Settings > Repository находим Deploy tokens и нажимаем Expand.
В поле Name вводим
```bash
k8s-pull-token
```
И ставим галочку рядом с read_registry.

Все остальные поля оставляем пустыми.

Нажимаем Create deploy token.

НЕ ЗАКРЫВАЕМ ОКНО БРАУЗЕРА!

Возвращаемся в консоль на первом мастере

Создаем image pull secret для того чтобы кубернетис мог пулить имаджи из гитлаба
```bash
kubectl create secret docker-registry web-slurm-api-gitlab-registry --docker-server registry.slurm.io --docker-email 'student@slurm.io' --docker-username '<первая строчка из окна создания токена в gitlab>' --docker-password '<вторая строчка из окна создания токена в gitlab>' --namespace student<номер своего логина>-production
```
Соответсвенно подставляя на место <> нужные параметры.

### Создание секрета для приложения

Выполняем команду
```bash
kubectl create secret generic web-slurm-api --from-literal db-user=postgres --from-literal db-password=postgres --namespace student<номер своего логина>-production
```

### Правка чарта и деплой приложения

Открываем файл из своего репозитория slurm-api
```bash
.helm/values.yaml
```
Находим там строчки
```yaml
env:
  DB_HOST: student<номер своего логина>-slurm-api-postgresql
  ASSETS_HOST: "http://demo.s<номер своего логина>.slurm.io"
  WEBDAV_HOST: student<номер своего логина>-slurm-api-webdav

ingress:
  host: demo.s<номер своего логина>.slurm.io

webdav:
  deploy: true
  ingress:
    host: demo.s<номер своего логина>.slurm.io
```
и меняем на свой номер логина
> В итоге нужно внести изменения в пяти местах.

После этого делаем коммит и пуш
```bash
git add .
git commit -m "add CI/CD"
git push
```

Смотрим в интейрфесе Gitlab на пайплайн
После успешного завершения открываем в браузере

```bash
demo.s<номер своего логина>.slurm.io/api
```
