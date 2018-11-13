---
title: Настройка CLI для сетки Azure Service Fabric | Документация Майкрософт
description: Сведения о настройке CLI для Сетки Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: tylermsft
ms.author: twhitney
ms.date: 07/26/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c30f4b9de279f8c02b7f6bc7fa7d9765972899b1
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50977437"
---
# <a name="set-up-the-service-fabric-mesh-cli"></a>Настройка CLI для Сетки Service Fabric
Интерфейс командной строки (CLI) вложенной службы "Сетка Service Fabric" требуется для развертывания и администрирования ресурсов в этой службе. 

В режиме предварительной версии CLI для Сетки Azure Service Fabric записывается как расширение для Azure CLI. Вы можете установить его в Azure Cloud Shell или локально в Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-service-fabric-mesh-cli-locally"></a>Установка интерфейса командной строки Сетки Azure Service Fabric локально
Если вы решили установить и использовать CLI локально, используйте Azure CLI версии 2.0.43 и выше. Чтобы узнать версию, выполните команду `az --version`. Если необходимо установить или обновить CLI, ознакомьтесь с [этим разделом][azure-cli-install].

Установите модуль расширения CLI для Сетки Azure Service Fabric. 

```azurecli-interactive
az extension add --name mesh
```

Чтобы обновить существующий модуль службы "Сетка Azure Service Fabric", выполните следующую команду.

```azurecli-interactive
az extension update --name mesh
```
## <a name="next-steps"></a>Дополнительная информация

Вы также можете настроить [среду разработки Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Найдите ответы на [часто задаваемые вопросы и способы решения распространенных проблем](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
