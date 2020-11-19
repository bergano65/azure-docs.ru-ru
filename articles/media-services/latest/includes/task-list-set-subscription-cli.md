---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: 94df457cc93a059de3f9bb294bd1c568b095781c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94884942"
---
<!-- List and set subscriptions -->

1. Получите список своих подписок с помощью команды [az account list](/cli/azure/account#az-account-list):

    ```
    az account list --output table
    ```

2. Выполните команду `az account set`, указав идентификатор подписки или имя, на которое нужно переключиться.

    ```
    az account set --subscription "My Demos"
    ```
