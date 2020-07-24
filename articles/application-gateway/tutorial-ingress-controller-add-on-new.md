---
title: Включение надстройки контроллера входящих данных для нового кластера AKS с новым экземпляром шлюза приложений Azure
description: Узнайте, как использовать Azure CLI, чтобы включить надстройку контроллера входящих данных для нового кластера AKS с новым экземпляром шлюза приложений.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 4634421829cf71c0c5b9476f8ff3d08b9caa7dbd
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117343"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Руководство. включение надстройки входящего контроллера (Предварительная версия) для нового кластера AKS с новым экземпляром шлюза приложений

Вы можете использовать Azure CLI, чтобы включить надстройку " [входной контроллер шлюза приложений" (агик)](ingress-controller-overview.md) для кластера [Azure KUBERNETES Services (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . Надстройка сейчас доступна в предварительной версии.

В этом руководстве вы создадите кластер AKS с включенной надстройкой АГИК. При создании кластера будет автоматически создан экземпляр шлюза приложений Azure для использования. Затем вы развернете пример приложения, которое будет использовать надстройку для предоставления приложения через шлюз приложений. 

Надстройка обеспечивает более быстрый способ развертывания АГИК для кластера AKS, чем [ранее через Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Он также предлагает полностью управляемый интерфейс.    

В этом руководстве вы узнаете, как:

> [!div class="checklist"]
> * Создайте группу ресурсов. 
> * Создайте новый кластер AKS с включенной надстройкой АГИК. 
> * Разверните пример приложения с помощью АГИК для входных данных в кластере AKS.
> * Убедитесь, что приложение достижимо через шлюз приложений.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования

Если вы решили установить и использовать CLI локально, для работы с этим руководством вам потребуется запустить Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

Зарегистрируйте флаг компонента *AKS-ингрессаппликатионгатевайаддон* с помощью команды [AZ Feature Register](https://docs.microsoft.com/cli/azure/feature#az-feature-register) , как показано в следующем примере. Это необходимо сделать только один раз для подписки, пока надстройка еще находится в режиме предварительной версии.
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
```

Отображение состояния может занять несколько минут `Registered` . Состояние регистрации можно проверить с помощью команды [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register):
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Когда будете готовы, обновите регистрацию поставщика ресурсов Microsoft. ContainerService с помощью команды [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider#az-provider-register) :
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Установите или обновите расширение AKS-Preview для этого руководства. Используйте следующие Azure CLI команды:
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

В Azure выделите связанные ресурсы группе ресурсов. Создайте группу ресурсов, используя команду [az group create](/cli/azure/group#az-group-create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *канадацентрал* (регион): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Развертывание кластера AKS с включенной надстройкой

Теперь вы можете развернуть новый кластер AKS с включенной надстройкой АГИК. Если вы не предоставляете существующий экземпляр шлюза приложений для использования в этом процессе, мы автоматически создадим и настроили новый экземпляр шлюза приложений для обслуживания трафика в кластере AKS.  

> [!NOTE]
> Надстройка входящего контроллера шлюза приложений поддерживает *только* номера SKU шлюза приложений v2 (Standard и WAF), а *не* номера SKU шлюза приложений версии v1. При развертывании нового экземпляра шлюза приложений с помощью надстройки АГИК можно развернуть только шлюз приложений Standard_v2 SKU. Если вы хотите включить надстройку для шлюза приложений WAF_v2 SKU, используйте один из следующих методов.
>
> - Включите WAF в шлюзе приложений на портале. 
> - Сначала создайте WAF_v2 экземпляр шлюза приложений, а затем следуйте инструкциям по [включению надстройки агик с существующим кластером AKS и существующим экземпляром шлюза приложений](tutorial-ingress-controller-add-on-existing.md). 

В следующем примере вы развернете новый кластер AKS с именем *myCluster* с помощью [CNI Azure](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) и [управляемых удостоверений](https://docs.microsoft.com/azure/aks/use-managed-identity). Надстройка АГИК будет включена в созданной группе ресурсов, *myResourceGroup*. 

Развертывание нового кластера AKS с включенной надстройкой АГИК без указания существующего экземпляра шлюза приложений приведет к автоматическому созданию экземпляра шлюза приложений Standard_v2 SKU. Таким образом, вы также укажете имя и адресное пространство подсети экземпляра шлюза приложений. Имя экземпляра шлюза приложений будет *мяппликатионгатевай*, а используемое адресное пространство подсети — 10.2.0.0/16. Убедитесь, что вы добавили или обновили расширение AKS-Preview в начале этого руководства. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Чтобы настроить дополнительные параметры для `az aks create` команды, см. [эти ссылки](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> Созданный кластер AKS появится в созданной группе ресурсов *myResourceGroup*. Однако автоматически созданный экземпляр шлюза приложений будет находиться в группе ресурсов узла, где находятся Пулы агентов. Группа ресурсов узла с именем *MC_resource-Group-name_cluster-name_location* по умолчанию, но может быть изменена. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Развертывание примера приложения с помощью АГИК

Теперь вы можете развернуть пример приложения в созданном кластере AKS. Приложение будет использовать надстройку АГИК для входящих подключений и подключить экземпляр шлюза приложений к кластеру AKS. 

Сначала получите учетные данные для кластера AKS, выполнив `az aks get-credentials` команду: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Теперь, когда у вас есть учетные данные, выполните следующую команду, чтобы настроить пример приложения, использующего АГИК для входящего трафика в кластер. АГИК обновит экземпляр шлюза приложений, настроенный ранее с соответствующими правилами маршрутизации для нового развертываемого примера приложения.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Проверка доступности приложения

Теперь, когда экземпляр шлюза приложений настроен для обслуживания трафика в кластере AKS, давайте проверим, доступен ли приложение. Сначала получите IP-адрес входящего трафика: 

```azurecli-interactive
kubectl get ingress
```

Убедитесь, что созданный пример приложения выполняется одним из следующих.

- Посещение IP-адреса экземпляра шлюза приложений, полученного при выполнении предыдущей команды.
- При помощи `curl`. 

Для получения обновления шлюзу приложений может потребоваться около минуты. Если шлюз приложений по-прежнему находится в состоянии **обновления** на портале, разрешите его завершение, прежде чем пытаться связаться с IP-адресом. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если они больше не нужны, удалите группу ресурсов, экземпляр шлюза приложений и все связанные с ним ресурсы.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия
* [Дополнительные сведения об отключении надстройки АГИК](./ingress-controller-disable-addon.md)
* [Сведения о том, какие заметки поддерживаются с помощью АГИК](./ingress-controller-annotations.md)
* [Устранение неполадок с АГИК](./ingress-controller-troubleshoot.md)

