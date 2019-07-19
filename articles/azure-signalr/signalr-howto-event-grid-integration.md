---
title: Отправка событий службы Azure SignalR в службу "Сетка событий"
description: Руководство по включению событий сетки событий для службы SignalR, а также по отправке подключенных или отключенных событий подключения клиента к образцу приложения.
services: signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 52e4194acd6a3abfed3fabadb892b0de76025b7e
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296862"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Отправка событий из службы Azure SignalR в службу "Сетка событий"

Сетка событий Azure — это полностью управляемая служба маршрутизации событий, которая обеспечивает унифицированное потребление событий с помощью модели Pub-подтипа. В этом пошаговом окне вы используете Azure CLI для создания службы Azure SignalR, подписки на события подключения, а затем развертывания примера веб-приложения для получения событий. Наконец, можно подключаться и отключаться и просматривать полезные данные события в примере приложения.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][azure-account], прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Команды Azure CLI в этой статье отформатированы для выполнения в оболочке **Bash**. Если вы используете другую оболочку, такую ​​как PowerShell или командную строку, вам может потребоваться соответственно откорректировать символы продолжения строки или строки назначения переменных. В этой статье используются переменные, чтобы минимизировать требуемое изменение команд.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. Следующая команда [AZ Group Create][az-group-create] создает группу ресурсов с именем *myResourceGroup* в регионе *eastus* . Если вы хотите использовать другое имя для своей группы ресурсов, установите другое значение для `RESOURCE_GROUP_NAME`.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Создание службы SignalR

Затем разверните службу Azure SignalR в группе ресурсов с помощью следующих команд.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

После создания службы SignalR Azure CLI возвращает выходные данные, аналогичные следующим:

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

После завершения развертывания (может занять несколько минут) откройте браузер и перейдите к веб-приложению, чтобы убедиться, что оно работает:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Подписка на события реестра

Подпишитесь на *раздел*, чтобы определить в Сетке событий Azure, какие события необходимо отслеживать и куда их отправлять. Следующая команда [AZ eventgrid Event-Subscription создает][az-eventgrid-event-subscription-create] подписку на созданную службу Azure SignalR и указывает URL-адрес вашего веб приложения в качестве конечной точки, в которую он должен отправить события. Переменные среды, которые вы заполнили в предыдущих разделах, используются здесь повторно, поэтому никаких изменений не требуется.

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

Переключитесь в режим `Serverless Mode` службы и настройте клиентское подключение к службе SignalR. В качестве образца можно использовать бессерверный [Пример](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) .

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Просмотр событий реестра

Теперь вы подключили клиента к службе SignalR. Перейдите к веб-приложению средства просмотра сетки событий, и вы увидите `ClientConnectionConnected` событие. Если вы завершите работу клиента, вы увидите также `ClientConnectionDisconnected` событие.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
