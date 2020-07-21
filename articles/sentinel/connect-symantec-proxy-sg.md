---
title: Подключение данных Symantec Проксисг к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Symantec Проксисг к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 51a5c5de7bce07aa6a54b9ff81e957c38cfffdd2
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532028"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>Подключение Symantec Проксисг к Azure Sentinel

В этой статье объясняется, как подключить устройство [Symantec проксисг](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) к Azure Sentinel. Соединитель данных Symantec Проксисг позволяет легко подключать журналы Symantec Проксисг с помощью Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. При интеграции между Symantec Проксисг и Sentinel a Azure используется системный журнал.

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>Пересылка журналов Symantec Проксисг в агент системного журнала  

Настройте Symantec Проксисг для пересылки сообщений Syslog в рабочую область Azure с помощью агента системного журнала.

1. На портале Sentinel Azure щелкните **соединители данных** и выберите **Symantec проксисг** Connector.

1. Выберите **открыть страницу соединителя**.

1. Следуйте инструкциям на странице **Symantec проксисг** .

## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные отображаются в Log Analytics в разделе syslog.

## <a name="validate-connectivity"></a>Проверка подключения

Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить Symantec Проксисг к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.