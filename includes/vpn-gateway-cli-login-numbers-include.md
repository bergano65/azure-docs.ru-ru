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
ms.openlocfilehash: cf3bbb6c45061d6f4885839fbfb7f069264fe986
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444330"
---
1. Войдите в свою подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране. Дополнительные сведения о входе в систему см. в статье [Начало работы с Azure CLI](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Если у вас есть несколько подписок Azure, укажите подписки для этой учетной записи.

   ```azurecli
   az account list --all
   ```
3. Укажите подписку, которую нужно использовать.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
