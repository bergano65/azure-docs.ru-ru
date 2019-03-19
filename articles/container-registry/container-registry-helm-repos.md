---
title: Использование репозиториев Helm в Реестре контейнеров Azure
description: Сведения о способах использования репозитория Helm в Реестре контейнеров Azure для хранения диаграмм приложений.
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551999"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Использование Реестра контейнеров Azure в качестве репозитория Helm для диаграмм приложения

Для быстрого развертывания приложений и управления ими в Kubernetes можно использовать [диспетчер пакетов Helm с открытым исходным кодом][helm]. При использовании Helm приложения определяются как *диаграммы*, хранящиеся в репозитории диаграмм Helm. Эти диаграммы определяют конфигурации и зависимости. Для них может осуществляться управление версиями на протяжении всего жизненного цикла приложения. Реестр контейнеров Azure можно использовать как узел для репозиториев диаграмм Helm.

С Реестром контейнеров Azure предоставляется конфиденциальный, безопасный репозиторий диаграмм Helm, который можно интегрировать с конвейерами сборки или другими службами Azure. Репозитории диаграмм Helm в Реестре контейнеров Azure предоставляют функции георепликации для хранения диаграмм в непосредственной пространственной близости от развертываний, а также для обеспечения избыточности. Вы платите только за объем хранилища, используемый для диаграмм. Эти возможности доступны во всех ценовых категориях Реестра контейнеров Azure.

В этой статье показано, как использовать репозиторий диаграмм Helm, хранящийся в Реестре контейнеров Azure.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются только в том случае, если вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="before-you-begin"></a>Перед началом работы

Чтобы выполнить действия, описанные в этой статье, необходимо выполнить следующие предварительные требования.

- **Реестр контейнеров Azure.** Создайте реестр контейнеров в своей подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md).
- **Клиент Helm начиная с версии 2.11.0 (не релиз-кандидат)**. Выполните команду `helm version`, чтобы узнать свою версию. Вам также потребуется сервер Helm (Tiller), инициализированный в кластере Kubernetes. При необходимости можно [создать кластер Службы Azure Kubernetes][aks-quickstart]. Дополнительные сведения о том, как установить и обновить Helm, см. [здесь][helm-install].
- **Azure CLI версии 2.0.46 или более поздней.** Выполните команду `az --version`, чтобы получить сведения о версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Добавление репозитория в клиент Helm

Репозиторий Helm — это HTTP-сервер, на котором можно хранить диаграммы Helm. Реестр контейнеров Azure обеспечивает это хранилище для диаграмм Helm и позволяет управлять определением индекса по мере добавления и удаления диаграмм в репозитории.

Чтобы добавить Реестр контейнеров Azure как репозиторий диаграмм Helm, используйте Azure CLI. При использовании этого подхода для клиента Helm задается универсальный код ресурса (URI) и учетные данные репозитория, поддерживаемого Реестром контейнеров Azure. Вам не нужно вручную указывать сведения об этом репозитории, поэтому учетные данные не предоставляются в журнале команд.

При необходимости войдите в Azure CLI и следуйте инструкциям на экране.

```azurecli
az login
```

С помощью команды [az configure][az-configure] настройте значения по умолчанию для Azure CLI, используя имя Реестра контейнеров Azure. В следующем примере замените `<acrName>` именем своего реестра:

```azurecli
az configure --defaults acr=<acrName>
```

Теперь добавьте репозиторий диаграмм Helm из Реестра контейнеров Azure в клиент Helm, используя команду [az acr helm repo add][az-acr-helm-repo-add]. Эта команда предоставляет маркер аутентификации для Реестра контейнеров Azure, используемый клиентом Helm. Этот маркер аутентификации действителен в течение 1 часа. Как и `docker login`, эту команду можно выполнить в будущих сеансах интерфейса командной строки для аутентификации клиента Helm в репозитории диаграмм Helm Реестра контейнеров Azure:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Добавление диаграммы в репозиторий

В рамках этой статьи вы получите существующую диаграмму Helm из общедоступного репозитория Helm *stable*. Репозиторий *stable* — это рекомендуемый общедоступный репозиторий, содержащий общие диаграммы приложений. Распространители пакетов могут отправлять диаграммы в репозиторий *stable* тем же способом, что и Центр Docker предоставляет общедоступный реестр для общих образов контейнера. Диаграммы, скачанные из общедоступного репозитория *stable*, можно затем отправить в закрытый репозиторий Реестра контейнеров Azure. В большинстве случаев необходимо создать и отправить собственные диаграммы для разрабатываемых приложений. Дополнительные сведения о создании собственных диаграмм Helm см. в разделе документации, посвященном [разработке диаграмм Helm][develop-helm-charts].

