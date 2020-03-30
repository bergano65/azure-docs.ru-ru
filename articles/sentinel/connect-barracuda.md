---
title: Подключите данные Barracuda к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные Barracuda к Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588490"
---
# <a name="connect-your-barracuda-appliance"></a>Подключите прибор Barracuda 



Разъем Barracuda Web Application Firewall (WAF) позволяет легко подключать журналы Barracuda к вашим Azure Sentinel, просматривать панели мониторинга, создавать пользовательские оповещения и улучшать исследования. Это дает вам более глубокое представление о сети вашей организации и улучшает возможности работы службы безопасности. Azure Sentinel использует преимущества интеграции между **агентом Barracuda** и Log Analytics для обеспечения беспрепятственной интеграции. 


> [!NOTE]
> Данные будут храниться в географическом местоположении рабочего пространства, на котором работает Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Напланить и подключить Barracuda WAF
Брандмауэр Web Application Firewall Barracuda может интегрировать и экспортировать журналы непосредственно в Azure Sentinel через агента Log Analytics.
1. Перейдите к [потоку конфигурации Barracuda WAF](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)и следуйте инструкциям по настройке соединения, используя следующие параметры:
    - **Идентификатор рабочего пространства:** копирование значения идентификатора рабочего пространства со страницы разъема Azure Sentinel Barracuda.
    - **Основной ключ**: скопируйте значение основного ключа со страницы разъема Azure Sentinel Barracuda.
1. Чтобы использовать соответствующую схему в журнале Analytics для событий Barracuda, поиск **CommonSecurityLog** и **barracuda_CL**.


## <a name="validate-connectivity"></a>Проверка подключения

Это может занять более 20 минут, пока ваши журналы начинают появляться в журнале Analytics. 



## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить приборы Barracuda к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте трудовые книжки](tutorial-monitor-your-data.md) для мониторинга данных.


