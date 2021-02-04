---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99215460"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Настройка поставщика датчика отпечатков пальцев

Начнем с создания и настройки поставщика датчика отпечатков пальцев. Поставщик датчика отпечатка пальца позволит получить информацию с платформозависимых датчиков на вашем устройстве и конвертировать ее измерения в общее представление для сеанса пространственной привязки облака.

> [!IMPORTANT]
> Обязательно просмотрите [этот раздел](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability) и убедитесь, что датчики, которые вы включаете, доступны на вашей платформе.