---
title: Webhooks to respond to registry actions
description: Learn how to use webhooks to trigger events when push or pull actions occur in your registry repositories.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454378"
---
# <a name="using-azure-container-registry-webhooks"></a>Использование веб-перехватчиков реестра контейнеров Azure

В реестре контейнеров Azure хранятся частные образы контейнеров Docker, а также осуществляется управление ими (подобно тому, как в Docker Hub хранятся общедоступные образы Docker). It can also host repositories for [Helm charts](container-registry-helm-repos.md) (preview), a packaging format to deploy applications to Kubernetes. Веб-перехватчики используются для активации событий при выполнении определенных действий в одном из репозиториев реестров. Веб-перехватчики могут реагировать на события на уровне реестра. Также их можно ориентировать на определенный тег репозитория. With a  [geo-replicated](container-registry-geo-replication.md) registry, you configure each webhook to respond to events in a specific regional replica.

Дополнительные сведения о запросах веб-перехватчика см. в [справочнике по схеме веб-перехватчика реестра контейнеров Azure](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Технические условия

* Реестр контейнеров Azure. Создайте реестр контейнеров в своей подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md). The [Azure Container Registry SKUs](container-registry-skus.md) have different webhooks quotas.
* Интерфейс командной строки Docker (Docker CLI). Установите [подсистему Docker](https://docs.docker.com/engine/installation/), чтобы настроить локальный компьютер в качестве узла Docker и получить доступ к командам Docker CLI.

## <a name="create-webhook---azure-portal"></a>Create webhook - Azure portal

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите в реестр контейнеров, в котором нужно создать веб-перехватчик.
1. Under **Services**, select **Webhooks**.
1. Щелкните **Добавить** на панели инструментов веб-перехватчика.
1. Заполните форму *Создать веб-перехватчик* следующими данными:

| Value | Описание |
|---|---|
| Webhook name | Имя, назначаемое веб-перехватчику. It may contain only letters and numbers, and must be 5-50 characters in length. |
| Location | For a [geo-replicated](container-registry-geo-replication.md) registry, specify the Azure region of the registry replica. 
| URI службы | Универсальный код ресурса (URI) для отправки веб-перехватчиком уведомлений POST. |
| Настраиваемые заголовки | Заголовки, которые требуется передавать вместе с запросом POST. Они должны быть в формате "ключ: значение". |
| "Trigger actions" (Активирующие действия) | Действия, которые активируют веб-перехватчик. Actions include image push, image delete, Helm chart push, Helm chart delete, and image quarantine. You can choose one or more actions to trigger the webhook. |
| Status | Состояние веб-перехватчика после его создания. По умолчанию он включен. |
| Область действия | Область действия веб-перехватчика. If not specified, the scope is for all events in the registry. It can be specified for a repository or a tag by using the format "repository:tag", or "repository:*" for all tags under a repository. |

Пример формы для веб-перехватчика приведен ниже.

![Пользовательский интерфейс создания веб-перехватчика ACR на портале Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Create webhook - Azure CLI

Чтобы создать веб-перехватчик с помощью Azure CLI, используйте команду [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create). The following command creates a webhook for all image delete events in the registry *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Проверка веб-перехватчика

### <a name="azure-portal"></a>портала Azure

Prior to using the webhook, you can test it with the **Ping** button. После нажатия этой кнопки к указанной конечной точке отправляется общий запрос POST и ответ записывается в журнал. Используя функцию проверки связи, мы сможем проверить, правильно ли настроен веб-перехватчик.

1. Выберите веб-перехватчик, который требуется проверить.
2. В верхней части панели инструментов выберите **Проверка связи**.
3. Просмотрите ответ от конечной точки в столбце **СОСТОЯНИЕ HTTP**.

![Пользовательский интерфейс создания веб-перехватчика ACR на портале Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Чтобы протестировать веб-перехватчик ACR с помощью Azure CLI, используйте команду [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Чтобы просмотреть результаты, используйте команду [az acr webhook list-events](/cli/azure/acr/webhook).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Удаление веб-перехватчика

### <a name="azure-portal"></a>портала Azure

Любой веб-перехватчик можно удалить, выбрав его и нажав кнопку **Удалить** на портале Azure.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Дальнейшие действия

### <a name="webhook-schema-reference"></a>Справочник по схеме веб-перехватчика

Дополнительные сведения о формате и свойствах полезных данных событий JSON, генерируемых Реестром контейнеров Azure, см. в справочнике по схеме веб-перехватчика:

[Azure Container Registry webhook schema reference](container-registry-webhook-reference.md) (Справочник по схеме реестра контейнеров Azure)

### <a name="event-grid-events"></a>События Сетки событий

В дополнение к событиям веб-перехватчика собственного реестра, описанным в этой статье, Реестр контейнеров Azure может генерировать события для Сетки событий.

[Краткое руководство. Отправка событий реестра контейнеров в Сетку событий](container-registry-event-grid-quickstart.md)
