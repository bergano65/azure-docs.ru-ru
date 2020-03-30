---
title: Подключение данных веб-приложений брандмауэра к Azure Sentinel
description: Узнайте, как подключить данные о брандмауэре веб-приложений Майкрософт к Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588184"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Подключение данных из брандмауэра веб-приложений Майкрософт



Вы можете передавать журналы из брандмауэра веб-приложений Microsoft (WAF) при общении с веб-приложением Azure Application Gateway. Этот WAF защищает ваши приложения от общих веб-уязвимостей, таких как инъекции S'L и кросс-сайт скриптов, и позволяет настроить правила, чтобы уменьшить ложные срабатывания. Следуйте этим инструкциям для потоковой передачи журналов брандмауэра веб-приложений Майкрософт в Azure Sentinel.


## <a name="prerequisites"></a>Предварительные требования

- Существующий ресурс шлюза приложений

## <a name="connect-to-microsoft-web-application-firewall"></a>Подключение к брандмауэру веб-приложений Майкрософт

Если у вас уже есть брандмауэр веб-приложений Майкрософт, убедитесь, что у вас есть существующий ресурс шлюза.
После развертывания брандмауэра веб-приложений Майкрософт и получения данных данные могут быть легко переданы в Azure Sentinel.
    
1. На портале Azure Sentinel выберите **разъемы данных.**
1. На странице разъемов данных выберите плитку **WAF.**
1. Перейдите на [ресурс](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) Application Gateway и выберите WAF.
    1. Выберите **Параметры диагностики**.
    1. Выберите **и добавьте диагностическую настройку** под столом.
    1. На странице **настройки диагностики** введите **имя** и выберите **Send to Log Analytics.**
    1. В **рабочей области аналитики журнала** выберите рабочее пространство Azure Sentinel.
    1. Выберите типы журналов, которые вы хотите проанализировать. Мы рекомендовали: ApplicationGatewayAccessLog и ApplicationGatewayFirewallLog.
1. Чтобы использовать соответствующую схему в журнале Analytics для предупреждений о брандмауэре веб-приложений Майкрософт, **ищите AzureDiagnostics.**

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить брандмауэр веб-приложений Майкрософт к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
