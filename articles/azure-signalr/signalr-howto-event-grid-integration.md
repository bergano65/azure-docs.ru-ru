---
title: Как отправить события службы Azure SignalR в сетку событий
description: Руководство, чтобы показать вам, как включить события Event Grid для службы SignalR, а затем отправить подключенные к клиенту связи / отключенные события в пример приложения.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: a76c9aaabf984723e2b60a7cd42425c9b29c916a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76710831"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>How to send events from Azure SignalR Service to Event Grid (Отправка событий из службы Azure SignalR в службу "Сетка событий")

Azure Event Grid — это полностью управляемая служба разгрома событий, обеспечивающая равномерное потребление событий с помощью модели pub-sub. В этом руководстве используется Azure CLI для создания службы Azure SignalR, подписки на события подключения, а затем развертывает пример веб-приложения для получения событий. Наконец, можно подключить и отключить и увидеть полезную нагрузку события в примере приложения.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][azure-account], прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Команды Azure CLI в этой статье отформатированы для выполнения в оболочке **Bash**. Если вы используете другую оболочку, такую ​​как PowerShell или командную строку, вам может потребоваться соответственно откорректировать символы продолжения строки или строки назначения переменных. В этой статье используются переменные, чтобы минимизировать требуемое изменение команд.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере с помощью команды [az group create][az-group-create] создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*. Если вы хотите использовать другое имя для своей группы ресурсов, установите другое значение для `RESOURCE_GROUP_NAME`.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Создание службы SignalR

Затем разместите службу Azure Signalr в группе ресурсов со следующими командами.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

После создания службы SignalR azure CLI возвращает выход, аналогичный следующему:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Создание конечной точки событий

В этом разделе вы используете шаблон Resource Manager, расположенный в репозитории GitHub, для развертывания готового примера веб-приложения в Службе приложений Azure. Позже вы подпишитесь на события Сетки событий вашего реестра и укажите это приложение как конечную точку, в которую отправляются события.

Чтобы развернуть пример приложения, задайте для `SITE_NAME` уникальное имя своего веб-приложения и выполните следующие команды. Имя сайта должно быть уникальным в пределах Azure, так как оно является частью полного доменного имени (FQDN) веб-приложения. В следующем разделе вы перейдете к полному доменному имени приложения в веб-браузере, чтобы просмотреть события реестра.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Как только развертывание успешно (это может занять несколько минут), откройте браузер и перейдите к веб-приложению, чтобы убедиться, что оно работает:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Подписка на события реестра

Подпишитесь на *раздел*, чтобы определить в Сетке событий Azure, какие события необходимо отслеживать и куда их отправлять. Следующие [события-подписка аз-грида создают][az-eventgrid-event-subscription-create] команду подписок на созданную службу Azure SignalR и определяют URL-адрес вашего веб-приложения в качестве конечной точки, на которую оно должно отправлять события. Переменные среды, которые вы заполнили в предыдущих разделах, используются здесь повторно, поэтому никаких изменений не требуется.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

После создания подписки вы увидите выходные данные, аналогичные следующим:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Активация событий реестра

Переключитесь в `Serverless Mode` режим обслуживания и установите клиентское подключение к службе SignalR. Вы можете взять в качестве [ссылки безсерверный образец.](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless)

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Просмотр событий реестра

Теперь вы подключили клиента к службе SignalR. Перейдите к веб-приложению Event Grid `ClientConnectionConnected` Viewer, и вы должны увидеть событие. Если вы прекратите работу клиента, `ClientConnectionDisconnected` вы также увидите событие.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
