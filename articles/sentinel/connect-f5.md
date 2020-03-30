---
title: Подключение данных F5 к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные F5 к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
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
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588269"
---
# <a name="connect-f5-to-azure-sentinel"></a>Подключение F5 к Azure Sentinel

В этой статье объясняется, как подключить прибор F5 к Azure Sentinel. Разъем данных F5 позволяет легко подключать журналы F5 с Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследования. Использование F5 в Azure Sentinel предоставит вам более подробную информацию об использовании Интернета в вашей организации и повысит возможности ее работы в области безопасности. 

## <a name="configure-your-f5-to-send-cef-messages"></a>Налажить F5 для отправки сообщений CEF

1. Перейдите на [F5 Настройка безопасности приложений событий журналов](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html), и следовать инструкциям для настройки удаленной регистрации, используя следующие руководящие принципы:
   - Установите **тип удаленного хранилища** в **CEF.**
   - Установите **Протокол** на **TCP**.
   - Установите **IP-адрес** на IP-адрес сервера Syslog.
   - Установите **номер порта** до **514**или порта, который вы установите для использования агента.
   - Можно установить **максимальный размер строки запроса** до размера, установленного в агенте.

1. Чтобы использовать соответствующую схему в журнале Analytics для `CommonSecurityLog`событий CEF, ищите .

1. Продолжить [STEP 3: Проверка подключения](connect-cef-verify.md).


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить F5 к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.

