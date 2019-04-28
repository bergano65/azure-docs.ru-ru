---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 74d7bd087df4b00c0bafb5ec33fbbdfa5c57b379
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390681"
---
Войдите в свою подписку Azure с помощью команды [az login](/cli/azure/) и следуйте инструкциям на экране. Дополнительные сведения о входе в систему см. в статье [Начало работы с Azure CLI](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Если у вас есть несколько подписок Azure, укажите подписки для этой учетной записи.

```azurecli
az account list --all
```

Укажите подписку, которую нужно использовать.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
