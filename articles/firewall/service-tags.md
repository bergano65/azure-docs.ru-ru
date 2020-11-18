---
title: Общие сведения о тегах службы Брандмауэра Azure
description: Тег службы представляет группу префиксов IP-адресов, чтобы упростить создание правила безопасности.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 47377817b62d33e8af79e4a0d2dceb68ba9dbdc5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658653"
---
# <a name="azure-firewall-service-tags"></a>Теги службы Брандмауэра Azure

Тег службы представляет группу префиксов IP-адресов, чтобы упростить создание правила безопасности. Нельзя создать собственный тег службы или задать IP-адреса, которые будут входить в тег. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов.

Теги службы Брандмауэра Azure можно использовать в поле назначения правил сети. Вы можете их использовать вместо определенных IP-адресов.

## <a name="supported-service-tags"></a>Поддерживаемые теги службы

Список тегов служб, доступных для использования в правилах сети брандмауэра Azure, см. в разделе [группы безопасности](../virtual-network/network-security-groups-overview.md#service-tags) .

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о правилах Брандмауэра Azure см. в статье [Логика обработки правил Брандмауэра Azure](rule-processing.md).