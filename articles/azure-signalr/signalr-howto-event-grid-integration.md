---
title: Как отправлять события служба Azure SignalR "Сетка событий"
description: Структуру, чтобы показать, как включить события сетки событий для службы SignalR, отправить клиентское подключение подключен или отключен события к примеру приложения.
services: azure-signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 2d782306938136ce6d21a331185f591316f58a29
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789180"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Как отправлять события из Azure SignalR службы "Сетка событий"

Сетка событий Azure — это полностью управляемая служба маршрутизации событий, обеспечивает равномерное потребление событий с помощью модели публикации подписки. В этом руководстве используется Azure CLI создать службу Azure SignalR, подписаться на события соединения, а затем развернуть пример веб-приложения для отправки событий. Наконец можно подключиться и отключиться и см. в разделе полезных данных события в приложении-примере.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][azure-account], прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Команды Azure CLI в этой статье отформатированы для выполнения в оболочке **Bash**. Если вы используете другую оболочку, такую ​​как PowerShell или командную строку, вам может потребоваться соответственно откорректировать символы продолжения строки или строки назначения переменных. В этой статье используются переменные, чтобы минимизировать требуемое изменение команд.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. Следующие [Создание группы az][az-group-create] команда создает группу ресурсов с именем *myResourceGroup* в *eastus* регион. Если вы хотите использовать другое имя для своей группы ресурсов, установите другое значение для `RESOURCE_GROUP_NAME`.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Создание службы SignalR

Теперь можно разверните в службе Azure Signalr в группе ресурсов, выполнив следующие команды.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

После создания службы SignalR, Azure CLI возвращает выходные данные следующего вида:

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

После успешного развертывания (может занять несколько минут), откройте браузер и перейдите к веб-приложения, чтобы убедиться, что он работает:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Подписка на события реестра

Подпишитесь на *раздел*, чтобы определить в Сетке событий Azure, какие события необходимо отслеживать и куда их отправлять. Следующие [az eventgrid-подписки на события создания][az-eventgrid-event-subscription-create] команда подписывается на службу Azure SignalR, вы создали и задает URL-адрес веб приложения в качестве конечной точки, к которому необходимо отправлять события. Переменные среды, которые вы заполнили в предыдущих разделах, используются здесь повторно, поэтому никаких изменений не требуется.

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

Переключиться в режим службы для `Serverless Mode` и настройте подключение клиента к службе SignalR. Рекомендуем ознакомиться с [Бессерверных пример](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) как ссылка.

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

Клиент теперь подключен к службе SignalR. Перейдите в средство просмотра сетки событий веб-приложение, и вы увидите `ClientConnectionConnected` событий. Если вы завершения работы клиента, вы также увидите `ClientConnectionDisconnected` событий.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
