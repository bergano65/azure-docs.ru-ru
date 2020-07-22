---
title: Подключение данных операционной системы Инфоблокс Network Identity (Ниос) к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные операционной системы удостоверений Инфоблокс Networking System (Ниос) к Azure Sentinel.
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
ms.openlocfilehash: ed4f2d769dbda3dec7b353fddfd1e5e0f3d00f9b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532173"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Подключение Инфоблокс Ниос к Azure Sentinel

В этой статье описывается подключение [устройства Инфоблокс Networking System (Ниос)](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) к Azure Sentinel. Соединитель данных Инфоблокс Ниос позволяет легко подключать журналы Инфоблокс к Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. При интеграции между Инфоблокс Ниос и Sentinel Azure используется системный журнал.

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Пересылка журналов Инфоблокс в агент системного журнала  

Настройте Инфоблокс для пересылки сообщений системного журнала в рабочую область Azure с помощью агента системного журнала.

1. На портале Sentinel Azure щелкните **соединители данных** и выберите **инфоблокс Ниос** Connector.

1. Выберите **открыть страницу соединителя**.

1. Следуйте инструкциям на странице **ИНФОБЛОКС Ниос** .

## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные отображаются в Log Analytics в разделе syslog.

## <a name="validate-connectivity"></a>Проверка подключения

Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить Инфоблокс Ниос к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.