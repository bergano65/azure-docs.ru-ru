---
title: Управление доступом к общедоступной сети для центра Интернета вещей Azure
description: Документация по отключению и включению доступа к общедоступной сети для центра Интернета вещей
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85937562"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Управление доступом к общедоступной сети для центра Интернета вещей

Чтобы ограничить доступ только к [частным конечным точкам центра Интернета вещей в виртуальной](virtual-network-support.md)сети, отключите доступ к общедоступной сети. Для этого используйте портал Azure или `publicNetworkAccess` API. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Отключение доступа к общедоступной сети с помощью портал Azure

1. Посетите [портал Azure](https://portal.azure.com)
2. Перейдите в Центр Интернета вещей.
3. В меню слева выберите **сеть** .
4. В разделе "разрешить доступ к общедоступной сети" выберите **отключено** .
5. Щелкните **Сохранить**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Изображение, показывающее портал Azure, где следует отключить доступ к общедоступной сети" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Чтобы включить доступ к общедоступной сети, выберите **включено**, а затем нажмите **сохранить**.

## <a name="ip-filter"></a>Фильтрация IP-адресов 

Если доступ к общедоступной сети отключен, все правила [IP-фильтра](iot-hub-ip-filtering.md) игнорируются. Это связано с тем, что все IP-адреса из общедоступного Интернета блокируются. Чтобы использовать IP-фильтр, используйте параметр **Выбранные диапазоны IP-адресов** .

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Исправление ошибки с встроенной конечной точкой, совместимой с концентратором событий

Существует ошибка в центре Интернета вещей, в которой [Встроенная совместимая с концентратором событий конечная точка](iot-hub-devguide-messages-read-builtin.md) по-своему доступна через общедоступный Интернет при отключенном доступе к центру Интернета вещей через общедоступную сеть. Дополнительные сведения и обращение к нам по поводу этой ошибки см. в разделе [Отключение доступа к общедоступной сети для центра Интернета вещей, отключение доступа к встроенной конечной точке концентратора событий](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).