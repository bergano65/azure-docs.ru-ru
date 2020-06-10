---
title: Настройка кластера в Службе Azure Kubernetes (AKS)
description: Сведения о том, как настроить кластер в Службе Kubernetes Azure (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725152"
---
# <a name="configure-an-aks-cluster"></a>Настройка кластера AKS.

При создании кластера AKS, возможно, потребуется изменить его конфигурацию для выполнения определенных задач. В этой статье описаны некоторые возможности персонализации кластера AKS.

## <a name="os-configuration-preview"></a>Конфигурация ОС (предварительная версия)

Теперь AKS поддерживает Ubuntu 18.04 в качестве операционной системы узла (предварительная версия). В период действия предварительной версии доступны Ubuntu 16.04 и Ubuntu 18.04.

Нужно установить следующие ресурсы:

- Azure CLI 2.2.0 или более поздней версии;
- расширение aks-preview 0.4.35.

Чтобы установить расширение aks-preview 0.4.35 или более поздней версии, выполните следующие команды Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Зарегистрируйте компонент `UseCustomizedUbuntuPreview`:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Состояние **Registered** (Зарегистрировано) может появиться через несколько минут. Состояние регистрации можно проверить с помощью команды [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list):

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Когда отобразится правильный статус, обновите регистрацию поставщика ресурсов `Microsoft.ContainerService` с помощью команды [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register):

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>Новые кластеры

При создании кластера выберите для него ОС Ubuntu 18.04. Установите флаг `--aks-custom-headers`, чтобы назначить Ubuntu 18.04 операционной системой по умолчанию.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Если вы предпочитаете обычный кластер Ubuntu 16.04, просто не указывайте пользовательский тег `--aks-custom-headers`.

### <a name="existing-clusters"></a>Существующие кластеры

Настройте для нового пула использование Ubuntu 18.04. Установите флаг `--aks-custom-headers`, чтобы назначить Ubuntu 18.04 операционной системой по умолчанию для этого пула узлов.

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Если вы предпочитаете обычный пул узлов Ubuntu 16.04, просто не указывайте пользовательский тег `--aks-custom-headers`.


## <a name="custom-resource-group-name"></a>Пользовательское имя группы ресурсов

При развертывании кластера Azure Kubernetes Service в Azure создается дополнительная группа ресурсов для рабочих узлов. По умолчанию AKS присваивает группе ресурсов для узлов имя `MC_resourcegroupname_clustername_location`. Но вы можете изменить его произвольным образом.

Чтобы указать собственное имя для группы ресурсов, установите для Azure CLI расширение aks-preview 0.3.2 или более поздней версии. В Azure CLI укажите параметр `--node-resource-group` при выполнении команды `az aks create`, чтобы указать пользовательское имя для группы ресурсов. Если вы применяете шаблон Azure Resource Manager для развертывания кластера AKS, группу ресурсов можно определить с помощью свойства `nodeResourceGroup`.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Дополнительную группу ресурсов автоматически создает поставщик ресурсов Azure в вашей подписке. Вы можете указать пользовательское имя для группы ресурсов только при создании кластера. 

При работе с группой ресурсов для узлов учитывайте, что нельзя:

- указать существующую группу ресурсов для узлов;
- поместить узлы в группу ресурсов в другой подписке;
- изменить имя группы ресурсов для узлов после создания кластера;
- выбрать имена управляемых ресурсов в группе ресурсов для узлов;
- изменять или удалять созданные Azure теги управляемых ресурсов в группе ресурсов для узлов.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как с помощью `Kured` [применять обновления для системы безопасности и ядра для узлов Linux](node-updates-kured.md) в кластере.
- Сведения о том, как обновить кластер до последней версии Kubernetes, см. в статье [Обновление кластера Службы Azure Kubernetes (AKS)](upgrade-cluster.md).
- В списке [вопросов и ответов об AKS](faq.md) собраны самые популярные рекомендации.