---
title: Облачные приложения или действия в политике условного доступа - Активный каталог Azure
description: Что такое облачные приложения или действия в политике условного доступа Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69bdd2d6825427597e9030a03aae7d219361ba25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671950"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Условный доступ: облачные приложения или действия

Облачные приложения или действия являются ключевым сигналом в политике условного доступа. Политики условного доступа позволяют администраторам присваивать элементы управления определенным приложениям или действиям.

- Администраторы могут выбрать из списка приложений, которые включают встроенные приложения Майкрософт и любые [интегрированные приложения Azure AD,](../manage-apps/what-is-application-management.md) включая галерею, негалерею и приложения, опубликованные через [Application Proxy.](../manage-apps/what-is-application-proxy.md)
- Администраторы могут определить политику не на основе облачного приложения, а на действиях пользователя. Единственным поддерживаемым действием является регистрационная информация безопасности (предварительный просмотр), позволяющая условный доступ для обеспечения контроля вокруг [комбинированного опыта регистрации информации безопасности.](../authentication/howto-registration-mfa-sspr-combined.md)

![Определение политики условного доступа и определение облачных приложений](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Облачные приложения Майкрософт

Многие из существующих облачных приложений Майкрософт включены в список приложений, которые вы можете выбрать. 

Администраторы могут назначить политику условного доступа следующим облачным приложениям от корпорации Майкрософт. Некоторые приложения, такие как Office 365 (предварительный просмотр) и Microsoft Azure Management, включают несколько связанных с ними приложений или служб. Следующий список не является исчерпывающим и может быть изменен.

- [Офис 365 (предварительный просмотр)](#office-365-preview)
- Службы Azure Analysis Services
- Azure DevOps
- [База данных SQL и хранилище данных Azure;](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Аналитика Microsoft Application Insights
- [Microsoft Azure Information Protection.](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Управление Microsoft Azure](#microsoft-azure-management)
- Управление подпиской Microsoft Azure
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control Portal
- Служба аутентификации торговых инструментов Майкрософт
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Регистрация в Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Планировщик (Майкрософт);
- Microsoft PowerApps
- Поиск в Microsoft в Бинге
- Microsoft StaffHub
- Microsoft Stream;
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Офис Суэй
- Outlook Groups
- Служба Power BI
- Project Online
- Skype для бизнеса Online
- Виртуальная частная сеть (VPN)
- ATP в Защитнике Windows

### <a name="office-365-preview"></a>Офис 365 (предварительный просмотр)

Office 365 предоставляет облачные службы производительности и совместной работы, такие как Exchange, SharePoint и Microsoft Teams. Облачные сервисы Office 365 глубоко интегрированы для обеспечения бесперебойного и совместного взаимодействия. Эта интеграция может привести к путанице при создании политик, так как некоторые приложения, такие как команды Майкрософт, зависят от других, таких как SharePoint или Exchange.

Приложение Office 365 (предварительный просмотр) позволяет таргетировать эти службы одновременно. Мы рекомендуем использовать новое приложение Office 365 (предварительный просмотр) вместо таргетинга на отдельные облачные приложения. Таргетинг этой группы приложений помогает избежать проблем, которые могут возникнуть из-за несовместимых политик и зависимостей.

Администраторы могут исключить определенные приложения из политики, если они пожелают, включив приложение Office 365 (предварительный просмотр) и исключив конкретные приложения по своему выбору в политику.

Ключевые приложения, включенные в клиентское приложение Office 365 (предварительный просмотр):

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream;
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Служба поиска Office 365
   - Office 365 Yammer
   - Office Delve
   - Office Online 
   - Office.com
   - OneDrive
   - PowerApps.
   - Skype для бизнеса Online
   - Sway

### <a name="microsoft-azure-management"></a>Управление Microsoft Azure

Приложение Microsoft Azure Management включает в себя несколько базовых служб. 

   - Портал Azure
   - Поставщик ресурсов Azure
   - Классические apis модели развертывания
   - Azure PowerShell
   - Администратор ампонок Visual Studio
   - Azure DevOps
   - Портал Azure Data Factory

> [!NOTE]
> Приложение Microsoft Azure Management применяется к Azure PowerShell, которая называется API-и. Она не применяется к Azure AD PowerShell, которая вызывает Microsoft Graph.

## <a name="other-applications"></a>Другие приложения

Помимо приложений Майкрософт, администраторы могут добавить любое приложение, зарегистрированное ВАЗу, в политики условного доступа. К таким приложениям могут относиться: 

- Приложения, опубликованные через [приложение Azure AD Proxy](../manage-apps/what-is-application-proxy.md)
- [Приложения, добавленные из галереи](../manage-apps/add-application-portal.md)
- [Пользовательские приложения не в галерее](../manage-apps/add-non-gallery-app.md)
- [Устаревшие приложения, опубликованные через контроллеры и сети доставки приложений](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Действия пользователя

Действия пользователя являются задачами, которые могут быть выполнены пользователем. Единственным поддерживаемым в настоящее время действием является информация о **безопасности регистра (предварительный просмотр),** которая позволяет политике условного доступа обеспечивать соблюдение, когда пользователи, включенные для комбинированной регистрации, пытаются зарегистрировать свою информацию о безопасности. Более подробную информацию можно найти в статье, [Комбинированная регистрация информации безопасности (предварительный просмотр)](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Дальнейшие действия

- [Условный доступ: условия](concept-conditional-access-conditions.md)

- [Общие политики условного доступа](concept-conditional-access-policy-common.md)
- [Зависимости от клиентских приложений](service-dependencies.md)
