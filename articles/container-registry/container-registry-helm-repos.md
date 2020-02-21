---
title: Хранение Helm диаграмм
description: Узнайте, как хранить диаграммы Helm для приложений Kubernetes с помощью репозиториев в реестре контейнеров Azure.
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 26588bb4dc3cf50656103b50d5d0559908a1ccb7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524637"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Отправка и извлечение Helm диаграмм в реестр контейнеров Azure

Для быстрого управления и развертывания приложений для Kubernetes можно использовать [Диспетчер пакетов Helm с открытым исходным кодом][helm]. При использовании Helm пакеты приложений определяются как [диаграммы](https://helm.sh/docs/topics/charts/), которые собираются и сохраняются в [репозитории диаграммы Helm](https://helm.sh/docs/topics/chart_repository/).

В этой статье показано, как разместить диаграммы Helm в репозиториях в реестре контейнеров Azure с помощью установки Helm 3 или Helm 2. В этом примере вы сохраняете существующую диаграмму Helm из общедоступного *Helmного* репозитория. Во многих случаях для разрабатываемых приложений можно создавать и передавать собственные диаграммы. Дополнительные сведения о создании собственных диаграмм Helm см. в разделе [Руководство разработчика шаблона диаграммы][develop-helm-charts].

> [!IMPORTANT]
> Поддержка диаграмм Helm в реестре контейнеров Azure сейчас доступна в предварительной версии. Предварительные версии предоставляются при условии, что вы соглашаетесь с дополнительными [условиями использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="helm-3-or-helm-2"></a>Helm 3 или Helm 2?

Для хранения, управления и установки диаграмм Helm используется клиент Helm и интерфейс командной строки Helm. Основные выпуски клиента Helm включают Helm 3 и Helm 2. Helm 3 поддерживает новый формат диаграммы и больше не устанавливает серверный компонент кассового сервера. Дополнительные сведения о различиях версий см. в разделе [часто задаваемые вопросы о версии](https://helm.sh/docs/faq/). Если вы ранее развернули Helm 2 диаграммы, см. статью [Миграция Helm v2 на v3](https://helm.sh/docs/topics/v2_v3_migration/).

Для размещения диаграмм Helm в реестре контейнеров Azure можно использовать Helm 3 или Helm 2 с рабочими процессами, специфичными для каждой версии:

* [Клиент Helm 3](#use-the-helm-3-client) . Используйте `helm chart` команды для управления диаграммами в реестре как [артефактами OCI](container-registry-image-formats.md#oci-artifacts)
* [Helm 2 Client](#use-the-helm-2-client) — используйте команды [AZ запись контроля][az-acr-helm] доступа, которые можно использовать в Azure CLI для добавления реестра контейнеров и управления им в качестве репозитория Helm Chart.

### <a name="additional-information"></a>Дополнительные сведения

* Для управления диаграммами как артефактами OCI рекомендуется использовать рабочий процесс Helm 3 с собственными `helm chart` командами.
* Вы можете использовать устаревшую команду [AZ helm][az-acr-helm] Azure CLI команды и рабочий процесс с клиентом Helm 3 и диаграммами. Однако некоторые команды, такие как `az acr helm list`, несовместимы с диаграммами Helm 3.
* Начиная с Helm 3 команды [AZ контроля доступа Helm][az-acr-helm] поддерживаются в основном для обеспечения совместимости с клиентом Helm 2 и форматом диаграммы. Дальнейшая разработка этих команд сейчас не запланирована.

## <a name="use-the-helm-3-client"></a>Использование клиента Helm 3

### <a name="prerequisites"></a>предварительные требования

- **Реестр контейнеров Azure** в подписке Azure. При необходимости создайте реестр с помощью [портал Azure](container-registry-get-started-portal.md) или [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm Client Version 3.0.0 или более поздней** версии. Запустите `helm version`, чтобы найти текущую версию. Дополнительные сведения об установке и обновлении Helm см. в разделе Installing [Helm][helm-install].
- **Кластер Kubernetes** , в котором будет установлена диаграмма Helm. При необходимости создайте [кластер службы Azure Kubernetes][aks-quickstart]. 
- **Azure CLI Version 2.0.71 или более поздней версии** — запустите `az --version`, чтобы найти версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

### <a name="high-level-workflow"></a>Рабочий процесс высокого уровня

С **Helm 3** вы:

* Может создать один или несколько репозиториев Helm в реестре контейнеров Azure
* Храните Helm 3 диаграммы в реестре как [артефакты OCI](container-registry-image-formats.md#oci-artifacts). В настоящее время Helm 3 поддержка OCI считается *экспериментальной*.
* Используйте команды `helm chart` непосредственно из интерфейса командной строки Helm, чтобы отправлять, запрашивать и управлять диаграммами Helm в реестре.
* Выполните аутентификацию в реестре с помощью Azure CLI, который автоматически обновляет клиент Helm, используя URI и учетные данные реестра. Эти данные реестра не нужно указывать вручную, поэтому учетные данные не отображаются в журнале команд.
* Используйте `helm install`, чтобы установить диаграммы в кластер Kubernetes из локального кэша репозитория.

Примеры см. в следующих разделах.

### <a name="enable-oci-support"></a>Включить поддержку OCI

Задайте следующую переменную среды, чтобы включить поддержку OCI в клиенте Helm 3. В настоящее время эта поддержка экспериментальна. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="pull-an-existing-helm-package"></a>Извлечение существующего пакета Helm

Если вы еще не добавили `stable` репозиторий диаграммы Helm, выполните команду `helm repo add`:

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

Извлечение пакета диаграммы из репозитория `stable` локально. Например, создайте локальный каталог, например *~/Акр-Хелм*, а затем скачайте существующий пакет диаграммы *stable/WordPress* . (Этот пример и другие команды в этой статье форматируются для оболочки bash.)

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm pull stable/wordpress --untar
```

В команде `helm pull stable/wordpress` не указана конкретная версия, поэтому *Последняя* версия была извлечена и распакована в подкаталоге `wordpress`.

### <a name="save-chart-to-local-registry-cache"></a>Сохранить диаграмму в локальном кэше реестра

Перейдите в каталог `wordpress`, содержащий файлы диаграммы Helm. Затем запустите `helm chart save`, чтобы сохранить копию диаграммы локально, а также создайте псевдоним с полным именем реестра и целевым репозиторием и тегом. 

В следующем примере имя реестра — *миконтаинеррегистри*, целевой репозиторий — *WordPress*, а целевой тег диаграммы — *последним*, но замените значения для вашей среды.

```console
cd wordpress
helm chart save . wordpress:latest
helm chart save . mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Запустите `helm chart list`, чтобы убедиться, что диаграммы сохранены в локальном кэше реестра. Она выводит выходные данные следующего вида:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
wordpress:latest                                         wordpress       8.1.0   5899db0 29.1 KiB        1 day 
mycontainerregistry.azurecr.io/helm/wordpress:latest     wordpress       8.1.0   5899db0 29.1 KiB        1 day 
```

### <a name="push-chart-to-azure-container-registry"></a>Принудительная отправка диаграммы в реестр контейнеров Azure

Выполните команду `helm chart push` в интерфейсе командной строки Helm 3, чтобы отправить диаграмму Helm в репозиторий в реестре контейнеров Azure. Если он не существует, создается репозиторий.

Сначала используйте команду Azure CLI [AZ запись контроля][az-acr-login] доступа для проверки подлинности в реестре:

```azurecli
az acr login --name mycontainerregistry
```

Отправьте диаграмму в полный целевой репозиторий:

```console
helm chart push mycontainerregistry.azurecr.io/helm/wordpress:latest
```

После успешной принудительной отправки выходные данные выглядят примерно так:

```console
The push refers to repository [mycontainerregistry.azurecr.io/helm/wordpress]
ref:     mycontainerregistry.azurecr.io/helm/wordpress:latest
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    29.1 KiB
name:    wordpress
version: 8.1.0
```

### <a name="list-charts-in-the-repository"></a>Вывод списка диаграмм в репозитории

Как и в случае с изображениями, хранящимися в реестре контейнеров Azure, можно использовать команды [AZ контроля][az-acr-repository] учетных записей, чтобы отобразить репозитории, в которых размещены диаграммы, а также теги и манифесты диаграммы. 

Например, выполните команду [AZ контроля доступа к репозиторию][az-acr-repository-show] , чтобы просмотреть свойства репозитория, созданного на предыдущем шаге.

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/wordpress
```

Она выводит выходные данные следующего вида:

```console
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-01-29T16:54:30.1514833Z",
  "imageName": "helm/wordpress",
  "lastUpdateTime": "2020-01-29T16:54:30.4992247Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Чтобы просмотреть сведения о диаграмме, хранящейся в репозитории, выполните команду AZ запись в [репозитории: Показать-манифесты][az-acr-repository-show-manifests] . Пример:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/wordpress --detail
```

Выходные данные, сокращенные в этом примере, показывают `configMediaType` `application/vnd.cncf.helm.config.v1+json`:

```console
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-01-29T16:54:30.2382436Z",
    "digest": "sha256:xxxxxxxx51bc0807bfa97cb647e493ac381b96c1f18749b7388c24bbxxxxxxxxx",
    "imageSize": 29995,
    "lastUpdateTime": "2020-01-29T16:54:30.3492436Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "latest"
    ]
  }
]
```

### <a name="pull-chart-to-local-cache"></a>Извлечь диаграмму в локальный кэш

Чтобы установить Helm диаграмму в Kubernetes, эта диаграмма должна находиться в локальном кэше. В этом примере сначала запустите `helm chart remove`, чтобы удалить существующую локальную диаграмму с именем `mycontainerregistry.azurecr.io/helm/wordpress:latest`:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Запустите `helm chart pull`, чтобы скачать диаграмму из реестра контейнеров Azure в локальный кэш:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/wordpress:latest
```

### <a name="export-helm-chart"></a>Экспортировать Helm диаграмму

Для дальнейшей работы с диаграммой экспортируйте ее в локальный каталог с помощью `helm chart export`. Например, экспортируйте диаграмму, которая была извлечена в каталог `install`:

```console
helm chart export mycontainerregistry.azurecr.io/helm/wordpress:latest --destination ./install
```

Чтобы просмотреть сведения о экспортированной диаграмме в репозитории, выполните команду `helm inspect chart` в каталоге, в который была экспортирована диаграмма.

```console
cd install
helm inspect chart wordpress
```

Если номер версии не указан, используется *последняя* версия. Helm возвращает подробные сведения о диаграмме, как показано в следующем сжатом выводе:

```
apiVersion: v1
appVersion: 5.3.2
dependencies:
- condition: mariadb.enabled
  name: mariadb
  repository: https://kubernetes-charts.storage.googleapis.com/
  tags:
  - wordpress-database
  version: 7.x.x
description: Web publishing platform for building blogs and websites.
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
```

### <a name="install-helm-chart"></a>Установить Helm диаграмму

Запустите `helm install`, чтобы установить диаграмму Helm, которая была извлечена в локальный кэш и экспортирована. Укажите имя выпуска или передайте параметр `--generate-name`. Пример:

```console
helm install wordpress --generate-name
```

По мере продолжения установки следуйте инструкциям в выходных данных команды, чтобы просмотреть URL-адреса и учетные данные Ворпресс. Можно также выполнить команду `kubectl get pods`, чтобы просмотреть ресурсы Kubernetes, развернутые с помощью диаграммы Helm:

```console
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Удаление диаграммы Helm из репозитория

Чтобы удалить диаграмму из репозитория, используйте команду [AZ запись репозитория Delete][az-acr-repository-delete] . Выполните следующую команду и подтвердите операцию при появлении запроса:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/wordpress:latest
```

## <a name="use-the-helm-2-client"></a>Использование клиента Helm 2

### <a name="prerequisites"></a>предварительные требования

- **Реестр контейнеров Azure** в подписке Azure. При необходимости создайте реестр с помощью [портал Azure](container-registry-get-started-portal.md) или [Azure CLI](container-registry-get-started-azure-cli.md).
- **Клиент Helm начиная с версии 2.11.0 (не релиз-кандидат)** . Выполните команду `helm version`, чтобы узнать свою версию. Вам также потребуется сервер Helm (Tiller), инициализированный в кластере Kubernetes. При необходимости создайте [кластер службы Azure Kubernetes][aks-quickstart]. Дополнительные сведения об установке и обновлении Helm см. в разделе Installing [Helm][helm-install-v2].
- **Azure CLI версии 2.0.46 или более поздней.** Выполните команду `az --version`, чтобы получить сведения о версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

### <a name="high-level-workflow"></a>Рабочий процесс высокого уровня

С **Helm 2** вы:

* Настройте реестр контейнеров Azure как *единое* хранилище диаграмм Helm. Реестр контейнеров Azure управляет определением индекса при добавлении и удалении диаграмм в репозитории.
* Используйте команды [AZ Helm][az-acr-helm] в Azure CLI, чтобы добавить реестр контейнеров Azure в качестве репозитория диаграммы Helm, а также для отправки диаграмм и управления ими. Эти Azure CLI команды создают оболочку для команд клиента Helm 2.
* Добавьте репозиторий диаграммы в реестр контейнеров Azure в локальный индекс репозитория Helm, поддерживающий поиск в диаграмме.
* Выполните аутентификацию в реестре контейнеров Azure с помощью Azure CLI, который автоматически обновляет клиент Helm, используя URI и учетные данные реестра. Эти данные реестра не нужно указывать вручную, поэтому учетные данные не отображаются в журнале команд.
* Используйте `helm install`, чтобы установить диаграммы в кластер Kubernetes из локального кэша репозитория.

Примеры см. в следующих разделах.

### <a name="add-repository-to-helm-client"></a>Добавление репозитория в клиент Helm

Добавьте в клиент Helm репозиторий диаграммы Helm реестра контейнеров Azure с помощью команды [AZ контроля доступа Helm репозиторий][az-acr-helm-repo-add] . Эта команда предоставляет маркер аутентификации для Реестра контейнеров Azure, используемый клиентом Helm. Маркер проверки подлинности действителен в течение 3 часов. Как и `docker login`, эту команду можно выполнить в будущих сеансах интерфейса командной строки для аутентификации клиента Helm в репозитории диаграмм Helm Реестра контейнеров Azure:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-chart-to-the-repository"></a>Добавление диаграммы в репозиторий

Сначала создайте локальный каталог в папке *~/Акр-Хелм*, а затем скачайте имеющуюся *стабильную или WordPress* диаграмму:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Введите `ls`, чтобы получить список загруженной диаграммы, и обратите внимание на версию WordPress, которая включена в имя файла. Команда `helm fetch stable/wordpress` не предоставила определенную версию, поэтому выбрана *последняя* версия. В следующем примере выходных данных WordPress диаграмма имеет версию *8.1.0*:

```
wordpress-8.1.0.tgz
```

Отправьте диаграмму в репозиторий диаграммы Helm в реестре контейнеров Azure, выполнив команду [AZ контроля доступа Helm Push][az-acr-helm-push] в Azure CLI. Укажите имя диаграммы Helm, скачанной на предыдущем шаге, например *ВордПресс-8.1.0. tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Через несколько секунд Azure CLI сообщает о сохранении диаграммы, как показано в следующем примере выходных данных:

```
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Вывод списка диаграмм в репозитории

Для использования диаграммы, отправленной на предыдущем шаге, необходимо обновить локальный индекс репозитория Helm. Вы можете выполнить повторную индексацию репозиториев в клиенте Helm или использовать Azure CLI, чтобы обновить индекс репозитория. Этот шаг необходимо выполнять при каждом добавлении диаграммы в репозиторий:

```azurecli
az acr helm repo add --name mycontainerregistry
```

С помощью диаграммы, хранимой в репозитории, и обновленного индекса, доступного в локальной среде, вы можете использовать команды обычного клиента Helm для поиска или установки. Чтобы просмотреть все диаграммы в репозитории, используйте команду `helm search`, указав собственное имя реестра контейнеров Azure:

```console
helm search mycontainerregistry
```

Диаграмма Wordpress, принудительно отправленная на предыдущем шаге, выведена в списке, как показано в следующем примере выходных данных:

```
NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  8.1.0           5.3.2       Web publishing platform for building blogs and websites.
```

Вы также можете вывести список диаграмм с Azure CLI с помощью команды [AZ контроля доступа Helm List][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Просмотр информации о диаграмме Helm

Чтобы просмотреть сведения о конкретной диаграмме в репозитории, можно использовать команду `helm inspect`.

```console
helm inspect mycontainerregistry/wordpress
```

Если номер версии не указан, используется *последняя* версия. Helm возвращает подробные сведения о диаграмме, как показано в следующем сокращенном примере выходных данных:

```
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

Также можно отобразить сведения о диаграмме с помощью команды Azure CLI [AZ запись контроля доступа Helm отобразить][az-acr-helm-show] . Как и в предыдущем примере, по умолчанию возвращается *последняя* версия диаграммы. Можно добавить `--version`, чтобы получить список определенной версии диаграммы, например *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Установка диаграммы Helm из репозитория

Диаграмма Helm в репозитории устанавливается путем указания имени репозитория и имени диаграммы. Для установки диаграммы WordPress можно использовать клиент Helm:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Если отправить репозиторий диаграмм Helm в Реестр контейнеров Azure, а затем вернуться к работе в новом сеансе интерфейса командной строки, для локального клиента Helm необходимо будет предоставить обновленный маркер аутентификации. Чтобы получить новый маркер проверки подлинности, используйте команду [AZ контроля доступа Helm Repository Add][az-acr-helm-repo-add] .

Во время установки выполняются следующие действия:

- Клиент Helm выполняет поиск индекса локального репозитория.
- Соответствующая диаграмма скачивается из репозитория Реестра контейнеров Azure.
- Диаграмма развертывается с помощью Tiller в кластере Kubernetes.

По мере продолжения установки следуйте инструкциям в выходных данных команды, чтобы просмотреть URL-адреса и учетные данные Ворпресс. Можно также выполнить команду `kubectl get pods`, чтобы просмотреть ресурсы Kubernetes, развернутые с помощью диаграммы Helm:

```
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Удаление диаграммы Helm из репозитория

Чтобы удалить диаграмму из репозитория, выполните команду [AZ запись контроля доступа Helm Delete][az-acr-helm-delete] . Укажите имя диаграммы, например *WordPress*, и версию для удаления, например *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Если вы хотите удалить все версии указанной диаграммы, не указывайте параметр `--version`.

Диаграмма будет возвращаться при запуске `helm search`. Как и в предыдущем примере, в клиенте Helm список доступных диаграмм в репозитории не обновляется автоматически. Чтобы обновить индекс репозитория клиента Helm, выполните команду [AZ контроля доступа Helm Repository Add][az-acr-helm-repo-add] .

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этой статьи применялась имеющаяся диаграмма Helm из общедоступного репозитория *stable*. Дополнительные сведения о создании и развертывании Helm диаграмм см. в разделе [Разработка диаграмм Helm][develop-helm-charts].

Диаграммы Helm можно использовать в процессе создания контейнеров. Дополнительные сведения см. в статье [Использование задач реестра контейнеров Azure][acr-tasks].

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
