---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 6fab0258b0c0e2f9b31358075a8f7a5be0228a5e
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095717"
---
[!INCLUDE [resource group intro text](resource-group.md)]

В Cloud Shell создайте группу ресурсов с помощью команды [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *Западная Европа*. Чтобы просмотреть все поддерживаемые расположения для службы приложений в Linux в ценовой категории **Базовый**, выполните команду [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Обычно группа ресурсов и ресурсы создаются в ближайших регионах. 

По завершении команды в выходных данных JSON будут отображаться свойства группы ресурсов.