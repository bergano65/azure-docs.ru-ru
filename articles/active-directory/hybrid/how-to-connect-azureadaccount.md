---
title: Изменение пароля учетной записи соединителя Azure AD | Документация Майкрософт
description: В этом разделе приведены инструкции для восстановления учетная запись соединителя Azure AD.
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
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204532"
---
# <a name="change-the-azure-ad-connector-account-password"></a>Изменение пароля учетной записи соединителя Azure AD
Предполагается, что учетная запись соединителя Azure AD является бесплатной службы. Если требуется сбросить ее учетные данные, в этой статье вы найдете необходимые сведения. Например если глобальный администратор по ошибке сбросить пароль учетной записи, с помощью PowerShell.

## <a name="reset-the-credentials"></a>Сброс учетных данных
Если учетная запись соединителя Azure AD не удается связаться с Azure AD из-за проблем с проверкой подлинности, можно сбросить пароль.

1. Войдите на сервер синхронизации Azure AD Connect и запустите PowerShell.
2. Запустите `Add-ADSyncAADServiceAccount`.  
   ![Командлет PowerShell addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Введите учетные данные глобального администратора Azure AD.

Этот командлет сбрасывает пароль для учетной записи службы и обновляет его в Azure AD и в модуле синхронизации.

## <a name="known-issues-these-steps-can-solve"></a>Известные проблемы, которые можно решить с помощью описанных действий
В этом разделе приведен список ошибок, обнаруженных пользователями, которые были исправлены путем сброса учетной записи Azure AD соединителя учетных данных.

---
Событие 6900  
Сервер обнаружил непредвиденную ошибку при обработке уведомления об изменении пароля.  
AADSTS70002: ошибка при проверке учетных данных. AADSTS50054: Для идентификации используется старый пароль.

---
Событие 659  
Произошла ошибка при получении конфигурации синхронизации политики паролей. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException.  
AADSTS70002: ошибка при проверке учетных данных. AADSTS50054: Для идентификации используется старый пароль.

## <a name="next-steps"></a>Дальнейшие действия
**Обзорные статьи**

* [Синхронизация Azure AD Connect: общие сведений о синхронизации и ее настройка](how-to-connect-sync-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md)

