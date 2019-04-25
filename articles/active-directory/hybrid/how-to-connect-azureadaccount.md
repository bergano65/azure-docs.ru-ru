---
title: 'Синхронизация Azure AD Connect: управление учетной записью службы Azure AD | Документация Майкрософт'
description: В этой статье описывается процедура восстановления учетной записи службы Azure AD.
services: active-directory
keywords: AADSTS70002, AADSTS50054 Как сбросить пароль для учетной записи службы соединителя синхронизации Azure AD Connect
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/12/2017
ms.date: 11/08/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: f88318c87e29567b40b5eacf10f3b6f259adee8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351024"
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Синхронизация Azure AD Connect: управление учетной записью службы Azure AD
Учетная запись службы, используемая соединителем Azure AD, как правило, не нуждается в обслуживании. Если требуется сбросить ее учетные данные, в этой статье вы найдете необходимые сведения. Например, это потребуется, если глобальный администратор по ошибке сбросит пароль учетной записи службы с помощью PowerShell.

## <a name="reset-the-credentials"></a>Сброс учетных данных
Если учетная запись службы, определенная в соединителе Azure AD, не может связаться с Azure AD из-за проблем с проверкой подлинности, можно сбросить пароль.

1. Войдите на сервер синхронизации Azure AD Connect и запустите PowerShell.
2. Запустите `Add-ADSyncAADServiceAccount`.  
   ![Командлет PowerShell addadsyncaadserviceaccount](./media/how-to-connect-azureadaccount/addadsyncaadserviceaccount.png)
3. Введите учетные данные глобального администратора Azure AD.

Этот командлет сбрасывает пароль для учетной записи службы и обновляет его в Azure AD и в модуле синхронизации.

## <a name="known-issues-these-steps-can-solve"></a>Известные проблемы, которые можно решить с помощью описанных действий
Ниже представлен список обнаруженных пользователями ошибок, которые можно исправить путем сброса учетных данных учетной записи службы Azure AD.

- - -
Событие 6900  
Сервер обнаружил непредвиденную ошибку при обработке уведомления об изменении пароля.  
AADSTS70002: ошибка при проверке учетных данных. AADSTS50054: Для идентификации используется старый пароль.

- - -
Событие 659  
Произошла ошибка при получении конфигурации синхронизации политики паролей. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException.  
AADSTS70002: ошибка при проверке учетных данных. AADSTS50054: Для идентификации используется старый пароль.

## <a name="next-steps"></a>Дальнейшие действия
**Обзорные статьи**

- [Синхронизация Azure AD Connect: общие сведений о синхронизации и ее настройка](how-to-connect-sync-whatis.md)
- [Интеграция локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md)


