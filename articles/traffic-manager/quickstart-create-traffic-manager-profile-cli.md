---
title: Краткое руководство. Создание профиля диспетчера трафика для обеспечения высокого уровня доступности приложений с помощью Azure CLI
description: В этом кратком руководстве описано, как создать профиль диспетчера трафика для сборки высокодоступного веб-приложения.
services: traffic-manager
author: asudbring
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: allensu
ms.openlocfilehash: dc7e555eb95cf88ecf57a6df4999672bb721b8cf
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618794"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Краткое руководство. Создание профиля диспетчера трафика для высокодоступного веб-приложения с помощью Azure CLI

В этом кратком руководстве показано, как создать профиль диспетчера трафика, который обеспечивает высокий уровень доступности веб-приложения.

В этом кратком руководстве вы создадите два экземпляра веб-приложения. Каждый из них выполняется в разном регионе Azure. Будет создан профиль диспетчера трафика, который основывается на [приоритете конечной точки](traffic-manager-routing-methods.md#priority). Профиль будет направлять пользовательский трафик к первичному сайту, который запускает веб-приложение. Диспетчер трафика постоянно отслеживает веб-приложение. Если основной сайт недоступен, он предоставляет автоматический переход на резервный сайт.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.28 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Создание профиля диспетчера трафика

С помощью команды [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) создайте профиль диспетчера трафика, который направляет пользовательский трафик на основании приоритета конечной точки.

В следующем примере замените **<profile_name>** уникальным именем профиля диспетчера трафика.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Создание веб-приложений

Для этого краткого руководства необходимо развернуть два экземпляра веб-приложения в разных регионах Azure (*восточная часть США* и *Западная Европа*). Каждый будет использоваться в качестве основных конечных точек и конечных точек отработки отказа для диспетчера трафика.

### <a name="create-web-app-service-plans"></a>Создание планов службы веб-приложений
С помощью команды [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) создайте планы службы веб-приложений для двух экземпляров веб-приложения, которые будут развернуты в двух разных регионах Azure.

В следующем примере замените **<appspname_eastus>** и **<appspname_westeurope>** уникальным именем плана службы приложений.

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Создание веб-приложения в плане службы приложений
С помощью команды [az webapp create](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) создайте два экземпляра веб-приложения в планах службы приложений в таких регионах Azure, как *восточная часть США* и *Западная Европа*.

В следующем примере замените **<app1name_eastus>** и **<app2name_westeurope>** уникальными именами приложения, а **<appspname_eastus>** и **<appspname_westeurope>**  — именами, которые использовались для создания планов службы приложений в предыдущем разделе.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Добавление конечных точек диспетчера трафика
Чтобы добавить два веб-приложения в качестве конечных точек в профиль диспетчера трафика, используйте команду [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) и сделайте следующее.

- Определите идентификатор веб-приложения и добавьте веб-приложение в регион Azure *восточная часть США* как основную конечную точку для маршрутизации всего пользовательского трафика. 
- Определите идентификатор веб-приложения и добавьте веб-приложение в регион Azure *Западная Европа* как конечную точку отработки отказа. 

Если основная конечная точка недоступна, трафик автоматически направляется на конечную точку отработки отказа.

В следующем примере замените **<app1name_eastus>** и **<app2name_westeurope>** именами приложений, созданными для каждого региона в предыдущем разделе, **<appspname_eastus>** и **<appspname_westeurope>**  — именами, которые использовались для создания планов службы приложений в предыдущем разделе, а **<profile_name>**  — именем профиля из раздела выше. 

**Конечная точка в восточной части США**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```
Запишите идентификатор, отображаемый в выходных данных, и используйте его в следующей команде, чтобы добавить конечную точку.

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Конечная точка в Западной Европе**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```
Запишите идентификатор, отображаемый в выходных данных, и используйте его в следующей команде, чтобы добавить конечную точку.

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Проверка профиля диспетчера трафика

В этом разделе произойдет проверка доменного имени профиля диспетчера трафика. Основная конечная точка должна быть недоступной. В результате вы увидите, что веб-приложение по-прежнему доступно. Причиной этого является отправление трафика к конечной точке отработки отказа диспетчером трафика.

В следующем примере замените **<app1name_eastus>** и **<app2name_westeurope>** именами приложений, созданными для каждого региона в предыдущем разделе, **<appspname_eastus>** и **<appspname_westeurope>**  — именами, которые использовались для создания планов службы приложений в предыдущем разделе, а **<profile_name>**  — именем профиля из раздела выше.

### <a name="determine-the-dns-name"></a>Определение DNS-имени

Определите DNS-имя профиля диспетчера трафика с помощью команды [az network traffic-manager profile show](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Скопируйте значение **RelativeDnsName**. DNS-именем в профиле диспетчера трафика будет *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Просмотр диспетчера трафика в действии
1. Введите DNS-имя своего профиля диспетчера трафика (*http://<* relativednsname *>.trafficmanager.net*) в веб-браузере, чтобы просмотреть веб-сайт по умолчанию для веб-приложения.

    > [!NOTE]
    > В этом кратком сценарии все запросы направляются к основной конечной точке, которой присваивается **Приоритет 1**.
2. Чтобы просмотреть отработку отказа диспетчера трафика в действии, отключите свой основной сайт с помощью команды [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Скопируйте DNS-имя своего профиля диспетчера трафика (*http://<* relativednsname *>.trafficmanager.net*), чтобы просмотреть веб-сайт в новом сеансе веб-браузера.
4. Убедитесь, что веб-приложение по-прежнему доступно.

## <a name="clean-up-resources"></a>Очистка ресурсов

Закончив работу, удалите группы ресурсов, веб-приложения и все связанные ресурсы с помощью команды [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurepowershell-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали профиль диспетчера трафика, который обеспечивает высокий уровень доступности веб-приложения. Дополнительные сведения о маршрутизации трафика см. в руководствах по диспетчеру трафика.

> [!div class="nextstepaction"]
> [Руководства по диспетчеру трафика](tutorial-traffic-manager-improve-website-response.md)
