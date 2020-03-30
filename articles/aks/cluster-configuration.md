---
title: Конфигурация кластера в службах Azure Kubernetes (AKS)
description: Узнайте, как настроить кластер в службе Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479167"
---
# <a name="configure-an-aks-cluster"></a>Настройка кластера AKS.

В рамках создания кластера AKS может потребоваться настроить конфигурацию кластера в соответствии с вашими потребностями. В этой статье представлено несколько вариантов настройки кластера AKS.

## <a name="os-configuration-preview"></a>Конфигурация ОС (Предварительный просмотр)

AKS теперь поддерживает Ubuntu 18.04 в качестве операционной системы узлов (ОС) в предварительном просмотре. В период предварительного просмотра доступны Ubuntu 16.04 и Ubuntu 18.04.

Необходимо установить следующие ресурсы:

- Azure CLI, версия 2.2.0 или позже
- Расширение aks-preview 0.4.35

Для установки расширения aks-preview 0.4.35 или позже используйте следующие команды Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

Зарегистрируйте функцию: `UseCustomizedUbuntuPreview`

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

Это может занять несколько минут для статуса, чтобы показать, как **зарегистрировано**. Проверить статус регистрации можно с помощью команды [списка функций az:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

Когда статус отображается зарегистрированным, освежите регистрацию поставщика `Microsoft.ContainerService` ресурсов с помощью команды [регистра аз-провайдера:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Нанастройка кластера для использования Ubuntu 18.04 при создании кластера. Используйте `--aks-custom-headers` флаг, чтобы установить Ubuntu 18.04 в качестве ОС по умолчанию.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

Если вы хотите создать регулярный кластер Ubuntu 16.04, вы можете `--aks-custom-headers` сделать это, опустив пользовательский тег.

## <a name="custom-resource-group-name"></a>Пользовательское имя группы ресурсов

При развертывании кластера службы Azure Kubernetes в Azure для узлов работы создается вторая группа ресурсов. По умолчанию AKS назовет `MC_resourcegroupname_clustername_location`группу ресурсов узлов, но вы также можете уведомлять свое собственное имя.

Чтобы указать имя группы ресурсов, установите версию расширения Azure CLI aks-preview 0.3.2 или позже. Используя Azure CLI, `--node-resource-group` используйте `az aks create` параметр команды, чтобы указать пользовательское имя для группы ресурсов. Если для развертывания кластера AKS используется шаблон менеджера ресурсов Azure, можно `nodeResourceGroup` определить имя группы ресурсов с помощью свойства.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Второстепенная группа ресурсов автоматически создается поставщиком ресурсов Azure в собственной подписке. Обратите внимание, что можно указать имя группы пользовательских ресурсов только при создании кластера. 

Работая с группой ресурсов узлов, имейте в виду, что вы не можете:

- Укажите существующую группу ресурсов для группы ресурсов узлов.
- Укажите другую подписку для группы ресурсов узлов.
- Измените имя группы ресурсов узла после создания кластера.
- Укажите имена для управляемых ресурсов в группе ресурсов узлов.
- Изменять или удалять созданные Azure теги управляемых ресурсов в группе ресурсов узлов.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, `Kured` как использовать [для применения обновлений безопасности и ядра к узлам Linux](node-updates-kured.md) в кластере.
- Просмотрите [кластер Azure Kubernetes Service (AKS),](upgrade-cluster.md) чтобы узнать, как обновить кластер до последней версии Kubernetes.
- Ознакомьтесь со списком [часто задаваемых вопросов об АКС,](faq.md) чтобы найти ответы на некоторые распространенные вопросы AKS.