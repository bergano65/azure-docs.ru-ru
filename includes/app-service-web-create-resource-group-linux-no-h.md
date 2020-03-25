---
title: включить файл
description: включить файл
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c70e2c166bee14ac58ee88bd18baf0cc61702767
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2020
ms.locfileid: "67185761"
---
[!INCLUDE [resource group intro text](resource-group.md)]

В Cloud Shell создайте группу ресурсов с помощью команды [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *Западная Европа*. Чтобы просмотреть все поддерживаемые расположения для службы приложений в Linux в ценовой категории **Базовый**, выполните команду [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

Обычно группа ресурсов и ресурсы создаются в ближайших регионах. 

По завершении команды в выходных данных JSON будут отображаться свойства группы ресурсов.