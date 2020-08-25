---
title: Командлеты Azure AD PowerShell для создания отчетов | Документация Майкрософт
description: Справочник по командлетам Azure AD PowerShell для создания отчетов.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a198a63d633573ad683a3f8e8215b2975721bc9
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794926"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Командлеты Azure AD PowerShell для создания отчетов

> [!NOTE] 
> Сейчас эти командлеты PowerShell работают только с модулем [предварительной версии Azure AD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) . Обратите внимание, что модуль предварительной версии не предлагается для использования в рабочей среде. 

Чтобы установить общедоступный предварительный выпуск, используйте следующую версию. 

```powershell
Install-module AzureADPreview
```

Дополнительные сведения о подключении к Azure AD с помощью PowerShell см. в статье [Azure AD PowerShell для Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

С помощью отчетов Azure Active Directory (Azure AD) можно получить сведения о действиях, связанных со всеми операциями записи в вашем направлении (журналы аудита) и данными проверки подлинности (журналы входа). Хотя сведения доступны с помощью MS API Graph, теперь вы можете получить те же данные с помощью командлетов Azure AD PowerShell для создания отчетов.

В этой статье приводятся общие сведения о командлетах PowerShell, используемых для журналов аудита и журналов входа.

## <a name="audit-logs"></a>Журналы аудита

[Журналы аудита](concept-audit-logs.md) обеспечивают трассировку с помощью журналов для всех изменений, выполненных различными функциями в Azure AD. Примеры журналов аудита включают изменения, вносимые в любые ресурсы в Azure AD, например добавление или удаление пользователей, приложений, групп, ролей и политик.

Получить доступ к журналам аудита можно с помощью командлета Get-Азуреадаудитдиректорилогс.


| Сценарий                      | Команда PowerShell |
| :--                           | :--                |
| Отображаемое имя приложения      | Get-Азуреадаудитдиректорилогс — Filter "Инитиатедби/App/displayName EQ" облачная синхронизация Azure AD " |
| Категория                      | Get-Азуреадаудитдиректорилогс — фильтр "Category EQ" Аппликатионманажемент "" |
| Дата и время действия            | Get-Азуреадаудитдиректорилогс-Filter "Активитидатетиме gt 2019-04-18" |
| Все вышеперечисленное              | Get-Азуреадаудитдиректорилогс-Filter "Инитиатедби/App/displayName EQ" облачная синхронизация Azure AD "и Категория EQ" Аппликатионманажемент "и Активитидатетиме gt 2019-04-18"|


На следующем рисунке показан пример для этой команды. 

![Кнопка "Data Summary" (Сводка данных)](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Журналы входа

Журналы [входа](concept-sign-ins.md) предоставляют сведения об использовании управляемых приложений и действий входа пользователя.

Получить доступ к журналам входа можно с помощью командлета Get-Азуреадаудитсигнинлогс.


| Сценарий                      | Команда PowerShell |
| :--                           | :--                |
| Отображаемое имя пользователя             | Get-Азуреадаудитсигнинлогс-Filter "userDisplayName EQ" Тимоти Перкинс "" |
| Создать дату и время              | Get-Азуреадаудитсигнинлогс-Filter "Креатеддатетиме gt 2019-04-18T17:30:00.0 Z" (все, начиная с 5:30 PM на 4/18) |
| Состояние                        | Get-Азуреадаудитсигнинлогс — Filter "Status/errorCode EQ 50105" |
| Отображаемое имя приложения      | Get-Азуреадаудитсигнинлогс-Filter «appDisplayName EQ ' Сторефронтстудио [WSFED Enabled] '» |
| Все вышеперечисленное              | Get-Азуреадаудитсигнинлогс-Filter "userDisplayName EQ" Тимоти Перкинс "and Status/errorCode Ne 0 и appDisplayName EQ" Сторефронтстудио [WSFED Enabled] "" |


На следующем рисунке показан пример для этой команды. 

![Кнопка "Data Summary" (Сводка данных)](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Дальнейшие действия

- [Что такое отчеты в Azure Active Directory](overview-reports.md).
- [Отчет о журналах аудита](concept-audit-logs.md). 
- [Программный доступ к отчетам Azure Active Directory](concept-reporting-api.md).
