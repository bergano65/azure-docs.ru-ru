---
title: включение файла
description: включение файла
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: da4177fd54c0d8777f15175cea3a74a8b01c0954
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305324"
---
1. Убедитесь, что вы вошли в учетную запись Azure и используете подписку, которую необходимо подключить для этой предварительной версии. Используйте следующий пример для регистрации:

    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowBastionHost -ProviderNamespace Microsoft.Network
    ```
2.  Еще раз зарегистрировать подписку с *Microsoft.Network* пространство имен поставщика.

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ````
3. Используйте следующую команду, чтобы убедиться, что *AllowBastionHost* компонент зарегистрирован с вашей подпиской:

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.Network
    ````

    Может занять несколько минут для завершения регистрации.