Сначала создайте каталог в *~/acr-helm*, а затем скачайте имеющуюся диаграмму *stable/wordpress*:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Выведите сведения о скачанной диаграмме и запишите версию WordPress, содержащуюся в имени файла. Команда `helm fetch stable/wordpress` не предоставила определенную версию, поэтому выбрана *последняя* версия. Все диаграммы Helm содержат номер версии в имени файла в соответствии со стандартом [SemVer 2][semver2]. В следующем примере выходных данных используется диаграмма Wordpress версии *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Теперь отправьте диаграмму в репозиторий диаграмм Helm в Реестре контейнеров Azure с помощью команды Azure CLI [az acr helm push][az-acr-helm-push]. Укажите имя скачанной на предыдущем шаге диаграммы Helm, например *wordpress-2.1.10.tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Через несколько секунд в Azure CLI отобразится оповещение о сохранении диаграммы, как показано в следующем примере выходных данных:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Вывод списка диаграмм в репозитории

Клиент Helm поддерживает локальную кэшированную копию содержимого удаленных репозиториев. Изменения, внесенные в удаленный репозиторий, не приводят к автоматическому обновлению списка доступных диаграмм, известных в локальной среде клиента Helm. При поиске диаграмм в репозиториях Helm использует локальный кэшированный индекс. Для использования диаграммы, отправленной на предыдущем шаге, необходимо обновить локальный индекс репозитория Helm. Вы можете выполнить повторную индексацию репозиториев в клиенте Helm или использовать Azure CLI, чтобы обновить индекс репозитория. Этот шаг необходимо выполнять при каждом добавлении диаграммы в репозиторий:

```azurecli
az acr helm repo add
```

С помощью диаграммы, хранимой в репозитории, и обновленного индекса, доступного в локальной среде, вы можете использовать команды обычного клиента Helm для поиска или установки. Чтобы просмотреть все диаграммы в репозитории, используйте команду `helm search <acrName>`. Укажите собственное имя Реестра контейнеров Azure:

```console
helm search <acrName>
```

Диаграмма Wordpress, принудительно отправленная на предыдущем шаге, выведена в списке, как показано в следующем примере выходных данных:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Вы также можете вывести список диаграмм с помощью Azure CLI, используя команду [az acr helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Просмотр информации о диаграмме Helm

Чтобы просмотреть сведения об определенной диаграмме в репозитории, можно также использовать обычный клиент Helm. Чтобы просмотреть сведения о диаграмме с именем *WordPress*, используйте команду `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Если номер версии не указан, используется *последняя* версия. Helm возвращает подробные сведения о диаграмме, как показано в следующем сокращенном примере выходных данных:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
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
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Вы также можете просмотреть данные диаграммы с помощью команды Azure CLI [az acr helm show][az-acr-helm-show]. Как и в предыдущем примере, по умолчанию возвращается *последняя* версия диаграммы. Вы можете добавить `--version`, чтобы вывести определенную версию диаграммы, например *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Установка диаграммы Helm из репозитория

Диаграмма Helm в репозитории устанавливается путем указания имен репозитория и диаграммы. Для установки диаграммы WordPress можно использовать клиент Helm:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Если отправить репозиторий диаграмм Helm в Реестр контейнеров Azure, а затем вернуться к работе в новом сеансе интерфейса командной строки, для локального клиента Helm необходимо будет предоставить обновленный маркер аутентификации. Чтобы получить новый маркер аутентификации, используйте команду [az acr helm repo add][az-acr-helm-repo-add].

Во время установки выполняются следующие действия:

- Клиент Helm выполняет поиск индекса локального репозитория.
- Соответствующая диаграмма скачивается из репозитория Реестра контейнеров Azure.
- Диаграмма развертывается с помощью Tiller в кластере Kubernetes.

В следующем сжатом примере выходные данные содержат сведения о ресурсах Kubernetes, развернутых с использованием диаграммы Helm:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Удаление диаграммы Helm из репозитория

Чтобы удалить диаграмму из репозитория, используйте команду [az acr helm delete][az-acr-helm-delete]. Укажите имя диаграммы, например *wordpress*, и версию, которую необходимо удалить, например *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Если вы хотите удалить все версии указанной диаграммы, не указывайте параметр `--version`.

Диаграммы продолжают возвращаться в выходных данных `helm search <acrName>`. Как и в предыдущем примере, в клиенте Helm список доступных диаграмм в репозитории не обновляется автоматически. Чтобы обновить индекс репозитория клиента Helm, используйте команду [az acr helm repo add][az-acr-helm-repo-add]:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этой статьи применялась имеющаяся диаграмма Helm из общедоступного репозитория *stable*. Дополнительные сведения о создании и развертывании собственных диаграмм Helm см. в разделе документации, посвященном [разработке диаграмм Helm][develop-helm-charts].

Диаграммы Helm можно использовать в процессе создания контейнеров. Дополнительные сведения см. в статье [Automate OS and framework patching with ACR Tasks][acr-tasks] (Автоматизация установки исправлений ОС и платформы с помощью задач Реестра контейнеров Azure).

Дополнительные сведения о том, как использовать Реестр контейнеров Azure и управлять им, см. в [этой статье с рекомендациями][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
