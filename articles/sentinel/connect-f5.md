---
title: Подключение данных F5 к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные F5 к Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: cf6dc6977ff066b646beac4db5562ae8d97ab066
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610562"
---
# <a name="connect-f5-to-azure-sentinel"></a>Подключение F5 к Azure Sentinel

В этой статье объясняется, как подключить устройство F5 к Azure Sentinel. Соединитель данных F5 позволяет легко подключать журналы F5 к Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследование. С помощью клавиши F5 в Azure Sentinel вы получите более подробные сведения об использовании Интернета в вашей организации и улучшите возможности ее работы. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Настройка F5 для отправки сообщений CEF

1. Перейдите в раздел [F5 Настройка ведения журнала событий безопасности приложения](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)и следуйте инструкциям по настройке удаленного ведения журналов, следуя приведенным ниже рекомендациям.
   - Задайте для **типа удаленного хранилища** значение **CEF**.
   - Задайте **протокол** **TCP**.
   - Задайте **IP-адрес** для IP-адреса сервера системного журнала.
   - Задайте **номер порта** **514**или порт, который будет использоваться агентом.
   - Можно задать **максимальный размер строки запроса** , равный размеру, заданному в агенте.

1. Чтобы использовать соответствующую схему в Log Analytics для событий CEF, выполните поиск по запросу `CommonSecurityLog`.

1. Перейдите к [шагу 3. Проверка подключения](connect-cef-verify.md).


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить метку F5 к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте книги](tutorial-monitor-your-data.md) для отслеживания данных.

