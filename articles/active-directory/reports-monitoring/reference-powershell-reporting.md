---
title: Командлеты Azure AD PowerShell для создания отчетов | Документация Майкрософт
description: Справочник по командлетам Azure AD PowerShell для создания отчетов.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 372041dc24db4ed66977364e2a2997c7195cca9c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988033"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Командлеты Azure AD PowerShell для создания отчетов

С помощью отчетов Azure Active Directory (Azure AD) можно получить сведения о действиях, связанных со всеми операциями записи в вашем направлении (журналы аудита) и данными проверки подлинности (журналы входа). Хотя сведения доступны с помощью MS API Graph, теперь вы можете получить те же данные с помощью командлетов Azure AD PowerShell для создания отчетов.

В этой статье приводятся общие сведения о командлетах PowerShell, используемых для журналов аудита и журналов входа.

## <a name="audit-logs"></a>Журналы аудита

[Журналы аудита](concept-audit-logs.md) обеспечивают трассировку с помощью журналов для всех изменений, выполненных различными функциями в Azure AD. Примеры журналов аудита включают изменения, внесенные в такие ресурсы в Azure AD, как добавление или удаление пользователей, приложений, групп, ролей и политик.

Получить доступ к журналам аудита можно с помощью командлета Get-Азуреадаудитдиректорилогс.


| Сценарий                      | Команда PowerShell |
| :--                           | :--                |
| Отображаемое имя приложения      | Get-Азуреадаудитдиректорилогс — Filter "Инитиатедби/App/displayName EQ" облачная синхронизация Azure AD " |
| Категория                      | Get-Азуреадаудитдиректорилогс — фильтрация "Category EQ" Управление приложениями "" |
| Дата и время действия            | Get-Азуреадаудитдиректорилогс-Filter "Активитидатетиме gt 2019-04-18" |
| все вышеперечисленное.              | Get-Азуреадаудитдиректорилогс-Filter "Инитиатедби/App/displayName EQ" облачная синхронизация Azure AD и Категория EQ "Управление приложениями" и Активитидатетиме gt 2019-04-18 "|


На следующем рисунке показан пример для этой команды. 

![Кнопка "Data Summary" (Сводка данных)](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Журналы входа

Журналы [входа](concept-sign-ins.md) предоставляют сведения об использовании управляемых приложений и действий входа пользователя.

Получить доступ к журналам входа можно с помощью командлета Get-Азуреадаудитсигнинлогс.


| Сценарий                      | Команда PowerShell |
| :--                           | :--                |
| Отображаемое имя пользователя             | Get-Азуреадаудитсигнинлогс-Filter "userDisplayName EQ" Тимоти Перкинс "" |
| Создать дату и время              | Get-Азуреадаудитсигнинлогс-Filter "Креатеддатетиме gt 2019-04-18T17:30:00.0 Z" (все, начиная с 5:30 PM на 4/18) |
| Сообщение о состоянии                        | Get-Азуреадаудитсигнинлогс — Filter "Status/errorCode EQ 50105" |
| Отображаемое имя приложения      | Get-Азуреадаудитсигнинлогс-Filter «appDisplayName EQ ' Сторефронтстудио [WSFED Enabled] '» |
| все вышеперечисленное.              | Get-Азуреадаудитсигнинлогс-Filter "userDisplayName EQ" Тимоти Перкинс "and Status/errorCode Ne 0 и appDisplayName EQ" Сторефронтстудио [WSFED Enabled] "" |


На следующем рисунке показан пример для этой команды. 

![Кнопка "Data Summary" (Сводка данных)](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Следующие шаги

- [Что такое отчеты в Azure Active Directory](overview-reports.md).
- [Отчет о журналах аудита](concept-audit-logs.md). 
- [Программный доступ к отчетам Azure Active Directory](concept-reporting-api.md).
