---
title: Подключение брандмауэра Barracuda Клауджен к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить Barracuda Клауджен Firewall к Azure Sentinel.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: cabailey
ms.openlocfilehash: 3d467026022a7bc90babd94827d64828f59ea714
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518129"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Подключение брандмауэра Barracuda Клауджен

Соединитель Barracuda Клауджен Firewall (КГФВ) позволяет легко подключать журналы Barracuda КГФВ к Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать расследования. Это позволяет получить более подробные сведения о сети организации и улучшить возможности обеспечения безопасности.




## <a name="prerequisites"></a>Технические условия

- Разрешения на чтение и запись в рабочей области "Sentinel" Azure.

- Брандмауэр Barracuda Клауджен должен быть настроен для экспорта журналов через системный журнал.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Подключение Sentinel Azure к Barracuda Клауджен Firewall

1. В портал Azure перейдите в раздел **Azure Sentinel** > **соединители данных** , а затем выберите соединитель **брандмауэра Barracuda клауджен** .

2. Выберите **открыть страницу соединителя**.

3. Следуйте инструкциям на странице **брандмауэра Barracuda клауджен** .


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить Barracuda Клауджен Firewall к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [получить представление о данных и потенциальных угрозах](quickstart-get-visibility.md).
- Приступая [к обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

