---
title: Смена сертификатов в службе Kubernetes Azure (AKS)
description: Узнайте, как поворачивать сертификаты в кластере Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 00d8546cb20d12c5f1a94bdcababa04a77c73133
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134415"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Смена сертификатов в службе Kubernetes Azure (AKS)

Служба Azure Kubernetes Service (AKS) использует сертификаты для проверки подлинности с множеством своих компонентов. Периодически может потребоваться повернуть эти сертификаты для обеспечения безопасности или политики. Например, у вас может быть политика для смены всех сертификатов каждые 90 дней.

В этой статье показано, как поворачивать сертификаты в кластере AKS.

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этой статьей требуется Azure CLI версии 2.0.76 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].


### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Для использования этой функции требуется расширение CLI *AKS-Preview* версии 0.4.21 или более поздней. Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS сертификаты, центры сертификации и учетные записи служб

AKS создает и использует следующие сертификаты, центры сертификации и учетные записи служб:

* Сервер API AKS создает центр сертификации (ЦС), называемый ЦС кластера.
* Сервер API имеет ЦС кластера, который подписывает сертификаты для односторонней связи от сервера API к кубелетс.
* Каждый kubelet также создает запрос подписи сертификата (CSR), который подписывается ЦС кластера для обмена данными между kubelet и сервером API.
* Хранилище значений ключей etcd имеет сертификат, подписанный ЦС кластера для обмена данными между etcd и сервером API.
* Хранилище значений ключей etcd создает ЦС, который подписывает сертификаты для проверки подлинности и авторизации репликации данных между репликами etcd в кластере AKS.
* Агрегатор API использует ЦС кластера для выпусков сертификатов для взаимодействия с другими API, например Open Service Broker для Azure. Агрегатор API также может иметь собственный ЦС для выдачи этих сертификатов, но в настоящее время использует ЦС кластера.
* Каждый узел использует маркер учетной записи службы (SA), подписанный центром сертификации кластера.
* Клиент `kubectl` имеет сертификат для взаимодействия с кластером AKS.

> [!NOTE]
> В кластерах AKS, созданных до 2019 марта, срок действия сертификатов истекает через два года. Любой кластер, созданный после марта 2019 или любого кластера с повернутыми сертификатами, имеет срок действия сертификатов через 30 лет.

## <a name="rotate-your-cluster-certificates"></a>Смена сертификатов кластера

> [!WARNING]
> Смена сертификатов с помощью `az aks rotate-certs` может привести к простою до 30 минут для кластера AKS.

Для входа в кластер AKS используйте команду [AZ AKS Get-Credential][az-aks-get-credentials] . Эта команда также скачивает и настраивает сертификат клиента `kubectl` на локальном компьютере.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Используйте `az aks rotate-certs` для смены всех сертификатов, центров сертификации и SAs в кластере.

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Выполнение `az aks rotate-certs` может занять до 30 минут. Если перед завершением команды произошел сбой, используйте `az aks show` для проверки состояния кластера на *вращение сертификата*. Если кластер находится в состоянии сбоя, повторно запустите `az aks rotate-certs`, чтобы снова повернуть сертификаты.

Убедитесь, что старые сертификаты больше не являются допустимыми, выполнив команду `kubectl`. Так как вы не обновили сертификаты, используемые `kubectl`, вы увидите ошибку.  Например,

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Обновите сертификат, используемый `kubectl`, запустив `az aks get-credentials`.

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Убедитесь, что сертификаты обновлены, выполнив команду `kubectl`, которая теперь будет выполнена успешно. Например,

```console
kubectl get no
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье показано, как автоматически поворачивать сертификаты, CAs и SAs кластера. Рекомендации [по обеспечению безопасности и обновления кластера в службе Azure Kubernetes (AKS)][aks-best-practices-security-upgrades] см. в статье рекомендации по обеспечению безопасности AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
