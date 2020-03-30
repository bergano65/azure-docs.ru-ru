---
title: Подключите данные Office 365 к Azure Sentinel Документы Майкрософт
description: Узнайте, как подключить данные Office 365 к Azure Sentinel.
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
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252511"
---
# <a name="connect-data-from-office-365-logs"></a>Подключение данных из Office 365 Журналы



Вы можете передавать журналы аудита из [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) в Azure Sentinel одним щелчком мыши. Вы можете передавать журналы аудита из Office 365 в рабочее пространство Azure Sentinel на одном и том же арендаторе. Разъем журнала активности Office 365 позволяет получить представление о текущей деятельности пользователей. Вы получите информацию о различных действиях пользователя, админа, системы и политики и событиях от Office 365. Подключив журналы Office 365 в Azure Sentinel, вы можете использовать эти данные для просмотра панелей мониторинга, создания пользовательских оповещений и улучшения процесса исследования.

> [!IMPORTANT]
> Если у вас есть лицензия E3, прежде чем вы сможете получить доступ к данным через API управленческой деятельности Office 365, необходимо включить единую систему аудита для организации Office 365. Вы делаете это, включив журнал аудита Office 365. Для получения инструкций смотрите [поиск журнала Turn Office 365 в журнале аудита.](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off) Для получения дополнительной информации [см.](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)

## <a name="prerequisites"></a>Предварительные требования

- Вы должны быть глобальным администратором или администратором безопасности в ашего арендатора.
- Ваш арендатор должен иметь возможность унифицированного аудита. Арендаторы с лицензиями Office 365 E3 или E5 имеют единый аудит, включенный по умолчанию. <br>Если у арендатора нет одной из этих лицензий, необходимо включить единый аудит арендатора с помощью одного из этих методов:
    - [Использование Set-AdminAuditLogLogConfig cmdlet](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) и включить параметр "UnifiedAuditLogIngingestionEnabled").
    - [Использование uI центра безопасности & соответствия](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)требованиям.

## <a name="connect-to-office-365"></a>Подключение к Office 365

1. В Azure Sentinel выберите **разъемы данных,** а затем щелкните плитку **Office 365.**

2. Если вы еще не включили его, вы можете сделать это, перейдя к лезвию **разъема Data Connectors** и выбрав разъем **Office 365.** Здесь вы можете нажать на **страницу Open Connector** и под разделом конфигурации с пометкой **Конфигурация** выбрать все журналы действий Office 365, которые вы хотите подключить к Azure Sentinel. 
   > [!NOTE]
   > Если вы уже подключили несколько клиентов к ранее поддерживаемой версии разъема Office 365 в Azure Sentinel, вы сможете просматривать и изменять те журналы, которые вы собираете с каждого клиента. Вы не сможете добавить дополнительных арендаторов, но вы можете удалить ранее добавленные арендаторы.
3. Чтобы использовать соответствующую схему в журнале Analytics для журналов Office 365, поиск **OfficeActivity**.


## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить Office 365 к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

