---
title: Руководство по Включение надстройки контроллера объекта ingress для нового кластера AKS с новым экземпляром Шлюза приложений Azure
description: Узнайте, как с помощью Azure CLI включить надстройку контроллера объекта ingress для нового кластера AKS с новым экземпляром Шлюза приложений Azure.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: ab917fe476a40eb8ea559bc08e52d4bbf16a8436
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285610"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Руководство по включению надстройки контроллера объекта ingress (предварительная версия) для нового кластера AKS с новым экземпляром Шлюза приложений

Используйте Azure CLI, чтобы включить надстройку [контроллера объекта ingress Шлюза приложений (AGIC)](ingress-controller-overview.md) для кластера [Службы Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/). Сейчас надстройка доступна в предварительной версии.

В этом учебнике описано, как создавать кластер AKS с включенной надстройкой AGIC. При создании кластера автоматически создается используемый экземпляр Шлюза приложений Azure. Затем вы развернете пример приложения, которое будет использовать надстройку для предоставления доступа к приложению через Шлюз приложений. 

Надстройка обеспечивает более быстрый способ развертывания AGIC для кластера AKS, чем [при использовании Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Она также предоставляет полностью управляемый интерфейс.    

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Создайте группу ресурсов. 
> * Создайте кластер AKS с включенной надстройкой AGIC. 
> * Разверните пример приложения с помощью AGIC для объекта ingress в кластере AKS.
> * Убедитесь, что приложение доступно через Шлюз приложений.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим учебником вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

Зарегистрируйте флаг компонента *AKS-IngressApplicationGatewayAddon*, используя команду [az feature register](https://docs.microsoft.com/cli/azure/feature#az-feature-register), как показано в указанном ниже примере. Это необходимо сделать только один раз для подписки, пока надстройка находится на стадии предварительной версии.
```azurecli-interactive
az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
```

Через несколько минут отобразится состояние `Registered`. Состояние регистрации можно проверить с помощью команды [az feature list](https://docs.microsoft.com/cli/azure/feature#az-feature-register):
```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов Microsoft.ContainerService с помощью команды [az provider register](https://docs.microsoft.com/cli/azure/provider#az-provider-register):
```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Установите или обновите расширение aks-preview для этого учебника. Используйте такие команды интерфейса командной строки Azure:
```azurecli-interactive
az extension add --name aks-preview
az extension list
```
```azurecli-interactive
az extension update --name aks-preview
az extension list
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

В Azure выделите связанные ресурсы группе ресурсов. Создайте группу ресурсов, используя команду [az group create](/cli/azure/group#az-group-create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении (регионе) *CanadaCentral*: 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Развертывание кластера AKS с включенной надстройкой

Теперь разверните кластер AKS с включенной надстройкой AGIC. Если вы не указали существующий экземпляр Шлюза приложений для использования в этом процессе, мы автоматически создадим и настроим экземпляр Шлюза приложений для обслуживания трафика в кластере AKS.  

> [!NOTE]
> Надстройка контроллера объекта ingress Шлюза приложений поддерживает *только* номер SKU Шлюза приложений версии 2 (Стандартный и WAF), а *не* версии 1. При развертывании нового экземпляра Шлюза приложений через надстройку AGIC вы можете развернуть только Шлюз приложений для SKU Standard_v2. Если вы хотите включить надстройку для WAF в SKU версии 2 Шлюза приложения, используйте один из следующих методов:
>
> - На портале включите WAF в Шлюзе приложений. 
> - Сначала создайте WAF версии 2 экземпляра Шлюза приложений, а затем следуйте инструкциям по [включению надстройки AGIC с существующим кластером AKS и экземпляром Шлюза приложений](tutorial-ingress-controller-add-on-existing.md). 

В следующем примере вы развернете новый кластер AKS с именем *myCluster* с помощью [Azure CNI](https://docs.microsoft.com/azure/aks/concepts-network#azure-cni-advanced-networking) и [управляемых удостоверений](https://docs.microsoft.com/azure/aks/use-managed-identity). Надстройка AGIC будет включена в созданной вами группе ресурсов *myResourceGroup*. 

Развертывание нового кластера AKS с включенной надстройкой AGIC без указания существующего экземпляра Шлюза приложений приведет к автоматическому созданию экземпляра SKU уровня "Стандартный" версии 2 экземпляра Шлюза приложений. Поэтому потребуется указать имя и диапазон адресов подсети экземпляра Шлюза приложений. Имя экземпляра Шлюза приложений будет *myApplicationGateway*, а используемый диапазон адресов подсети — 10.2.0.0/16. Убедитесь, что вы добавили или обновили расширение aks-preview в начале этого учебника. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" 
```

Информацию для настройки дополнительных параметров команды `az aks create` см. по [этим ссылкам](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create). 

> [!NOTE]
> Созданный вами кластер AKS отобразится в группе ресурсов *myResourceGroup*. Но автоматически созданный экземпляр Шлюза приложений будет находиться в группе ресурсов узла, в котором находятся пулы агентов. По умолчанию группа ресурсов узла имеет имя *MC_resource-group-name_cluster-name_location* (можно изменить). 

## <a name="deploy-a-sample-application-by-using-agic"></a>Развертывание примера приложения с помощью AGIC

Теперь вы можете развернуть пример приложения в созданном кластере AKS. Приложение использует надстройку AGIC для ingress и подключит экземпляр Шлюза приложений к кластеру AKS. 

Сначала получите учетные данные для кластера AKS с помощью команды `az aks get-credentials`: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Теперь, когда у вас есть учетные данные, выполните указанную ниже команду, чтобы настроить пример приложения, использующего AGIC для ingress в кластере. AGIC обновит экземпляр Шлюза приложений, настроенный вами ранее с соответствующими правилами маршрутизации для нового развертываемого примера приложения.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Проверка доступности приложения

Теперь, когда экземпляр Шлюза приложений настроен для обслуживания трафика в кластере AKS, давайте проверим доступность приложения. Сначала получите IP-адрес объекта ingress: 

```azurecli-interactive
kubectl get ingress
```

Убедитесь, что созданный вами пример приложения запускается одним из следующих способов:

- Используя IP-адрес экземпляра Шлюза приложений, полученного при выполнении предыдущей команды.
- При помощи `curl`. 

Для обновления Шлюзу приложений может потребоваться около минуты. Если Шлюз приложений на портале по-прежнему находится в состоянии **Обновление**, дождитесь окончания процедуры, прежде чем подключаться к IP-адресу. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, экземпляр Шлюза приложений и все связанные ресурсы, если они больше не нужны:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Сведения об отключении надстройки AGIC](./ingress-controller-disable-addon.md)

