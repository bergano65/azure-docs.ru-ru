---
title: Сведения об управлении большими наборами разделов в Сетке событий Azure и публикация событий в этих разделах с помощью Доменов событий
description: Сведения о создании разделов и управлении ими в Сетке событий Azure и публикация событий в этих разделах с помощью Доменов событий.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 48a5356b03e38e864ba76f048febdb0b040893f5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669301"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Управление разделами и публикация событий с помощью Доменов событий

В этой статье показано, как сделать следующее:

* создать Домен в Сетке событий;
* оформить подписку на разделы;
* получение списка ключей;
* опубликовать события в Домен.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Создание Домена событий

Создать Домен событий можно с помощью расширения `eventgrid` для [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). После создания Домена его можно использовать для управления большими наборами разделов.

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

После успешного создания вернется следующий результат:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Запишите значения `endpoint` и `id`, так как они понадобятся для управления Доменом и публикации событий.

## <a name="create-topics-and-subscriptions"></a>Создание разделов и подписок

Служба "Сетка событий" автоматически создает соответствующий раздел в Домене и управляет им на основе вызова для создания подписки на событие для раздела Домена. Отдельного шага по созданию раздела в Домене нет. Аналогичным образом, когда последняя подписка на событие для раздела удаляется, сам раздел также удаляется.

Подписка на раздел в домене совпадает с подпиской на любой другой ресурс Azure:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

Идентификатор ресурса — это тот же идентификатор, который был возвращен при создании Домена ранее. Чтобы указать раздел, на который вы хотите подписаться, добавьте `/topics/<my-topic>` в конец идентификатора ресурса.

Чтобы создать подписку на событие в области Домена, которая принимает все события в Домене, укажите домен как `resource-id`, не вводя какие-либо разделы, например `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`.

Если вам нужна тестовая конечная точка, куда будут отправляться события, вы всегда можете развернуть [предварительно готовое веб-приложение](https://github.com/Azure-Samples/azure-event-grid-viewer), отображающее входящие события. Вы можете отправлять события на веб-сайт тестирования по адресу `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Разрешения, заданные для раздела, хранятся в Azure Active Directory и должны быть явно удалены. Удаление подписки на событие не отменяет доступ пользователей к созданию подписок на события, если у них есть доступ на запись в разделе.

## <a name="manage-access-to-topics"></a>Управление доступом к разделам

Управление доступом к разделам выполняется через [назначение ролей](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-cli). Назначение ролей использует проверку доступа на основе ролей, чтобы ограничить операции с ресурсами Azure для авторизованных пользователей в определенной области.

Сетка событий имеет две встроенные роли, которые можно использовать для назначения определенным пользователям доступа к различным разделам в пределах домена. Это роль `EventGrid EventSubscription Contributor (Preview)`, которая позволяет создавать и удалять подписки, и роль `EventGrid EventSubscription Reader (Preview)`, которая позволяет только перечислять подписки на события.

Следующая команда ограничивала бы `alice@contoso.com` созданием и удалением подписки на события только в разделе `foo`:

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

В статье [Сетка событий: безопасность и проверка подлинности](./security-authentication.md) см. дополнительные сведения о:

* контроле доступа к управлению;
* Типы операций
* создании настраиваемых определений роли.

## <a name="publish-events-to-an-event-grid-domain"></a>Публикация событий в Домен службы "Сетка событий"

Публикация событий в Домен совпадает с публикацией [в настраиваемый раздел](./post-to-custom-topic.md). Единственное различие заключается в том, что вам нужно указать раздел, к которому должно перенаправляться каждое событие. Следующий массив событий приведет к публикации события с идентификатором `"id": "1111"` в раздел `foo`, а событие с идентификатором `"id": "2222"` будет отправлено в раздел `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Чтобы получить ключи, которые будет использовать Домен, выполните команду ниже:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Затем используйте предпочтительный метод создания запроса HTTP POST для публикации событий в Домен Сетки событий.

## <a name="next-steps"></a>Дополнительная информация

* Общие понятия, касающиеся Доменов событий, и сведения об их эффективности см. в [этой статье](./event-domains.md).