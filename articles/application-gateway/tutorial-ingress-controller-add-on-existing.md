---
title: Включение надстройки входящего контроллера для существующего кластера AKS с существующим шлюзом приложений Azure
description: Используйте этот учебник, чтобы включить надстройку контроллера входящих данных для существующего кластера AKS с существующим шлюзом приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 42952e379b9f68008de23ee3b1717280d8dd6cb2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088127"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>Руководство. включение надстройки контроллера входящего трафика шлюза приложений для существующего кластера AKS с существующим шлюзом приложений с помощью Azure CLI (Предварительная версия)

Вы можете использовать Azure CLI, чтобы включить надстройку "входящий [контроллер" шлюза приложений (агик)](ingress-controller-overview.md) , которая в настоящее время доступна в предварительной версии для кластера [Azure KUBERNETES Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . В этом руководстве вы узнаете, как использовать надстройку АГИК, чтобы предоставить приложение Kubernetes в существующем кластере AKS с помощью существующего шлюза приложений, развернутого в отдельных виртуальных сетях. Начнем с создания кластера AKS в одной виртуальной сети и шлюза приложений в отдельной виртуальной сети для имитации существующих ресурсов. Затем вы включите надстройку АГИК, объедините две виртуальные сети и развернете пример приложения, которое будет предоставляться через шлюз приложений с помощью надстройки АГИК. Если вы включаете надстройку АГИК для существующего шлюза приложений и существующий кластер AKS в той же виртуальной сети, можно пропустить шаг пиринга ниже. Надстройка обеспечивает более быстрый способ развертывания АГИК для кластера AKS, чем [ранее через Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) , а также обеспечивает полностью управляемый интерфейс.  

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * Создание группы ресурсов 
> * Создание нового кластера AKS 
> * Создание нового шлюза приложений 
> * Включение надстройки АГИК в существующем кластере AKS с помощью существующего шлюза приложений 
> * Одноранговая виртуальная сеть шлюза приложений с виртуальной сетью кластера AKS
> * Развертывание примера приложения с использованием АГИК для входящего трафика в кластере AKS
> * Проверка доступности приложения через шлюз приложений

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Зарегистрируйте флаг функции *AKS-ингрессаппликатионгатевайаддон* с помощью команды [AZ Feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , как показано в следующем примере. Это необходимо сделать только один раз для каждой подписки, пока надстройка еще находится в предварительной версии:
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
```

Для отображения зарегистрированного состояния может потребоваться несколько минут. Состояние регистрации можно проверить с помощью команды [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register).
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.containerservice/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов Microsoft.ContainerService с помощью команды [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register).
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Обязательно установите или обновите расширение AKS-Preview для этого руководства; Используйте следующие команды Azure CLI
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

В Azure выделите связанные ресурсы группе ресурсов. Создайте группу ресурсов, используя команду [az group create](/cli/azure/group#az-group-create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *канадацентрал* (регион). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Развертывание нового кластера AKS

Теперь можно развернуть новый кластер AKS, чтобы имитировать наличие существующего кластера AKS, для которого требуется включить надстройку АГИК.  

В следующем примере вы разворачиваете новый кластер AKS с именем *myCluster* , используя [CNI Azure](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) и [управляемые удостоверения](https://docs.microsoft.com/azure/aks/use-managed-identity) в созданной группе ресурсов *myResourceGroup*.    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Чтобы настроить дополнительные параметры для `az aks create` команды, перейдите по ссылкам [здесь](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Развертывание нового шлюза приложений 

Теперь вы можете развернуть новый шлюз приложений, чтобы имитировать использование существующего шлюза приложений, который будет использоваться для балансировки нагрузки трафика в кластер AKS, *myCluster*. Имя шлюза приложений будет *мяппликатионгатевай*, но необходимо сначала создать ресурс общедоступного IP-адреса с именем *myPublicIp*и новую виртуальную сеть с именем *myVnet* с адресным пространством 11.0.0.0/8, а также подсеть с адресным пространством 11.1.0.0/16 с именем *mySubnet*и развернуть шлюз приложений в *mySubnet* с помощью *myPublicIp*. 

При использовании кластера AKS и шлюза приложений в отдельных виртуальных сетях адресные пространства двух виртуальных сетей не должны перекрываться. Адресное пространство по умолчанию, в котором развертывается кластер AKS, — 10.0.0.0/8, поэтому мы устанавливаем префикс адреса виртуальной сети шлюза приложений 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Надстройка "контроллер входящего трафика шлюза приложений" (АГИК) поддерживает **только** номера SKU шлюза приложений версии 2 (Standard и WAF), а **не** номера SKU шлюза приложений v1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>Включение надстройки АГИК в существующем кластере AKS с существующим шлюзом приложений 

Теперь вы включите надстройку АГИК в созданном кластере AKS, *myCluster*, и укажите НАДСТРОЙКу агик, чтобы использовать существующий шлюз приложений, который вы создали, *мяппликатионгатевай*. Убедитесь, что вы добавили или обновили расширение AKS-Preview в начале этого руководства. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>Совместное соединение между двумя виртуальными сетями

Так как мы развернули кластер AKS в своей виртуальной сети и шлюзе приложений в другой виртуальной сети, необходимо объединить две виртуальные сети, чтобы трафик перенаправлялся из шлюза приложений в модули Pod кластера. Для пиринга между двумя виртуальными сетями требуется выполнение команды Azure CLI два раза, чтобы обеспечить двустороннюю связь. Первая команда создаст подключение пиринга из виртуальной сети шлюза приложений к виртуальной сети AKS. Вторая команда создаст одноранговое соединение в другом направлении. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>Развертывание примера приложения с помощью АГИК 

Теперь вы можете развернуть пример приложения в созданном кластере AKS, который будет использовать надстройку АГИК для входящих подключений, и подключить шлюз приложений к кластеру AKS. Сначала вы получите учетные данные для кластера AKS, который вы развернули, выполнив `az aks get-credentials` команду. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

После получения учетных данных для созданного кластера выполните следующую команду, чтобы настроить пример приложения, использующего АГИК для входящего трафика в кластер. АГИК обновит шлюз приложений, настроенный ранее, с соответствующими правилами маршрутизации для нового развертываемого примера приложения.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Проверка доступности приложения

Теперь, когда шлюз приложений настроен для обслуживания трафика в кластере AKS, давайте проверим, доступен ли ваше приложение. Сначала вы получите IP-адрес входящего трафика. 

```azurecli-interactive
kubectl get ingress
```

Убедитесь, что созданный пример приложения запущен и работает, либо посетив IP-адрес шлюза приложений, полученный при выполнении указанной выше команды, или установите флажок с помощью `curl` . Для получения обновления шлюзу приложений может потребоваться около минуты. Поэтому, если шлюз приложений по-прежнему находится в состоянии обновления на портале, позвольте ему завершиться, прежде чем пытаться связаться с IP-адресом. 

## <a name="clean-up-resources"></a>Очистка ресурсов

При необходимости вы можете удалить группу ресурсов, шлюз приложений и все связанные ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения об отключении надстройки АГИК](./ingress-controller-disable-addon.md)
* [Дополнительные сведения о том, какие заметки поддерживаются с помощью АГИК](./ingress-controller-annotations.md)
* [Устранение неполадок с АГИК](./ingress-controller-troubleshoot.md)

