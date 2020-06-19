---
title: Подключение данных Office 365 к Azure Sentinel | Документация Майкрософт
description: Узнайте, как подключить данные Office 365 к Azure Sentinel.
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
ms.openlocfilehash: c3e63063b3ea4e7fba3997ddd645aa59fe857488
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758577"
---
# <a name="connect-data-from-office-365-logs"></a>Подключение данных из журналов Office 365



Можно выполнять потоковую передачу журналов аудита из [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) в Azure Sentinel одним щелчком мыши. Можно выполнять потоковую передачу журналов аудита из Office 365 в рабочую область Sentinel Azure в том же арендаторе. Соединитель журнала действий Office 365 предоставляет информацию о текущих действиях пользователей. Предоставляются сведения о событиях и действиях разных пользователей, администраторов, системы и политик из журналов действий Office 365. Подключив журналы Office 365 к Azure Sentinel, вы сможете использовать эти данные для просмотра панелей мониторинга, создания настраиваемых оповещений и улучшения процесса расследования.

> [!IMPORTANT]
> Если у вас есть лицензия E3, прежде чем получить доступ к данным через API действий управления Office 365, необходимо включить унифицированное ведение журнала аудита для организации Office 365. Для этого необходимо включить журнал аудита Office 365. Инструкции см. в разделе [Включение или отключение поиска по журналам аудита Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Дополнительные сведения см. в справочнике по [API действий управления Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Предварительные требования

- Необходимо иметь права глобального администратора или администратора безопасности на вашем арендаторе.
- Для арендатора должен быть включен унифицированный аудит. Для арендаторов с лицензиями Office 365 E3 или E5 по умолчанию включен унифицированный аудит. <br>Если у арендатора нет одной из этих лицензий, для включения унифицированного аудита на арендаторе используется один из следующих методов.
    - [Использование командлета Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) и включение параметра UnifiedAuditLogIngestionEnabled).
    - [Использование интерфейса пользователя Центра безопасности и соответствия требованиям Office 365](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).
   
   > [!NOTE]
   > В настоящее время соединитель данных O365 автоматически записывает действия Exchange и SharePoint, как описано на странице соединителя в разделе "Типы данных". Рекомендуется ознакомиться с [этой статьей, если вам нужны данные аудита Teams и необходимо обеспечить защиту Teams с помощью Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761). 

## <a name="connect-to-office-365"></a>Подключение к Office 365

1. В Azure Sentinel выберите **Соединители данных**, а затем щелкните элемент **Office 365**.

2. Если вы еще не включили эту функцию, перейдите в колонку **Соединители данных** и выберите соединитель **Office 365**. Здесь можно щелкнуть **страницу "Открыть соединитель"** и выбрать в разделе конфигурации с меткой **Конфигурация** все журналы действий Office 365, которые необходимо подключить к Azure Sentinel. 
   > [!NOTE]
   > Если вы уже подключили несколько арендаторов к поддерживаемой ранее версии соединителя Office 365 в Azure Sentinel, вы сможете просматривать и изменять журналы, полученные от каждого арендатора. Добавление дополнительных арендаторов не предусмотрено, но можно удалить ранее добавленные арендаторы.
3. Чтобы использовать соответствующую схему при анализе журналов Office 365, выполните поиск **OfficeActivity**.


## <a name="next-steps"></a>Дальнейшие действия
Этот документ описывает подключение Office 365 к Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

