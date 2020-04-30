---
title: Облачные приложения или действия в политике условного доступа — Azure Active Directory
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
ms.openlocfilehash: b15b022726c09ccbaf9674775d114c8dd1916e1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457303"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>Условный доступ: облачные приложения или действия

Облачные приложения или действия являются ключевым сигналом в политике условного доступа. Политики условного доступа позволяют администраторам назначать элементы управления конкретным приложениям или действиям.

- Администраторы могут выбрать из списка приложений, которые включают встроенные приложения Майкрософт и все [интегрированные приложения Azure AD](../manage-apps/what-is-application-management.md) , включая коллекцию, не коллекцию и приложения, опубликованные через [прокси приложения](../manage-apps/what-is-application-proxy.md).
- Администраторы могут определить политику, не основанную на облачном приложении, но в действии пользователя. Единственное поддерживаемое действие — зарегистрировать сведения о безопасности (Предварительная версия), что позволяет условному доступу применять элементы управления к [Объединенным сведениям о безопасности](../authentication/howto-registration-mfa-sspr-combined.md).

![Определение политики условного доступа и указание облачных приложений](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Облачные приложения Майкрософт

В список приложений, которые можно выбрать, входят многие из существующих приложений Microsoft Cloud. 

Администраторы могут назначить политику условного доступа для следующих облачных приложений от Майкрософт. Некоторые приложения, такие как Office 365 (Предварительная версия) и управление Microsoft Azure, включают несколько связанных дочерних приложений или служб. Следующий список не является исчерпывающим и может быть изменен.

- [Office 365 (Предварительная версия)](#office-365-preview)
- Службы Azure Analysis Services
- Azure DevOps
- [База данных SQL и хранилище данных Azure;](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Аналитика Microsoft Application Insights
- [Microsoft Azure Information Protection.](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Управление Microsoft Azure](#microsoft-azure-management)
- Управление подпиской Microsoft Azure
- Microsoft Cloud App Security
- Портал управления доступом к средствам Microsoft Commerce
- Служба проверки подлинности инструментов Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Регистрация в Microsoft Intune](/intune/enrollment/multi-factor-authentication)
- Планировщик (Майкрософт);
- Microsoft PowerApps
- Microsoft Search в Bing
- Microsoft StaffHub
- Microsoft Stream;
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Служба Power BI
- Project Online
- Skype для бизнеса Online
- Виртуальная частная сеть (VPN)
- ATP в Защитнике Windows

### <a name="office-365-preview"></a>Office 365 (Предварительная версия)

Office 365 предоставляет облачные службы для повышения производительности и совместной работы, такие как Exchange, SharePoint и Microsoft Teams. Облачные службы Office 365 тесно интегрированы для обеспечения беспрепятственного и совместного взаимодействия. Такая интеграция может вызвать путаницу при создании политик, так как некоторые приложения, такие как Microsoft Teams, имеют зависимости от других, таких как SharePoint или Exchange.

Приложение Office 365 (Предварительная версия) позволяет одновременно ориентироваться на эти службы. Мы рекомендуем использовать новое приложение Office 365 (Предварительная версия) вместо настройки индивидуальных облачных приложений. Нацеливание на эту группу приложений позволяет избежать проблем, которые могут возникать из-за несоответствия политик и зависимостей.

Администраторы могут исключить определенные приложения из политики, если они хотят включить приложение Office 365 (Предварительная версия) и исключить определенные приложения по своему усмотрению в политике.

Ключевые приложения, которые входят в клиентское приложение Office 365 (Предварительная версия):

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

Приложение управления Microsoft Azure включает несколько базовых служб. 

   - Портал Azure
   - Поставщик Azure Resource Manager
   - API-интерфейсы классической модели развертывания
   - Azure PowerShell
   - Портал администратора подписок Visual Studio
   - Azure DevOps
   - Портал фабрики данных Azure

> [!NOTE]
> Приложение управления Microsoft Azure применяется к Azure PowerShell, которое вызывает API Azure Resource Manager. Она не применяется к Azure AD PowerShell, которая вызывает Microsoft Graph.

## <a name="other-applications"></a>Другие приложения

Помимо приложений Майкрософт, администраторы могут добавить любое зарегистрированное приложение Azure AD в политики условного доступа. К таким приложениям могут относиться: 

- Приложения, опубликованные с помощью [Azure AD application proxy](../manage-apps/what-is-application-proxy.md)
- [Приложения, добавленные из коллекции](../manage-apps/add-application-portal.md)
- [Пользовательские приложения, отсутствующие в коллекции](../manage-apps/add-non-gallery-app.md)
- [Устаревшие приложения, опубликованные с помощью контроллеров и сетей доставки приложений](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Действия пользователя

Действия пользователя — это задачи, которые может выполнять пользователь. Единственное поддерживаемое действие — **зарегистрировать сведения о безопасности**, что позволяет применять политику условного доступа, когда пользователи, которым разрешена Объединенная регистрация, регистрируют свои сведения о безопасности. Дополнительные сведения можно найти в статье [Объединенная регистрация сведений о безопасности](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Дальнейшие шаги

- [Условный доступ: условия](concept-conditional-access-conditions.md)

- [Общие политики условного доступа](concept-conditional-access-policy-common.md)
- [Зависимости клиентских приложений](service-dependencies.md)
