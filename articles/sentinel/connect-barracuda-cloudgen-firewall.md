---
title: Подключение брандмауэра Barracuda Клауджен к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить Barracuda Клауджен Firewall к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: dd0115fa34b10805570b6e9d8d09ae96fc169dfc
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621137"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>Подключение Barracuda CloudGen Firewall

Соединитель Barracuda Клауджен Firewall (КГФВ) позволяет легко подключать журналы Barracuda КГФВ к Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать расследования. Это позволяет получить более подробные сведения о сети организации и улучшить возможности обеспечения безопасности.

## <a name="prerequisites"></a>Предварительные требования

- Разрешения на чтение и запись в рабочей области "Sentinel" Azure.

- Брандмауэр Barracuda Клауджен должен быть настроен для экспорта журналов через системный журнал.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Подключение Sentinel Azure к Barracuda Клауджен Firewall

1. В портал Azure перейдите к   >  **соединителям данных** Azure Sentinel, а затем выберите соединитель **брандмауэра Barracuda клауджен** .

2. Выберите **открыть страницу соединителя**.

3. Следуйте инструкциям на странице **брандмауэра Barracuda клауджен** .


## <a name="next-steps"></a>Следующие шаги
В этом документе вы узнали, как подключить Barracuda Клауджен Firewall к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.


