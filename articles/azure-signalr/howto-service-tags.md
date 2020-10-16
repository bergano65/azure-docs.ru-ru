---
title: Использование тегов служб
titleSuffix: Azure SignalR Service
description: Использование тегов службы для разрешения исходящего трафика в службу Azure SignalR
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: 8810309fef5dbbb35465a2af15d42fa8a59d5401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84302106"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Использование тегов службы для службы SignalR Azure

При настройке [группы безопасности сети](../virtual-network/security-overview.md#network-security-groups)можно использовать [теги службы](../virtual-network/security-overview.md#service-tags) для службы SignalR Azure. Он позволяет определить исходящее правило сетевой безопасности для конечных точек службы SignalR Azure, не требуя жестко определять IP-адреса.

Служба Azure SignalR управляет этими тегами службы. Нельзя создать собственный тег службы или изменить существующий. Корпорация Майкрософт управляет такими префиксами адресов, которые соответствуют тегу службы, и автоматически обновляет тег службы при изменении адресов.

## <a name="use-service-tag-on-portal"></a>Использовать тег службы на портале

Вы можете разрешить исходящий трафик в службу Azure SignalR, добавив новое правило сетевой безопасности для исходящей сети:

1. Перейдите в группу безопасности сети.

1. Щелкните меню параметров, именуемое **правила безопасности для исходящего трафика**.

1. Нажмите кнопку **+ Добавить** в верхней части страницы.

1. В разделе **назначение**выберите **тег службы** .

1. Выберите **азуресигналр** в разделе **тег целевой службы**.

1. Введите **443** в **диапазоны портов назначения**.

    ![Создание правила безопасности для исходящего трафика](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Настройте другие поля в соответствии с вашими потребностями.

1. Нажмите кнопку **Добавить**.


## <a name="next-steps"></a>Дальнейшие действия

- [Группы безопасности сети: Теги служб](../virtual-network/security-overview.md#security-rules)
