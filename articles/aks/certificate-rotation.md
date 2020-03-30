---
title: Поверните сертификаты в службе Azure Kubernetes (AKS)
description: Узнайте, как повернуть сертификаты в кластере Службы Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: f299b13baf5811b92bdc2e40b027868617d7574c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368525"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Поверните сертификаты в службе Azure Kubernetes (AKS)

Служба Azure Kubernetes (AKS) использует сертификаты для проверки подлинности со многими компонентами. Периодически может потребоваться повернуть эти сертификаты по соображениям безопасности или политики. Например, у вас может быть политика для ротации всех сертификатов каждые 90 дней.

В этой статье показано, как повернуть сертификаты в кластере AKS.

## <a name="before-you-begin"></a>Перед началом

Эта статья требует, чтобы вы запускали версию Azure CLI 2.0.77 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli-install]

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>Сертификаты AKS, органы сертификации и счета обслуживания

AKS генерирует и использует следующие сертификаты, органы сертификации и счета обслуживания:

* Сервер AKS API создает Сертификационный орган (CA), называемый кластером CA.
* Сервер API имеет кластер CA, который подписывает сертификаты для односторонней связи с сервером API кубелетов.
* Каждый кубелет также создает запрос на подписание сертификата (CSR), который подписывается кластером CA, для связи с кубелетом на сервер API.
* В магазине значений с etcd key имеетсертификат, подписанный кластерным CA для связи с etcd на сервер API.
* Магазин значений и т.д. создает ЦС, который подписывает сертификаты для проверки подлинности и авторизует репликацию данных между etcd репликами в кластере AKS.
* Агрегатор API использует Ca кластера для выдачи сертификатов для связи с другими API, такими как Open Service Broker for Azure. Агрегатор API также может иметь свой собственный CA для выдачи этих сертификатов, но в настоящее время он использует кластер CA.
* Каждый узла использует маркер учетной записи службы (SA), который подписывается Ca кластера.
* Клиент `kubectl` имеет сертификат для общения с кластером AKS.

> [!NOTE]
> Кластеры AKS, созданные до марта 2019 года, имеют сертификаты, срок действия которых истекает через два года. Любой кластер, созданный после марта 2019 года, или любой кластер с повернутыми сертификатами, имеет сертификаты кластера CA, срок действия которых истекает через 30 лет. Срок действия всех остальных сертификатов истекает через два года. Чтобы проверить, когда был `kubectl get nodes` создан кластер, используйте, чтобы увидеть *Возраст* пулов узлов.
> 
> Кроме того, можно проверить срок действия сертификата кластера. Например, следующая команда отображает сведения о сертификате для кластера *myAKSCluster.*
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>Поверните сертификаты кластера

> [!WARNING]
> Вращение сертификатов с использованием `az aks rotate-certs` может привести к тому, что кластер AKS может вызвать до 30 минут простоя.

Используйте [az aks получить полномочия,][az-aks-get-credentials] чтобы войти в кластер AKS. Эта команда также загружает и настраивает сертификат `kubectl` клиента на локальной машине.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Используйте `az aks rotate-certs` для вращения всех сертификатов, CA и SA в кластере.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Это может занять до 30 минут для `az aks rotate-certs` завершения. Если команда завершает работу `az aks show` перед завершением, используйте для проверки состояния кластера: *вращение сертификата.* Если кластер находится в неисправном состоянии, повторите, `az aks rotate-certs` чтобы снова повернуть сертификаты.

Убедитесь, что старые сертификаты больше `kubectl` не действительны при запуске команды. Поскольку вы не обновили `kubectl`сертификаты, используемые, вы увидите ошибку.  Пример:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Обновление сертификата, `kubectl` используемого при запуске. `az aks get-credentials`

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Проверка сертификатов была обновлена `kubectl` при запуске команды, которая теперь будет успешной. Пример:

```console
kubectl get no
```

> [!NOTE]
> Если у вас есть службы, которые работают поверх AKS, такие как [Пространства Azure Dev,][dev-spaces]возможно, потребуется [обновить сертификаты, связанные с этими службами.][dev-spaces-rotate]

## <a name="next-steps"></a>Дальнейшие действия

В этой статье показано, как автоматически вращать сертификаты кластера, CAs и SA. Вы можете ознакомиться [с рекомендациями по безопасности кластеров и обновлениям в службе Azure Kubernetes (AKS)][aks-best-practices-security-upgrades] для получения дополнительной информации о рекомендациях безопасности AKS.


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[dev-spaces-rotate]: ../dev-spaces/troubleshooting.md#error-using-dev-spaces-after-rotating-aks-certificates
