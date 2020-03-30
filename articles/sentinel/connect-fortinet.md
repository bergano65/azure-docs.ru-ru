---
title: Подключите данные Fortinet к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные Fortinet к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aabc95fcb8d3b32b89bb83f4f6892c3d40b03417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588201"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Подключите Fortinet к Azure Sentinel



В этой статье объясняется, как подключить прибор Fortinet к Azure Sentinel. Разъем данных Fortinet позволяет легко подключать журналы Fortinet к Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследования. Использование Fortinet на Azure Sentinel предоставит вам более подробную информацию об использовании Интернета в вашей организации и повысит возможности ее работы в области безопасности. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Переадресованные журналы Fortinet агенту Syslog

Нанастройка Fortinet для переадресовки сообщений Syslog в формате CEF в рабочее пространство Azure через агента Syslog.

1. Откройте CLI на вашем приборе Fortinet и запустите следующие команды:

        config log syslogd setting
        set format cef
        set port 514
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Замените **IP-адрес** сервера IP-адресом агента.
    - Установите **порт Syslog** до **514** или порт, установленный на агенте.
    - Для включения формата CEF в ранних версиях FortiOS может потребоваться выполнить набор команд **csv.**
 
   > [!NOTE] 
   > Для получения дополнительной информации, перейдите в [библиотеку документов Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Выберите версию и используйте **справочник** и **справочник по ссылке на журналы.**

1. Чтобы использовать соответствующую схему в аналитике журналов Azure Monitor `CommonSecurityLog`для событий Fortinet, ищите

1. Продолжить [STEP 3: Проверка подключения](connect-cef-verify.md).


## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы узнали, как подключить приборы Fortinet к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.


