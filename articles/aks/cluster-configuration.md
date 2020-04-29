---
title: Конфигурация кластера в Azure Kubernetes Services (AKS)
description: Узнайте, как настроить кластер в службе Kubernetes Azure (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479167"
---
# <a name="configure-an-aks-cluster"></a>Настройка кластера AKS.

В рамках создания кластера AKS может потребоваться настроить конфигурацию кластера в соответствии с вашими потребностями. В этой статье представлено несколько вариантов настройки кластера AKS.

## <a name="os-configuration-preview"></a>Конфигурация ОС (Предварительная версия)

AKS теперь поддерживает Ubuntu 18,04 как операционную систему узла (ОС) в предварительной версии. В течение периода действия предварительной версии доступны как Ubuntu 16,04, так и Ubuntu 18,04.

Необходимо установить следующие ресурсы:

- Azure CLI версии 2.2.0 или более поздней.
- Расширение AKS-Preview 0.4.35

Чтобы установить расширение AKS-Preview 0.4.35 или более позднюю версию, используйте следующие Azure CLI команды:

```azurecli
az extension add --name aks-preview
az extension list
```

Зарегистрируйте `UseCustomizedUbuntuPreview` функцию:

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Чтобы состояние отображалось как **зарегистрированное**, может потребоваться несколько минут. Проверить состояние регистрации можно с помощью команды [AZ Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) .

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Когда состояние отображается как зарегистрированное, обновите регистрацию поставщика `Microsoft.ContainerService` ресурсов с помощью команды [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Настройте кластер для использования Ubuntu 18,04 при создании кластера. Используйте `--aks-custom-headers` флаг, чтобы установить Ubuntu 18,04 в качестве ОС по умолчанию.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Если вы хотите создать обычный кластер Ubuntu 16,04, это можно сделать, опустив пользовательский `--aks-custom-headers` тег.

## <a name="custom-resource-group-name"></a>Имя пользовательской группы ресурсов

При развертывании кластера службы Kubernetes Azure в Azure для рабочих узлов создается вторая группа ресурсов. По умолчанию AKS будет указывать имя группы `MC_resourcegroupname_clustername_location`ресурсов узла, но можно также указать собственное имя.

Чтобы указать собственное имя группы ресурсов, установите расширение AKS-Preview Azure CLI версии 0.3.2 или более поздней. Используя Azure CLI, используйте `--node-resource-group` параметр `az aks create` команды, чтобы указать пользовательское имя для группы ресурсов. При использовании шаблона Azure Resource Manager для развертывания кластера AKS можно определить имя группы ресурсов с помощью `nodeResourceGroup` свойства.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Вторичная группа ресурсов автоматически создается поставщиком ресурсов Azure в собственной подписке. Обратите внимание, что при создании кластера можно указать только имя пользовательской группы ресурсов. 

При работе с группой ресурсов узла следует помнить, что вы не можете:

- Укажите существующую группу ресурсов для группы ресурсов узла.
- Укажите другую подписку для группы ресурсов узла.
- Измените имя группы ресурсов узла после создания кластера.
- Укажите имена управляемых ресурсов в группе ресурсов узла.
- Измените или удалите созданные Azure Теги управляемых ресурсов в группе ресурсов узла.

## <a name="next-steps"></a>Дальнейшие шаги

- Узнайте, как использовать `Kured` для [применения обновлений безопасности и ядра к узлам Linux](node-updates-kured.md) в кластере.
- Сведения о том, как обновить кластер до последней версии Kubernetes, см. в статье [Обновление кластера Azure Kubernetes Service (AKS)](upgrade-cluster.md) .
- Ознакомьтесь со списком [часто задаваемых вопросов о AKS](faq.md) , чтобы найти ответы на некоторые распространенные вопросы AKS.