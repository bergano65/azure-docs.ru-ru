---
title: Включить имя файла
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 74d7bd087df4b00c0bafb5ec33fbbdfa5c57b379
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67185127"
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
