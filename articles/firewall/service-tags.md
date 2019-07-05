---
title: Общие сведения о тегах службы Брандмауэра Azure
description: Эта статья содержит общие сведения о тегах службы Брандмауэра Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450167"
---
# <a name="azure-firewall-service-tags"></a>Теги службы Брандмауэра Azure

Тег службы представляет группу префиксов IP-адресов, чтобы упростить создание правила безопасности. Нельзя создать собственный тег службы или задать IP-адреса, которые будут входить в тег. Корпорация Майкрософт управляет префиксами адресов, входящих в тег службы, и автоматически обновляет этот тег при изменении адресов.

Теги службы Брандмауэра Azure можно использовать в поле назначения правил сети. Вы можете их использовать вместо определенных IP-адресов.

## <a name="supported-service-tags"></a>Поддерживаемые теги службы

См. в разделе [группы безопасности](../virtual-network/security-overview.md#service-tags) список теги служб, которые доступны для использования в правилах брандмауэра Azure сети.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о правилах Брандмауэра Azure см. в статье [Логика обработки правил Брандмауэра Azure](rule-processing.md).