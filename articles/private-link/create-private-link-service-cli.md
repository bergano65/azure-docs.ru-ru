---
title: Создание службы частной связи Azure с помощью Azure CLI
description: Узнайте, как создать службу частной связи Azure с помощью Azure CLI
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 87d0f08d67dbbe6a0fa1725aba850c8d9b6c5619
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104707"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Создание службы частной связи с помощью Azure CLI
В этой статье показано, как создать службу частной связи в Azure с помощью Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, в этом кратком руководстве вам потребуется использовать последнюю версию Azure CLI. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).
## <a name="create-a-private-link-service"></a>Создание службы "Приватный канал"
### <a name="create-a-resource-group"></a>Создать группу ресурсов

Перед созданием виртуальной сети необходимо создать группу ресурсов, которая будет содержать эту виртуальную сеть. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). В этом примере создается группа ресурсов с именем *myResourceGroup* в расположении *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Создать виртуальную сеть
Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/az-network-vnet-create). В этом примере создается виртуальная сеть по умолчанию с именем *myVirtualNetwork* с одной подсетью с именем *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Создание подсети
Создайте подсеть для виртуальной сети с помощью команды [AZ Network vnet подсети Create](/cli/azure/network/az-network-vnet-subnet-create). В этом примере создается подсеть с именем *mySubnet* в виртуальной сети *myVirtualNetwork* :

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Создание внутренней Load Balancer 
Создайте внутреннюю подсистему балансировки нагрузки с помощью команды [AZ Network фунтов Create](/cli/azure/network/lb#az-network-lb-create). В этом примере создается внутренний балансировщик нагрузки с именем *милб* в группе ресурсов с именем *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Создание пробы работоспособности балансировщика нагрузки

Зонд работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут принимать трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не определит его работоспособность. Создайте зонд работоспособности с помощью команды [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest), чтобы отслеживать работоспособность виртуальных машин. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Создание правила балансировщика нагрузки

Правило подсистемы балансировки нагрузки определяет интерфейсную конфигурацию IP-адресов для входящего трафика и внутренний пул IP-адресов для приема трафика, а также порты источника и назначения. С помощью команды [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) создайте правило LB с именем *myHTTPRule* для ожидания передачи данных на порту 80, используемого внешним пулом *myFrontEnd*, и отправки трафика с балансировкой нагрузки внутреннему пулу адресов *myBackEndPool*, который также использует порт 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Создание внутренних серверов

В этом примере мы не рассмотрим создание виртуальной машины. Вы можете выполнить действия, описанные в статье [создание внутренней подсистемы балансировки нагрузки для балансировки нагрузки виртуальных машин с помощью Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) , чтобы создать две виртуальные машины, которые будут использоваться в качестве внутренних серверов для балансировщика нагрузки. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Отключить сетевые политики службы частной связи в подсети 
Для службы частной связи требуется IP-адрес из любой подсети, выбранной в виртуальной сети. Сейчас мы не поддерживаем сетевые политики для этих IP-адресов.  Поэтому необходимо отключить сетевые политики в подсети. Обновите подсеть, чтобы отключить сетевые политики службы частной связи, с помощью команды [AZ Network vnet подсети Update](/cli/azure/network/az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Создание службы "Приватный канал"  
 
Создайте службу частной связи с помощью многосерверной IP-конфигурации Load Balancer (цен. категория "Стандартный") с помощью команды [AZ Network Private-Link-Service Create](/cli/azure/network/az-network-private-link-service-create). В этом примере создается служба частной связи с именем *миплс* , использующая Load Balancer (цен. Категория "Стандартный") с именем *myLoadBalancer* в группе ресурсов с именем *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
После создания запишите идентификатор службы частной связи. Он потребуется позже для запроса подключения к этой службе.  
 
На этом этапе служба закрытых ссылок успешно создана и готова к получению трафика. Обратите внимание, что в примере выше показано только создание службы Private Link с помощью Azure CLI.  Мы не настроили серверные пулы подсистемы балансировки нагрузки или любое приложение в серверных пулах для прослушивания трафика. Если вы хотите просмотреть сквозные потоки трафика, настоятельно рекомендуем настроить приложение за Load Balancer (цен. категория "Стандартный").  
 
Далее будет продемонстрировано, как сопоставлять эту службу с частной конечной точкой в другой виртуальной сети с помощью Azure CLI. Опять же, пример ограничен созданием частной конечной точки и подключением к службе Private Link, созданной выше с помощью Azure CLI. Кроме того, можно создать виртуальные машины в виртуальной сети для отправки и получения трафика в частную конечную точку.        
 
## <a name="private-endpoints"></a>Частные конечные точки

### <a name="create-the-virtual-network"></a>Создание виртуальной сети 
Создайте виртуальную сеть с помощью команды [AZ Network vnet Create](/cli/azure/network/az-network-vnet-create). В этом примере создается виртуальная сеть с именем *мипевнет* в группе ресурсов с именем *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Создание подсети 
Создайте подсеть в виртуальной сети с помощью команды [AZ Network vnet подсети Create](/cli/azure/network/az-network-vnet-subnet-create). В этом примере создается подсеть с именем *mySubnet* в виртуальной сети с именем *мипевнет* в группе ресурсов с именем *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Отключить политики сети частной конечной точки в подсети 
Частная конечная точка может быть создана в любой подсети по вашему выбору в виртуальной сети. В настоящее время в частных конечных точках не поддерживаются сетевые политики.  Поэтому необходимо отключить сетевые политики в подсети. Обновите подсеть, чтобы отключить политики сети частной конечной точки, с [обновлением подсети AZ Network vnet](/cli/azure/network/az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Создание частной конечной точки и подключение к службе частной связи 
Создайте закрытую конечную точку для использования службы закрытых ссылок, созданной в виртуальной сети.
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
Вы можете получить *частный-Connection-Resource-id* с помощью `az network private-link-service show` службы Private Link. Идентификатор будет выглядеть следующим образом:   
/Субскриптионс/субид/ресаурцеграупс/*resourcegroupname*/провидерс/Микрософт.Нетворк/привателинксервицес/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Показывать подключения службы частной связи 
 
Просмотрите запросы на подключение к службе частной связи с помощью команды [AZ Network Private-Link-Service Показать](/cli/azure/network/az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Следующие шаги
- Дополнительные сведения о [службе частной связи Azure](private-link-service-overview.md)
 
