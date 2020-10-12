---
title: Connect Pulse Connect Secure Data to Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить Pulse Connect Secure Data to Azure Sentinel.
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
ms.openlocfilehash: f7f6de717f0ed9a2e27e15199b6f0ea566e0fdcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87021636"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Подключение к службе Pulse Connect Secure в Azure Sentinel

> [!IMPORTANT]
> Соединитель защищенных данных Pulse Connect в Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

В этой статье объясняется, как подключить устройство [Pulse Connect Secure](https://www.pulsesecure.net/products/pulse-connect-secure/) в Azure Sentinel. Соединитель защищенных данных Pulse Connect позволяет легко подключать Пульс подключения к безопасным журналам с помощью Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. Интеграция между Pulse Connect Secure и Azure Sentinel позволяет использовать системный журнал.

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Пересылка пульса подключение защищенных журналов к агенту системного журнала  

Настройте пульс Connect Secure для пересылки сообщений Syslog в рабочую область Azure с помощью агента системного журнала.

1. На портале Sentinel Azure щелкните **соединители данных** и выберите **импульс подключить безопасный** соединитель.

1. Выберите **открыть страницу соединителя**.

1. Выполните инструкции на странице **Pulse Connect Secure** .

## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные отображаются в Log Analytics в разделе syslog.

## <a name="validate-connectivity"></a>Проверка подключения

Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить Pulse Connect Secure к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.