---
title: Подключение данных системы управления Иллусиве атаковать к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные системы управления Иллусиве атаковать к Azure Sentinel.
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
ms.openlocfilehash: 7d2d3871dd9836e2c68155aa82ce01dced128bf3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532168"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Подключение системы управления Иллусиве атаковать к Azure Sentinel

В этой статье объясняется, как подключить [систему управления Иллусиве атаковать](https://www.illusivenetworks.com/technology/platform/attack-detection-system) к Azure Sentinel. Соединитель данных системы управления Иллусиве-атакой позволяет предоставлять доступ к данным анализа уязвимой зоны Иллусиве и к журналам инцидентов с помощью Sentinel Azure, а также просматривать эти сведения на выделенных панелях мониторинга, которые позволяют получить представление о риске уязвимой зоны Организации (панель мониторинга ASM) и контролировать несанкционированное бокового смещения перемещение в сети организации (панель мониторинга ADS).

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Пересылка журналов системы управления Иллусивеной атакой агенту системного журнала  

Настройте систему управления атаками для пересылки сообщений Syslog в формате CEF в рабочую область Azure с помощью агента системного журнала.

1. Войдите в консоль Иллусиве и перейдите к разделу Settings->Reporting.

1. Поиск системного журнала Серверсץ

1. Введите следующие сведения:
   - Имя узла: IP-адрес агента syslog Linux или имя узла с полным доменным именем
   - Порт: 514
   - Протокол: TCP
   - Сообщения аудита: отправка сообщений аудита на сервер

1. Чтобы добавить сервер syslog, нажмите кнопку Добавить.

1. Чтобы использовать соответствующую схему в Log Analytics системе управления атаками Иллусиве, выполните поиск CommonSecurityLog.

## <a name="next-steps"></a>Дальнейшие действия

В этом документе вы узнали, как подключить систему управления Иллусиве атаковать к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.
