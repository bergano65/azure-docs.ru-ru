---
title: Подключите данные Palo Alto Networks к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные Palo Alto Networks к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: a79b7a1448e1decb377aa0072261df068c366567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588133"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Соедините сети Пало-Альто в Azure Sentinel



В этой статье объясняется, как подключить прибор Palo Alto Networks к Azure Sentinel. Разъем данных Palo Alto Networks позволяет легко подключать журналы Palo Alto Networks с Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследования. Использование сетей Palo Alto на Azure Sentinel даст вам более подробную информацию об использовании Интернета вашей организацией и повысит возможности ее работы в области безопасности. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Форвард Пало-Альто Сети журналы для агента Syslog

Нанастройка сети Palo Alto для переадресовки сообщений Syslog в формате CEF в рабочее пространство Azure через агент Syslog:
1.  Перейдите к [общим справочнистам по формату событий (CEF)](https://docs.paloaltonetworks.com/resources/cef) и загрузите PDF для типа прибора. Следуйте всем инструкциям в руководстве, чтобы настроить ваш Palo Alto Сети прибор для сбора событий CEF. 

1.  Перейдите к [настройке мониторинга Syslog](https://aka.ms/asi-syslog-paloalto-forwarding) и выполните шаги 2 и 3, чтобы настроить переадресировку события CEF с прибора Palo Alto Networks в Azure Sentinel.

    1. Убедитесь в том, чтобы установить **формат сервера Syslog** для **BSD.**

       > [!NOTE]
       > Операции копирования/вставки из PDF могут изменить текст и вставить случайные символы. Чтобы избежать этого, скопируйте текст в редактор и удалите все символы, которые могут нарушить формат журнала перед его вставкой, как вы можете видеть в этом примере.
 
        ![Проблема копирования текста CEF](./media/connect-cef/paloalto-text-prob1.png)

1. Чтобы использовать соответствующую схему в журнале Analytics для событий Palo Alto Networks, поиск **CommonSecurityLog**.

1. Продолжить [STEP 3: Проверка подключения](connect-cef-verify.md).




## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить приборы Palo Alto Networks к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.


