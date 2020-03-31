---
title: Создание службы частной ссылки Azure с помощью Azure CLI
description: Узнайте, как создать службу частного ссылки Azure с помощью ClI Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350239"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Создание службы приватного соединения с помощью Azure CLI
В этой статье показано, как создать службу private Link в Azure с помощью Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вместо этого вы решили установить и использовать Azure CLI локально, этот быстрый запуск требует от вас использования последней версии Azure CLI. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).
## <a name="create-a-private-link-service"></a>Создание службы "Приватный канал"
### <a name="create-a-resource-group"></a>Создание группы ресурсов

Перед созданием виртуальной сети необходимо создать группу ресурсов, которая будет содержать эту виртуальную сеть. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). В этом примере создается группа ресурсов с именем *myResourceGroup* в расположении *westcentralus*.

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Создание виртуальной сети
Создайте виртуальную сеть с [az сети Vnet создать](/cli/azure/network/vnet#az-network-vnet-create). Этот пример создает виртуальную сеть по умолчанию под названием *myVirtualNetwork* с одной подсетью под названием *mySubnet:*

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Создание подсети
Создайте подсеть для виртуальной сети с [подсетью сети az vnet.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) Этот пример создает подсеть под названием *mySubnet* в виртуальной сети *myVirtualNetwork:*

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Создание баланса внутренней нагрузки 
Создайте внутренний балансик нагрузки с [az сети lb создать.](/cli/azure/network/lb#az-network-lb-create) Этот пример создает внутренний балансисатор нагрузки под названием *myILB* в группе ресурсов под названием *myResourceGroup.* 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Создание пробы работоспособности балансировщика нагрузки

Зонд работоспособности проверяет все экземпляры виртуальной машины, чтобы убедиться, что они могут принимать трафик. Экземпляр виртуальной машины с неудачной пробой удаляется из балансировщика нагрузки, пока не перейдет в оперативный режим и проба не определит его работоспособность. Создайте зонд здоровья с [az зондом lb сети создать](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) для того чтобы контролировать работоспособность фактически машин. 

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

В этом примере мы не освещаем создание виртуальных машин. Вы можете следить за шагами в [создании внутреннего балансомера нагрузки для загрузки баланса Виртуальных технологий с помощью Azure CLI](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) для создания двух виртуальных машин, которые будут использоваться в качестве серверов бэкэнда для балансировора нагрузки. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Отключить политику сети услуг Private Link в подсети 
Служба Private Link требует IP-адреса от любой подсети по вашему выбору в виртуальной сети. В настоящее время мы не поддерживаем сетевые политики на этих ИП.  Следовательно, мы должны отключить сетевые политики в подсети. Обновление подсети, чтобы отключить политику сети услуг Private Link с [обновлением подсети сети az.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Создание службы "Приватный канал"  
 
Создание службы Private Link с использованием конфигурации IP-адреса Standard Load Balancer с [помощью создания частной ссылки сети az.](/cli/azure/network/private-link-service#az-network-private-link-service-create) Этот пример создает службу Private Link под названием *myPLS* с использованием Standard Load Balancer под названием *myLoadBalancer* в ресурсной группе под названием *myResourceGroup.* 
 
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
После создания обратите внимание на идентификатор службы частной ссылки. Это понадобятся позже для запроса подключения к этой службе.  
 
На этом этапе успешно создается служба Private Link, которая готова к приему трафика. Обратите внимание, что приведенный выше пример предназначен только для демонстрации создания сервиса Private Link с помощью Azure CLI.  Мы не настроили пулы бэкэнда нагрузочика или любое приложение в пулах бэкэнда, чтобы прослушать трафик. Если вы хотите видеть сквозные транспортные потоки, настоятельно рекомендуется настроить приложение по стандартному балансу.  
 
Далее мы продемонстрируем, как сопоставить эту службу с частной конечной точкой в другой виртуальной сети с помощью Azure CLI. Опять же, пример ограничивается созданием частной конечных точек и подключением к службе Private Link, созданной выше с помощью Azure CLI. Кроме того, вы можете создавать виртуальные машины в виртуальной сети для отправки/получения трафика в частную конечную точку.        
 
## <a name="private-endpoints"></a>Частные конечные точки

### <a name="create-the-virtual-network"></a>Создание виртуальной сети 
Создайте виртуальную сеть с [az сети Vnet создать](/cli/azure/network/vnet#az-network-vnet-create). Этот пример создает виртуальную сеть под названием *myPEVNet* в ресурсной группе под названием *myResourcegroup:* 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Создание подсети 
Создание подсети в виртуальной сети с [az сети vnet подсети создать](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Этот пример создает подсеть под названием *mySubnet* в виртуальной сети под названием *myPEVnet* в группе ресурсов под названием *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Отключить политики частной сети конечных точек в подсети 
Частная конечная точка может быть создана в любой подсети по вашему выбору в виртуальной сети. В настоящее время мы не поддерживаем сетевые политики на частных конечных точках.  Следовательно, мы должны отключить сетевые политики в подсети. Обновление подсети, чтобы отключить частные политики сети конечных точек с [обновлением подсети сети az.](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Создание частной конечной точки и подключение к частной службе ссылки 
Создайте частную конечную точку для потребления услугPrivate Link, созданную выше в виртуальной сети:
  
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
Вы можете получить *частный-соединение-ресурс-id* с `az network private-link-service show` на частный ссылка службы. Идентификатор будет выглядеть следующим:   
/подписка/субID/ресурсгруппы/название*ресурсной группы*/провайдеры/Microsoft.Network/privateLinkServices/**privatelinkservicename/ privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Показать соединения службы Private Link 
 
Просматривайте запросы на подключение в службе Private Link с помощью [шоу частной ссылки az](/cli/azure/network/private-link-service#az-network-private-link-service-show)network.    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Дальнейшие действия
- Узнайте больше об [услуге частной ссылки Azure](private-link-service-overview.md)
 
