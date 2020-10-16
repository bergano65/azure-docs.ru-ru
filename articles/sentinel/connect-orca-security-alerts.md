---
title: Подключение оповещений системы безопасности Orca к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные оповещений системы безопасности Orca к Azure Sentinel, просмотреть панели мониторинга, создать настраиваемые оповещения и улучшить исследование.
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
ms.openlocfilehash: 735f4b447d7258a9b444f3b75a6537dec20b1307
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076267"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Подключение оповещений системы безопасности Orca к Azure Sentinel 

> [!IMPORTANT]
> Соединитель оповещений системы безопасности Orca в Azure Sentinel сейчас находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Соединитель оповещений системы безопасности Orca позволяет легко переносить оповещения о решении системы безопасности для [оповещений Orca](https://orca.security/) в Azure Sentinel, чтобы их можно было просматривать в книгах, использовать их для создания пользовательских оповещений и внедрять их для улучшения расследования. Интеграция оповещений системы безопасности Orca и Azure Sentinel позволяет использовать REST API.

> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="configure-and-connect-orca-security-alerts"></a>Настройка и подключение оповещений системы безопасности Orca

Оповещения безопасности Orca могут интегрировать и экспортировать журналы непосредственно в Azure Sentinel.

1. На портале Sentinel Azure щелкните **соединители данных** и выберите пункт **оповещения системы безопасности Orca** , а затем **откройте страницу соединителя**.

2. https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integrationДля завершения интеграции с платформой Orca см. статью.

## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные отображаются в Log Analytics в разделе **CustomLogs** в таблице **OrcaAlerts_CL** .
Чтобы использовать соответствующую схему в Log Analytics для оповещений Orca, выполните поиск по запросу `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Проверка подключения
Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить оповещения безопасности Orca к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.

