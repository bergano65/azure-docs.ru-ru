---
title: Диаграммы хелт магазина
description: Узнайте, как хранить диаграммы Helm для приложений Kubernetes с помощью репозиториев в реестре контейнеров Azure
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 3f1a68258b758380a66b63e3c3137f1d460d288c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399377"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Нажмите и потяните диаграммы Helm в реестр контейнеров Azure

Для быстрого развертывания приложений и управления ими в Kubernetes можно использовать [диспетчер пакетов Helm с открытым исходным кодом][helm]. С Помощью Helm пакеты приложений определяются как [диаграммы,](https://helm.sh/docs/topics/charts/)которые собираются и хранятся в [репозитории диаграммы Helm.](https://helm.sh/docs/topics/chart_repository/)

В этой статье показано, как размещать репозитории диаграмм Helm в реестре контейнеров Azure, используя установку Helm 3 или Helm 2. Во многих сценариях вы будете создавать и загружать свои собственные диаграммы для приложений, которые вы разрабатываете. Для получения дополнительной информации о том, как [Chart Template Developer's Guide][develop-helm-charts]создать свои собственные диаграммы helm, см. Вы также можете хранить существующую диаграмму Helm из другого репо Helm.

> [!IMPORTANT]
> Поддержка диаграмм helm в реестре контейнеров Azure в настоящее время находится в предварительном просмотре. Предварительные просмотры доступны для вас при условии, что вы согласны с [дополнительными условиями использования.][terms-of-use] Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="helm-3-or-helm-2"></a>Шлем 3 или Хелм 2?

Для хранения, управления и установки диаграмм Helm используются клиент Helm и Helm CLI. Основные релизы клиента Helm включают Helm 3 и Helm 2. Helm 3 поддерживает новый формат диаграммы и больше не устанавливает компонент сервера Tiller. Для получения подробной информации о различиях версии, [см.](https://helm.sh/docs/faq/) Если вы ранее развернули диаграммы Helm 2, см. [Migrating Helm v2 до v3.](https://helm.sh/docs/topics/v2_v3_migration/)

Вы можете использовать диаграммы Helm 3 или Helm 2 для размещения диаграмм Helm в реестре контейнеров Azure, с рабочими процессами, специфичными для каждой версии:

* [Клиент Helm](#use-the-helm-3-client) 3 `helm chart` - использовать команды в Helm CLI для управления диаграммами в вашем реестре как [артефакты OCI](container-registry-image-formats.md#oci-artifacts)
* [Клиент Helm 2](#use-the-helm-2-client) - используйте команды [az acr Helm][az-acr-helm] в Azure CLI для добавления и управления реестром контейнеров в качестве репозитория диаграммы helm

### <a name="additional-information"></a>Дополнительные сведения

* Для большинства сценариев мы рекомендуем использовать `helm chart` рабочий процесс Helm 3 с нативными командами для управления диаграммами как артефактами OCI.
* Вы можете использовать устаревшие команды [az acr helm][az-acr-helm] Azure CLI и рабочий процесс с клиентом Helm 3 и диаграммами. Однако некоторые команды, `az acr helm list` такие как несовместимы с диаграммами Helm 3.
* По состоянию на 3, [команды az acr руля][az-acr-helm] поддерживаются в основном для совместимости с клиентом Helm 2 и форматом диаграммы. Дальнейшее развитие этих команд в настоящее время не планируется.

## <a name="use-the-helm-3-client"></a>Используйте клиента Helm 3

### <a name="prerequisites"></a>Предварительные требования

- **Реестр контейнеров Azure** в подписке Azure. При необходимости создайте реестр с помощью [портала Azure](container-registry-get-started-portal.md) или [Azure CLI.](container-registry-get-started-azure-cli.md)
- **Версия клиента helm 3.1.0** `helm version` или более поздно - Бегите для того чтобы найти вашу в настоящее время версию. Дополнительные сведения о том, как установить и обновить Helm, см. [здесь][helm-install].
- **Кластер Kubernetes,** где вы установите диаграмму Helm. При необходимости создайте [кластер службы Azure Kubernetes.][aks-quickstart] 
- **Версия Azure CLI 2.0.71 или позже** - Запуск, `az --version` чтобы найти версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

### <a name="high-level-workflow"></a>Рабочий процесс высокого уровня

С **helm 3** вы:

* Можно создать одно или несколько репозиторий Helm в реестре контейнеров Azure
* Хранить helm 3 диаграммы в реестре, как [артефакты OCI](container-registry-image-formats.md#oci-artifacts). В настоящее время, Helm 3 поддержка OCI является *экспериментальным*.
* Authenticate с вашим `helm registry login` реестром с помощью команды.
* Используйте `helm chart` команды в Helm CLI, чтобы толкать, тянуть и управлять графиками helm в реестре
* Используйте `helm install` для установки диаграмм в кластер Kubernetes из локального кэша репозитория.

Ниже приведены разделы для примеров.

### <a name="enable-oci-support"></a>Включить поддержку OCI

Установите следующую переменную среды, чтобы включить поддержку OCI в клиенте Helm 3. В настоящее время эта поддержка является экспериментальной. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Создание диаграммы образца

Создайте тестовую диаграмму, используя следующие команды:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

В качестве основного примера `templates` извинайте каталог в папку и сначала удалите содержимое:

```console
cd hello-world/templates
rm -rf *
```

В `templates` папке создайте `configmap.yaml` файл, вызванный следующим содержимым:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Подробнее о создании и запуске этого примера [можно](https://helm.sh/docs/chart_template_guide/getting_started/) узнать в документах Helm.

### <a name="save-chart-to-local-registry-cache"></a>Сохранить диаграмму в локальном кэше реестра

Изменение каталога `hello-world` в поддиректор. Затем запустите, `helm chart save` чтобы сохранить копию диаграммы локально, а также создать псевдоним с полностью квалифицированным названием реестра (все нижний регистр) и целевой репозиторий и тег. 

В следующем примере, имя реестра *mycontainerregistry*, целевой репо *привет-мир*, и целевой тег диаграммы *v1*, но заменить значения для вашей среды:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Запуск, `helm chart list` чтобы подтвердить, что вы сохранили диаграммы в локальном кэше реестра. Она выводит выходные данные следующего вида:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Authenticate с реестром

Запустите `helm registry login` команду в CLI Helm 3 для [проверки подлинности с помощью](container-registry-authentication.md) учетных данных, подходящих для вашего сценария.

Например, создайте в реестр принцип службы активных каталогов Azure Active directory [с разрешениями на вытягивание и нажатие](container-registry-auth-service-principal.md#create-a-service-principal) (роль AcrPush). Затем предоставите основные `helm registry login`учетные данные службы. Следующий пример поставляет пароль с помощью переменной среды:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Нажмите диаграмму в реестр контейнеров Azure

Запустите `helm chart push` команду в Helm 3 CLI, чтобы подтолкнуть диаграмму к полностью квалифицированному целевому репозиторию:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

После успешного нажатия выход аналогичен:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Вывод списка диаграмм в репозитории

Как и в том, что изображения хранятся в реестре контейнеров Azure, можно использовать команды [репозитория az acr,][az-acr-repository] чтобы показать репозитории, вмещая ваши диаграммы, а также теги и манифесты диаграмм. 

Например, выполнить [репозиторий az acr,][az-acr-repository-show] чтобы увидеть свойства репо, созданного на предыдущем этапе:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

Она выводит выходные данные следующего вида:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Выполнить команду [шоу-манифестов аз-акр,][az-acr-repository-show-manifests] чтобы увидеть детали диаграммы, хранящиеся в репозитории. Пример:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Выход, сокращенный в этом `configMediaType` примере, `application/vnd.cncf.helm.config.v1+json`показывает:

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>Потяните диаграмму к локальному кэшу

Чтобы установить диаграмму Helm в Kubernetes, диаграмма должна быть в локальном кэше. В этом примере `helm chart remove` сначала запустите, `mycontainerregistry.azurecr.io/helm/hello-world:v1`чтобы удалить существующую локальную диаграмму с именем:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Выполнить, `helm chart pull` чтобы загрузить диаграмму из реестра контейнеров Azure в локальном кэше:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Диаграмма экспортной хелбы

Чтобы продолжить работу с диаграммой, экспортировать `helm chart export`его в локальный каталог с помощью . Например, экспортировать диаграмму, `install` потянунную в каталог:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Чтобы просмотреть информацию для экспортируемой диаграммы в репо, запустите `helm show chart` команду в каталоге, где вы экспортировали диаграмму.

```console
cd install
helm show chart hello-world
```

Helm возвращает подробную информацию о последней версии диаграммы, как показано на следующем выходе образца:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Установка диаграммы руля

Выполнить `helm install` для установки диаграммы helm вы вытащили в локальный кэш и экспортируется. Укажите имя релиза, такое `--generate-name` как *myhelmtest,* или передайте параметр. Пример:

```console
helm install myhelmtest ./hello-world
```

Выход после успешной установки диаграммы аналогичен:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Чтобы проверить установку, `helm get manifest` запустите команду. Команда возвращает данные YAML `configmap.yaml` в файл шаблона.

Выполнить, `helm uninstall` чтобы удалить релиз диаграммы на кластере:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Удаление диаграммы Helm из репозитория

Чтобы удалить диаграмму из репозитория, используйте команду [удаления репозитория az acr.][az-acr-repository-delete] Выполнить следующую команду и подтвердить операцию, когда предложено:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Используйте клиента Helm 2

### <a name="prerequisites"></a>Предварительные требования

- **Реестр контейнеров Azure** в подписке Azure. При необходимости создайте реестр с помощью [портала Azure](container-registry-get-started-portal.md) или [Azure CLI.](container-registry-get-started-azure-cli.md)
- **Клиент Helm начиная с версии 2.11.0 (не релиз-кандидат)**. Выполните команду `helm version`, чтобы узнать свою версию. Вам также потребуется сервер Helm (Tiller), инициализированный в кластере Kubernetes. При необходимости создайте [кластер службы Azure Kubernetes.][aks-quickstart] Дополнительные сведения о том, как установить и обновить Helm, см. [здесь][helm-install-v2].
- **Azure CLI версии 2.0.46 или более поздней.** Выполните команду `az --version`, чтобы получить сведения о версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

### <a name="high-level-workflow"></a>Рабочий процесс высокого уровня

С **helm 2** вы:

* Навлаживайте реестр контейнеров Azure в *качестве единого* репозитория диаграммы Helm. Реестр контейнеров Azure управляет определением индекса при добавлении и удалении диаграмм в репозиторий.
* Authenticate с вашим реестром контейнеров Azure через Azure CLI, который затем автоматически обновляет ваш клиент Helm с реестром URI и учетными данными. Вам не нужно вручную указывать эту информацию реестра, поэтому учетные данные не будут раскрыты в истории команд.
* Используйте команды [az acr helm][az-acr-helm] в Azure CLI, чтобы добавить реестр контейнеров Azure в качестве репозитория диаграммы руля, а также нажать и управлять диаграммами. Эти команды Azure CLI обертывания команд клиента Helm 2.
* Добавьте репозиторий диаграммы в реестр контейнеров Azure в локальный индекс репо Helm, поддерживая поиск диаграммы.
* Используйте `helm install` для установки диаграмм в кластер Kubernetes из локального кэша репозитория.

Ниже приведены разделы для примеров.

### <a name="add-repository-to-helm-client"></a>Добавление репозитория клиенту Helm

Добавьте репозиторий диаграммы реестра контейнеров Azure Вашего клиента Helm, используя команду [репо az acr helm.][az-acr-helm-repo-add] Эта команда предоставляет маркер аутентификации для Реестра контейнеров Azure, используемый клиентом Helm. Токен проверки подлинности действителен в течение 3 часов. Как и `docker login`, эту команду можно выполнить в будущих сеансах интерфейса командной строки для аутентификации клиента Helm в репозитории диаграмм Helm Реестра контейнеров Azure:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Добавление диаграммы образца в репозиторий

Во-первых, создайте локальный каталог на *q/acr-helm,* затем загрузите существующую *конюшню/wordpress* диаграмму:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Введите `ls` список загруженной диаграммы и обратите внимание на версию Wordpress, включенную в имя файла. Команда `helm fetch stable/wordpress` не предоставила определенную версию, поэтому выбрана *последняя* версия. В следующем выводе примера диаграмма Wordpress представляет собой версию *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Нажмите диаграмму в репозиторий диаграммы Helm в реестре контейнеров Azure, используя команду [нажатия на руль az acr][az-acr-helm-push] в Azure CLI. Укажите название диаграммы Helm, загруженной на предыдущем этапе, например *wordpress-8.1.0.tgz:*

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Через несколько мгновений ClI Azure сообщает, что диаграмма сохранена, как показано на следующем выводе примера:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Вывод списка диаграмм в репозитории

Для использования диаграммы, отправленной на предыдущем шаге, необходимо обновить локальный индекс репозитория Helm. Вы можете выполнить повторную индексацию репозиториев в клиенте Helm или использовать Azure CLI, чтобы обновить индекс репозитория. Этот шаг необходимо выполнять при каждом добавлении диаграммы в репозиторий:

```azurecli
az acr helm repo add --name mycontainerregistry
```

С помощью диаграммы, хранимой в репозитории, и обновленного индекса, доступного в локальной среде, вы можете использовать команды обычного клиента Helm для поиска или установки. Чтобы просмотреть все диаграммы в репозитории, используйте `helm search` команду, предоставив свое собственное имя реестра контейнеров Azure:

```console
helm search mycontainerregistry
```

Диаграмма Wordpress, принудительно отправленная на предыдущем шаге, выведена в списке, как показано в следующем примере выходных данных:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

Вы также можете вывести список диаграмм с помощью Azure CLI, используя команду [az acr helm list][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Просмотр информации о диаграмме Helm

Для просмотра информации для определенной диаграммы репо можно использовать `helm inspect` команду.

```console
helm inspect mycontainerregistry/wordpress
```

Если номер версии не указан, используется *последняя* версия. Helm возвращает подробные сведения о диаграмме, как показано в следующем сокращенном примере выходных данных:

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

Вы также можете просмотреть данные диаграммы с помощью команды Azure CLI [az acr helm show][az-acr-helm-show]. Как и в предыдущем примере, по умолчанию возвращается *последняя* версия диаграммы. Вы можете `--version` припотеть, чтобы перечислить конкретную версию диаграммы, например *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Установка диаграммы Helm из репозитория

Диаграмма Хелма в репозитории устанавливается путем указания имени репозитория и имени диаграммы. Для установки диаграммы WordPress можно использовать клиент Helm:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Если отправить репозиторий диаграмм Helm в Реестр контейнеров Azure, а затем вернуться к работе в новом сеансе интерфейса командной строки, для локального клиента Helm необходимо будет предоставить обновленный маркер аутентификации. Чтобы получить новый маркер аутентификации, используйте команду [az acr helm repo add][az-acr-helm-repo-add].

Во время установки выполняются следующие действия:

- Клиент Helm выполняет поиск индекса локального репозитория.
- Соответствующая диаграмма скачивается из репозитория Реестра контейнеров Azure.
- Диаграмма развертывается с помощью Tiller в кластере Kubernetes.

По мере выполнения установки следуйте инструкциям в выводе команд, чтобы увидеть URL-адреса и учетные данные WorPress. Вы также можете `kubectl get pods` запустить команду, чтобы увидеть ресурсы Kubernetes, развернутые через диаграмму Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Удаление диаграммы Helm из репозитория

Чтобы удалить диаграмму из репозитория, используйте команду [az acr helm delete][az-acr-helm-delete]. Укажите название диаграммы, например *Wordpress,* и версию для удаления, например *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Если вы хотите удалить все версии указанной диаграммы, не указывайте параметр `--version`.

График продолжает возвращаться при `helm search`запуске. Как и в предыдущем примере, в клиенте Helm список доступных диаграмм в репозитории не обновляется автоматически. Чтобы обновить индекс репозитория клиента Helm, используйте команду [az acr helm repo add][az-acr-helm-repo-add]:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о создании и развертывании собственных диаграмм Helm см. в разделе документации, посвященном [разработке диаграмм Helm][develop-helm-charts].
* Подробнее об установке приложений с помощью службы «Хелм» в [Azure Kubernetes Service (AKS).](../aks/kubernetes-helm.md)
* Диаграммы Helm можно использовать в процессе создания контейнеров. Для получения дополнительной информации [см.][acr-tasks]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
