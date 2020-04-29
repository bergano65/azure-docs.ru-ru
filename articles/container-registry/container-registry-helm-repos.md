---
title: Хранение Helm диаграмм
description: Узнайте, как хранить диаграммы Helm для приложений Kubernetes с помощью репозиториев в реестре контейнеров Azure.
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 3f1a68258b758380a66b63e3c3137f1d460d288c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399377"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Отправка и извлечение Helm диаграмм в реестр контейнеров Azure

Для быстрого развертывания приложений и управления ими в Kubernetes можно использовать [диспетчер пакетов Helm с открытым исходным кодом][helm]. При использовании Helm пакеты приложений определяются как [диаграммы](https://helm.sh/docs/topics/charts/), которые собираются и сохраняются в [репозитории диаграммы Helm](https://helm.sh/docs/topics/chart_repository/).

В этой статье показано, как разместить репозитории диаграмм Helm в реестре контейнеров Azure с помощью установки Helm 3 или Helm 2. Во многих случаях для разрабатываемых приложений можно создавать и передавать собственные диаграммы. Дополнительные сведения о создании собственных диаграмм Helm см. в разделе [Руководство разработчика шаблона диаграммы][develop-helm-charts]. Можно также сохранить существующую диаграмму Helm из другого репозитория Helm.

> [!IMPORTANT]
> Поддержка диаграмм Helm в реестре контейнеров Azure сейчас доступна в предварительной версии. Предварительные версии предоставляются при условии, что вы соглашаетесь с дополнительными [условиями использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="helm-3-or-helm-2"></a>Helm 3 или Helm 2?

Для хранения, управления и установки диаграмм Helm используется клиент Helm и интерфейс командной строки Helm. Основные выпуски клиента Helm включают Helm 3 и Helm 2. Helm 3 поддерживает новый формат диаграммы и больше не устанавливает серверный компонент кассового сервера. Дополнительные сведения о различиях версий см. в разделе [часто задаваемые вопросы о версии](https://helm.sh/docs/faq/). Если вы ранее развернули Helm 2 диаграммы, см. статью [Миграция Helm v2 на v3](https://helm.sh/docs/topics/v2_v3_migration/).

Для размещения диаграмм Helm в реестре контейнеров Azure можно использовать Helm 3 или Helm 2 с рабочими процессами, специфичными для каждой версии:

* [Helm 3](#use-the-helm-3-client) . Используйте `helm chart` команды в интерфейсе командной строки Helm для управления диаграммами в реестре как [артефактами OCI](container-registry-image-formats.md#oci-artifacts)
* [Helm 2 Client](#use-the-helm-2-client) — используйте команды [AZ запись контроля][az-acr-helm] доступа, которые можно использовать в Azure CLI для добавления реестра контейнеров и управления им в качестве репозитория Helm Chart.

### <a name="additional-information"></a>Дополнительные сведения

* Для большинства сценариев рекомендуется использовать рабочий процесс Helm 3 с собственными `helm chart` командами для управления диаграммами как артефактами OCI.
* Вы можете использовать устаревшую команду [AZ helm][az-acr-helm] Azure CLI команды и рабочий процесс с клиентом Helm 3 и диаграммами. Однако некоторые команды, такие как `az acr helm list` , не совместимы с диаграммами Helm 3.
* Начиная с Helm 3 команды [AZ контроля доступа Helm][az-acr-helm] поддерживаются в основном для обеспечения совместимости с клиентом Helm 2 и форматом диаграммы. Дальнейшая разработка этих команд сейчас не запланирована.

## <a name="use-the-helm-3-client"></a>Использование клиента Helm 3

### <a name="prerequisites"></a>Предварительные условия

- **Реестр контейнеров Azure** в подписке Azure. При необходимости создайте реестр с помощью [портал Azure](container-registry-get-started-portal.md) или [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm Client версии 3.1.0 или более поздней** — выполните команду `helm version` , чтобы найти текущую версию. Дополнительные сведения о том, как установить и обновить Helm, см. [здесь][helm-install].
- **Кластер Kubernetes** , в котором будет установлена диаграмма Helm. При необходимости создайте [кластер службы Azure Kubernetes][aks-quickstart]. 
- **Azure CLI версии 2.0.71 или более поздней** — выполните команду `az --version` , чтобы найти версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

### <a name="high-level-workflow"></a>Рабочий процесс высокого уровня

С **Helm 3** вы:

* Может создать один или несколько репозиториев Helm в реестре контейнеров Azure
* Храните Helm 3 диаграммы в реестре как [артефакты OCI](container-registry-image-formats.md#oci-artifacts). В настоящее время Helm 3 поддержка OCI является *экспериментальной*.
* Проверьте подлинность в реестре с помощью `helm registry login` команды.
* Использование `helm chart` команд в интерфейсе командной строки Helm для отправки, извлечения и управления диаграммами Helm в реестре
* Используйте `helm install` для установки диаграмм в кластер Kubernetes из локального кэша репозитория.

Примеры см. в следующих разделах.

### <a name="enable-oci-support"></a>Включить поддержку OCI

Задайте следующую переменную среды, чтобы включить поддержку OCI в клиенте Helm 3. В настоящее время эта поддержка экспериментальна. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Создание образца диаграммы

Создайте тестовую диаграмму с помощью следующих команд:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

В качестве базового примера измените каталог на `templates` папку и сначала удалите содержимое:

```console
cd hello-world/templates
rm -rf *
```

В `templates` папке создайте файл с именем `configmap.yaml` со следующим содержимым:

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

Дополнительные сведения о создании и выполнении этого примера см. в разделе [Начало работы](https://helm.sh/docs/chart_template_guide/getting_started/) в документах Helm.

### <a name="save-chart-to-local-registry-cache"></a>Сохранить диаграмму в локальном кэше реестра

Измените каталог на `hello-world` подкаталог. Затем выполните команду `helm chart save` , чтобы сохранить копию диаграммы локально и создать псевдоним с полным именем реестра (все строчные), а также с целевым репозиторием и тегом. 

В следующем примере имя реестра — *миконтаинеррегистри*, целевой репозиторий — *Hello-World*, а целевой тег диаграммы — *v1*, но значения для вашей среды заменяются:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Выполните `helm chart list` команду, чтобы убедиться, что диаграммы сохранены в локальном кэше реестра. Она выводит выходные данные следующего вида:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Проверка подлинности в реестре

Выполните `helm registry login` команду в CLI Helm 3, чтобы выполнить [аутентификацию в реестре](container-registry-authentication.md) , используя учетные данные, подходящие для вашего сценария.

Например, создайте [субъект-службу Azure Active Directory с разрешениями Pull и Push](container-registry-auth-service-principal.md#create-a-service-principal) (роль акрпуш) в реестре. Затем укажите учетные данные субъекта `helm registry login`-службы в. В следующем примере пароль предоставляется с помощью переменной среды:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Принудительная отправка диаграммы в реестр контейнеров Azure

Выполните `helm chart push` команду в CLI Helm 3, чтобы передать диаграмму в полный целевой репозиторий:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

После успешной принудительной отправки выходные данные выглядят примерно так:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Вывод списка диаграмм в репозитории

Как и в случае с изображениями, хранящимися в реестре контейнеров Azure, можно использовать команды [AZ контроля][az-acr-repository] учетных записей, чтобы отобразить репозитории, в которых размещены диаграммы, а также теги и манифесты диаграммы. 

Например, выполните команду [AZ контроля доступа к репозиторию][az-acr-repository-show] , чтобы просмотреть свойства репозитория, созданного на предыдущем шаге.

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

Чтобы просмотреть сведения о диаграмме, хранящейся в репозитории, выполните команду AZ запись в [репозитории: Показать-манифесты][az-acr-repository-show-manifests] . Пример:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Выходные данные, сокращенные в этом примере, показывают `configMediaType` `application/vnd.cncf.helm.config.v1+json`:

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

### <a name="pull-chart-to-local-cache"></a>Извлечь диаграмму в локальный кэш

Чтобы установить Helm диаграмму в Kubernetes, эта диаграмма должна находиться в локальном кэше. В этом примере сначала выполните команду `helm chart remove` , чтобы удалить существующую локальную диаграмму с именем `mycontainerregistry.azurecr.io/helm/hello-world:v1`:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Выполните `helm chart pull` команду, чтобы скачать диаграмму из реестра контейнеров Azure в локальный кэш:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Экспортировать Helm диаграмму

Для дальнейшей работы с диаграммой экспортируйте ее в локальный каталог с помощью `helm chart export`. Например, экспортируйте диаграмму, которая была извлечена в `install` каталог:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Чтобы просмотреть сведения о экспортированной диаграмме в репозитории, выполните `helm show chart` команду в каталоге, в котором была экспортирована диаграмма.

```console
cd install
helm show chart hello-world
```

Helm возвращает подробные сведения о последней версии диаграммы, как показано в следующем примере выходных данных:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Установить Helm диаграмму

Выполните `helm install` команду, чтобы установить диаграмму Helm, которая была извлечена в локальный кэш и экспортирована. Укажите имя выпуска, например *михелмтест*, или передайте `--generate-name` параметр. Пример:

```console
helm install myhelmtest ./hello-world
```

Выходные данные после успешной установки диаграммы похожи на следующие:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Чтобы проверить установку, выполните `helm get manifest` команду. Команда возвращает данные YAML в файле `configmap.yaml` шаблона.

Выполните `helm uninstall` команду, чтобы удалить выпуск диаграммы в кластере:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Удаление диаграммы Helm из репозитория

Чтобы удалить диаграмму из репозитория, используйте команду [AZ запись репозитория Delete][az-acr-repository-delete] . Выполните следующую команду и подтвердите операцию при появлении запроса:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Использование клиента Helm 2

### <a name="prerequisites"></a>Предварительные условия

- **Реестр контейнеров Azure** в подписке Azure. При необходимости создайте реестр с помощью [портал Azure](container-registry-get-started-portal.md) или [Azure CLI](container-registry-get-started-azure-cli.md).
- **Клиент Helm начиная с версии 2.11.0 (не релиз-кандидат)**. Выполните команду `helm version`, чтобы узнать свою версию. Вам также потребуется сервер Helm (Tiller), инициализированный в кластере Kubernetes. При необходимости создайте [кластер службы Azure Kubernetes][aks-quickstart]. Дополнительные сведения о том, как установить и обновить Helm, см. [здесь][helm-install-v2].
- **Azure CLI версии 2.0.46 или более поздней.** Выполните команду `az --version`, чтобы получить сведения о версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

### <a name="high-level-workflow"></a>Рабочий процесс высокого уровня

С **Helm 2** вы:

* Настройте реестр контейнеров Azure как *единое* хранилище диаграмм Helm. Реестр контейнеров Azure управляет определением индекса при добавлении и удалении диаграмм в репозитории.
* Выполните аутентификацию в реестре контейнеров Azure с помощью Azure CLI, который автоматически обновляет клиент Helm, используя URI и учетные данные реестра. Эти данные реестра не нужно указывать вручную, поэтому учетные данные не отображаются в журнале команд.
* Используйте команды [AZ Helm][az-acr-helm] в Azure CLI, чтобы добавить реестр контейнеров Azure в качестве репозитория диаграммы Helm, а также для отправки диаграмм и управления ими. Эти Azure CLI команды создают оболочку для команд клиента Helm 2.
* Добавьте репозиторий диаграммы в реестр контейнеров Azure в локальный индекс репозитория Helm, поддерживающий поиск в диаграмме.
* Используйте `helm install` для установки диаграмм в кластер Kubernetes из локального кэша репозитория.

Примеры см. в следующих разделах.

### <a name="add-repository-to-helm-client"></a>Добавление репозитория в клиент Helm

Добавьте в клиент Helm репозиторий диаграммы Helm реестра контейнеров Azure с помощью команды [AZ контроля доступа Helm репозиторий][az-acr-helm-repo-add] . Эта команда предоставляет маркер аутентификации для Реестра контейнеров Azure, используемый клиентом Helm. Маркер проверки подлинности действителен в течение 3 часов. Как и `docker login`, эту команду можно выполнить в будущих сеансах интерфейса командной строки для аутентификации клиента Helm в репозитории диаграмм Helm Реестра контейнеров Azure:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Добавление образца диаграммы в репозиторий

Сначала создайте локальный каталог в папке *~/Акр-Хелм*, а затем скачайте имеющуюся *стабильную или WordPress* диаграмму:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Введите `ls` , чтобы получить список загруженной диаграммы, и обратите внимание на версию WordPress, которая включена в имя файла. Команда `helm fetch stable/wordpress` не предоставила определенную версию, поэтому выбрана *последняя* версия. В следующем примере выходных данных WordPress диаграмма имеет версию *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Отправьте диаграмму в репозиторий диаграммы Helm в реестре контейнеров Azure, выполнив команду [AZ контроля доступа Helm Push][az-acr-helm-push] в Azure CLI. Укажите имя диаграммы Helm, скачанной на предыдущем шаге, например *ВордПресс-8.1.0. tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Через несколько секунд Azure CLI сообщает о сохранении диаграммы, как показано в следующем примере выходных данных:

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

С помощью диаграммы, хранимой в репозитории, и обновленного индекса, доступного в локальной среде, вы можете использовать команды обычного клиента Helm для поиска или установки. Чтобы просмотреть все диаграммы в репозитории, используйте `helm search` команду, указав собственное имя реестра контейнеров Azure:

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

Чтобы просмотреть сведения о конкретной диаграмме в репозитории, можно использовать `helm inspect` команду.

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

Вы также можете просмотреть данные диаграммы с помощью команды Azure CLI [az acr helm show][az-acr-helm-show]. Как и в предыдущем примере, по умолчанию возвращается *последняя* версия диаграммы. Можно добавить `--version` к списку определенную версию диаграммы, например *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Установка диаграммы Helm из репозитория

Диаграмма Helm в репозитории устанавливается путем указания имени репозитория и имени диаграммы. Для установки диаграммы WordPress можно использовать клиент Helm:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Если отправить репозиторий диаграмм Helm в Реестр контейнеров Azure, а затем вернуться к работе в новом сеансе интерфейса командной строки, для локального клиента Helm необходимо будет предоставить обновленный маркер аутентификации. Чтобы получить новый маркер аутентификации, используйте команду [az acr helm repo add][az-acr-helm-repo-add].

Во время установки выполняются следующие действия:

- Клиент Helm выполняет поиск индекса локального репозитория.
- Соответствующая диаграмма скачивается из репозитория Реестра контейнеров Azure.
- Диаграмма развертывается с помощью Tiller в кластере Kubernetes.

По мере продолжения установки следуйте инструкциям в выходных данных команды, чтобы просмотреть URL-адреса и учетные данные Ворпресс. Вы также можете выполнить `kubectl get pods` команду, чтобы просмотреть ресурсы Kubernetes, развернутые с помощью диаграммы Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Удаление диаграммы Helm из репозитория

Чтобы удалить диаграмму из репозитория, используйте команду [az acr helm delete][az-acr-helm-delete]. Укажите имя диаграммы, например *WordPress*, и версию для удаления, например *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Если вы хотите удалить все версии указанной диаграммы, не указывайте параметр `--version`.

Диаграмма будет возвращена при запуске `helm search`. Как и в предыдущем примере, в клиенте Helm список доступных диаграмм в репозитории не обновляется автоматически. Чтобы обновить индекс репозитория клиента Helm, используйте команду [az acr helm repo add][az-acr-helm-repo-add]:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения о создании и развертывании собственных диаграмм Helm см. в разделе документации, посвященном [разработке диаграмм Helm][develop-helm-charts].
* Дополнительные сведения об установке приложений с помощью Helm в [службе Kubernetes Azure (AKS)](../aks/kubernetes-helm.md).
* Диаграммы Helm можно использовать в процессе создания контейнеров. Дополнительные сведения см. в статье [Использование задач реестра контейнеров Azure][acr-tasks].

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
