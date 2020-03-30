---
title: Смлеты Azure AD PowerShell для отчетности Документы Майкрософт
description: Ссылка на cmdlets Azure AD PowerShell для отчетности.
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
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495311"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>Командлеты Azure AD PowerShell для создания отчетов

> [!NOTE] 
> В настоящее время эти cmdlets Powershell работают только с модулем [Azure AD Preview.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) Обратите внимание, что модуль предварительного просмотра не предлагается для производственного использования. 

Для установки публичного предварительного релиза используйте следующее. 

```powershell
Install-module AzureADPreview
```
Для получения дополнительной информации о том, как подключиться к Azure AD с помощью powershell, пожалуйста, смотрите статью [Azure AD Powershell для Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0).  

С помощью отчетов Azure Active Directory (Azure AD) вы можете получить подробную информацию о действиях по всем операциям записи в вашем направлении (журналы аудита) и данные аутентификации (журналы входа). Хотя эта информация доступна с помощью API MS Graph, теперь вы можете получить те же данные, используя cmdlets Azure AD PowerShell для отчетности.

В этой статье представлен обзор cmdlets PowerShell, которые можно использовать для журналов аудита и входа.

## <a name="audit-logs"></a>Журналы аудита

[Системы аудита](concept-audit-logs.md) обеспечивают прослеживаемость через журналы для всех изменений, внесенных различными функциями в Azure AD. Примеры журналов аудита включают изменения, внесенные в такие ресурсы в Azure AD, как добавление или удаление пользователей, приложений, групп, ролей и политик.

Вы получаете доступ к журналам аудита с помощью cmdlet 'Get-AzureADAuditAuditDirectoryLogs.


| Сценарий                      | Команда PowerShell |
| :--                           | :--                |
| Имя дисплея приложения      | Get-AzureADAuditAuditDirector-Фильтр "инициируетсяBy/app/displayName eq ' Azure AD Cloud Sync"" |
| Категория                      | Get-AzureADAuditAuditDirectoryLogs -Фильтр "категория eq 'Управление приложениями'" |
| Время действия Дата            | Get-AzureADAuditAuditDirectoryLogs -Фильтр "активностьДата времени 2019-04-18" |
| все вышеперечисленное.              | Get-AzureADAuditAuditDirectorYLogs -Фильтр "инициируетсяBy/app/displayName eq 'Azure AD Cloud Sync' и категория eq 'Управление приложениями' и activityDateTime gt 2019-04-18"|


Следующее изображение показывает пример для этой команды. 

![Кнопка "Data Summary" (Сводка данных)](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>Журналы входа

В [журналах входа](concept-sign-ins.md) предоставляется информация об использовании управляемых приложений и действиях пользователя.

Вы получаете доступ к журналам входа с помощью cmdlet 'Get-AzureADAuditSignInLogs.


| Сценарий                      | Команда PowerShell |
| :--                           | :--                |
| Отображаемое имя пользователя             | Get-AzureADAuditSignInLogs -Фильтр "userDisplayName eq 'Тимоти Перкинс'" |
| Создание Времени даты              | Get-AzureADAuditSignInLogs -Фильтр "созданDateTime GT 2019-04-18T17:30:00.0" (Все с 17:30 14/18) |
| Состояние                        | Get-AzureADAuditSignInLogs -Фильтр "статус / ошибкаКод eq 50105" |
| Имя дисплея приложения      | Get-AzureADAuditSignInLogs -Фильтр "appDisplayName eq 'StoreFrontStudio (включился) "" |
| все вышеперечисленное.              | Get-AzureADAuditSignInLogs -Фильтр "userDisplayName eq 'Тимоти Перкинс' и статус / ошибкаCode ne 0 и appDisplayName eq 'StoreFrontStudio (включился) "" |


Следующее изображение показывает пример для этой команды. 

![Кнопка "Data Summary" (Сводка данных)](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>Дальнейшие действия

- [Что такое отчеты в Azure Active Directory](overview-reports.md).
- [Отчет журналов аудита](concept-audit-logs.md). 
- [Программный доступ к отчетам Azure Active Directory](concept-reporting-api.md).
