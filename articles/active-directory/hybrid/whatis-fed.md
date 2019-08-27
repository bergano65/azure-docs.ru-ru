---
title: Использование федерации с Azure Active Directory | Документы Майкрософт
description: Использование федерации с Azure Active Directory
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52f9832a4307ea9519df448f192eeca70043a6d6
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035172"
---
# <a name="what-is-federation-with-azure-ad"></a>Использование федерации с Azure Active Directory

Федерация — это совокупность доменов, для которых установлено отношение доверия. Уровень доверия может отличаться, но обычно предусматривает проверку подлинности и почти всегда — авторизацию. Типичная федерация может включать ряд организаций, для которых установлено отношение доверия для общего доступа к набору ресурсов.

Можно создать федерацию локальной среды с помощью Azure AD, чтобы использовать ее для проверки подлинности и авторизации.  Этот метод входа гарантирует, что все проверки подлинности пользователя происходит локально.  Этот метод позволяет администраторам реализовать более строгие уровни контроля доступа. Поддерживается федерация с AD FS и PingFederate.

![Федеративная идентификация](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Если вы решите использовать федерацию со службами федерации Active Directory (AD FS), вы сможете дополнительно настроить синхронизацию хэша паролей как дополнительную защиту на случай сбоя в инфраструктуре AD FS.


## <a name="next-steps"></a>Следующие шаги

- [Что собой представляет гибридная идентификация](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/whatis-hybrid-identity)
- [Использование Azure AD Connect с расширением Azure AD Connect Health](whatis-azure-ad-connect.md)
- [Использование синхронизации хэша паролей](whatis-phs.md)
- [Использование федерации](whatis-fed.md)
- [Использование единого входа](how-to-connect-sso.md)
- [Принцип работы федерации](how-to-connect-fed-whatis.md)
- [Федерация с PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)
