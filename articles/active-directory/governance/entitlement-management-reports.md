---
title: Просмотр отчетов и журналов в управлении назначениями Azure AD (Предварительная версия) — Azure Active Directory
description: Узнайте, как просматривать отчеты о назначениях пользователей и журналы аудита в Azure Active Directory управления назначением (Предварительная версия).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b4751b421f5af1536af9a88d15e060ab59bdb
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489058"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Просмотр отчетов и журналов в управлении назначениями Azure AD (Предварительная версия)

> [!IMPORTANT]
> Управление правами Azure Active Directory (Azure AD) сейчас предоставляется в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="view-resources-a-user-has-access-to"></a>Просмотр ресурсов, к которым у пользователя есть доступ

1. Щелкните **Azure Active Directory** и выберите **Управление удостоверениями**.

1. В меню слева щелкните **отчет о назначениях пользователей**.

1. Щелкните **выбрать пользователей** , чтобы открыть панель Выбор пользователей.

1. Найдите пользователя в списке, к которому требуется просмотреть ресурсы, к которым у них есть доступ.

1. Щелкните пользователя и нажмите кнопку **выбрать**.

    Отображается список ресурсов, к которым имеет доступ пользователь. Он включает в себя пакет доступа, политику и даты.

    ![Отчет о назначениях пользователей](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Определение состояния запроса пользователя

Чтобы получить дополнительные сведения о том, как пользователь запрашивает и получил доступ к пакету Access, можно использовать журнал аудита Azure AD. В частности, можно использовать записи журнала в `EntitlementManagement` категориях и `UserManagement` для получения дополнительных сведений о шагах обработки для каждого запроса.  

1. Щелкните **Azure Active Directory** и выберите **журналы аудита**.

1. В верхней части измените **категорию** на `EntitlementManagement` или `UserManagement`, в зависимости от записи аудита, которую вы ищете.  

1. Нажмите кнопку **Применить**.

1. Чтобы скачать журналы, нажмите кнопку **скачать**.

Когда Azure AD получает новый запрос, он записывает запись аудита, в которой **Категория** является `EntitlementManagement` , а **действие** — обычно `User requests access package assignment`.  В случае прямого назначения, созданного в портал Azure, поле **действия** записи аудита имеет `Administrator directly assigns user to access package`значение, а пользователь, выполняющий назначение, идентифицируется с помощью **акторусерпринЦипалнаме**.

Azure AD будет записывать дополнительные записи аудита во время выполнения запроса, в том числе:

| Категория | Действия | Состояние запроса |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Запрос не требует утверждения |
| `UserManagement` | `Create request approval` | Запрос требует утверждения |
| `UserManagement` | `Add approver to request approval` | Запрос требует утверждения |
| `EntitlementManagement` | `Approve access package assignment request` | Запрос утвержден |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Запрос утвержден или не требует утверждения |

Когда пользователю назначается доступ, Azure AD записывает запись аудита для `EntitlementManagement` категории с **действием** `Fulfill access package assignment`.  Пользователь, получивший доступ, идентифицируется по полю **акторусерпринЦипалнаме** .

Если доступ не был назначен, `EntitlementManagement` Azure AD записывает запись аудита для категории с **действием** либо `Deny access package assignment request`, если запрос был отклонен утверждающим, либо `Access package assignment request timed out (no approver action taken)`, если время ожидания запроса истекло до утверждения утверждающего.

По истечении срока действия назначения пакета доступа пользователем или после его удаления администратором Azure AD записывает запись аудита для `EntitlementManagement` категории с **действием** . `Remove access package assignment`

## <a name="next-steps"></a>Следующие шаги

- [Устранение неполадок управления назначениями Azure AD](entitlement-management-troubleshoot.md)
- [Распространенные сценарии](entitlement-management-scenarios.md)
