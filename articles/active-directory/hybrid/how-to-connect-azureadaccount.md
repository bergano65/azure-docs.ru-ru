---
title: Изменение пароля учетной записи AD-разъема Azure AD (ru) Документы Майкрософт
description: В этой теме изуверятся, как восстановить учетную запись Azure AD Connector.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ea151ee79fccd66f1d9422744d8f57829677ec0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204532"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Изменение пароля учетной записи соединителя Azure AD
Учетная запись Azure AD Connector должна быть бесплатной. Если требуется сбросить ее учетные данные, в этой статье вы найдете необходимые сведения. Например, если Глобальный администратор по ошибке сбросить пароль в учетной записи с помощью PowerShell.

## <a name="reset-the-credentials"></a>Сброс учетных данных
Если учетная запись Azure AD Connector не может связаться с Azure AD из-за проблем с аутентификацией, пароль может быть спрошен.

1. Войдите на сервер синхронизации Azure AD Connect и запустите PowerShell.
2. Выполните `Add-ADSyncAADServiceAccount`.  
   ![Командлет PowerShell addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Введите учетные данные глобального администратора Azure AD.

Этот командлет сбрасывает пароль для учетной записи службы и обновляет его в Azure AD и в модуле синхронизации.

## <a name="known-issues-these-steps-can-solve"></a>Известные проблемы, которые можно решить с помощью описанных действий
Этот раздел представляет собой список ошибок, о которых сообщают клиенты, исключившие учетные данные, сбросить на учетную запись Azure AD Connector.

---
Событие 6900  
Сервер обнаружил непредвиденную ошибку при обработке уведомления об изменении пароля.  
AADSTS70002: ошибка при проверке учетных данных. AADSTS50054: старый пароль используется для проверки подлинности.

---
Событие 659  
Произошла ошибка при получении конфигурации синхронизации политики паролей. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException.  
AADSTS70002: ошибка при проверке учетных данных. AADSTS50054: старый пароль используется для проверки подлинности.

## <a name="next-steps"></a>Дальнейшие действия
**Обзорные статьи**

* [Службы синхронизации Azure AD Connect: общие сведений о синхронизации и ее настройка](how-to-connect-sync-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory.](whatis-hybrid-identity.md)

