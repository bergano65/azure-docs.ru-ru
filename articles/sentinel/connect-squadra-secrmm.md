---
title: Подключение скуадра Technologies Секрмм Data к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить скуадра Technologies Секрмм Data to Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588116"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Подключение данных Секрмм скуадра Technologies к Azure Sentinel 

> [!IMPORTANT]
> Соединитель данных скуадра Technologies Security Media Manager (Секрмм) в Azure Sentinel в настоящее время находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Соединитель скуадра Technologies Секрмм позволяет легко подключать ваши скуадра технологии Секрмм журналов решений безопасности с помощью Azure Sentinel. Она позволяет просматривать панели мониторинга, создавать пользовательские предупреждения и улучшать исследование. Этот соединитель позволяет получить представление о событиях съемных носителей USB. Интеграция между скуадра технологиями Секрмм и Azure Sentinel позволяет использовать REST API.


> [!NOTE]
> Данные будут храниться в географическом расположении рабочей области, на которой вы используете метку Azure.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Настройка и подключение технологий скуадра Секрмм 

Технологии скуадра Секрмм могут интегрировать и экспортировать журналы непосредственно в Azure Sentinel.
1. На портале Sentinel Azure щелкните соединители данных, выберите скуадра Technologiess Секрмм, а затем откройте страницу соединителя.

2. Выполните действия, описанные в руководстве по [адаптации технологий скуадра для Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) , чтобы получить данные скуадра Секрмм в Azure Sentinel.   


## <a name="find-your-data"></a>Поиск данных

После установки успешного подключения данные отображаются в Log Analytics в разделе CustomLogs secRMM_CL.
Чтобы использовать соответствующую схему в Log Analytics скуадра Technologies Секрмм, выполните поиск secRMM_CL.

## <a name="validate-connectivity"></a>Проверка подключения
Если журналы начнут появляться в Log Analytics, это может занять до 20 минут. 


## <a name="next-steps"></a>Дальнейшие шаги
В этом документе вы узнали, как подключить скуадра Technologies Секрмм к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.

