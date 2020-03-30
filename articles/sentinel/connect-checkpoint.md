---
title: Подключите данные контрольной точки к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные Check Point к Azure Sentinel.
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
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588422"
---
# <a name="connect-check-point-to-azure-sentinel"></a>Подключение контрольной точки к Azure Sentinel



В этой статье объясняется, как подключить прибор Check Point к Azure Sentinel. Разъем данных Check Point позволяет легко подключать журналы Check Point с Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследования. Использование Check Point на Azure Sentinel предоставит вам более подробную информацию об использовании Интернета в вашей организации и повысит возможности ее работы в области безопасности. 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>Переадресованные журналы Check Point агенту Syslog

Нанастройка прибора Check Point для переадресовывания сообщений Syslog в формате CEF в рабочее пространство Azure через агента Syslog.

1. Перейти к [Экспорт журнала чек-пойнт](https://aka.ms/asi-syslog-checkpoint-forwarding).
1. Прокрутите вниз до **Basic Deployment** и следуйте инструкциям по настройке соединения, используя следующие рекомендации:
   - Установите **порт Syslog** до **514** или порт, который вы установили на агенте.
     - Замените **имя** и **IP-адрес целевого сервера** в CLI именем агента Syslog и IP-адресом.
     - Установите формат **CEF.**
1. Если вы используете версию R77.30 или R80.10, прокрутите до **установки** и следуйте инструкциям по установке журнала Экспортер для вашей версии.
1. Продолжить [STEP 3: Проверка подключения](connect-cef-verify.md).
 

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить приборы Check Point к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- [Проверка подключения.](connect-cef-verify.md)
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.


