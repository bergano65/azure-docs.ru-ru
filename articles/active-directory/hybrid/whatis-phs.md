---
title: Синхронизация хэша паролей с Azure Active Directory | Документы Майкрософт
description: Синхронизация хэша паролей.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e40eecce3ff0ea5b252fd135ae553fa20e0a77b
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85386357"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>Синхронизация хэша паролей с Azure Active Directory
Синхронизация хэша паролей является одним из методов входа, используемым для реализации гибридной идентификации. Azure AD Connect синхронизирует хэш пользовательских паролей между локальным экземпляром службы Active Directory и облачным экземпляром службы Azure AD.

Синхронизация хэшированных паролей — это расширение функции синхронизации каталогов, реализованной в службе синхронизации Azure AD Connect. Эту функцию можно использовать для входа в службу Azure AD, например Office 365. Вы можете входить в службу с помощью пароля, который используется на локальном экземпляре Active Directory.

![Что такое Azure AD Connect?](./media/how-to-connect-password-hash-synchronization/arch1.png)

За счет сокращения числа паролей, которые должны помнить пользователи, до одного, синхронизация паролей дает следующие преимущества: Синхронизация хэша паролей обеспечивает:

* Повышение производительности пользователей.
* Снижение затрат на техническую поддержку.  

Синхронизация хэша паролей также включает [обнаружение утечки учетных данных](../identity-protection/concept-identity-protection-risks.md#user-risk) для гибридных учетных записей. Корпорация Майкрософт вместе со специалистами по теневому Интернету и правоохранительными органами работает над поиском общедоступных пар имен и паролей. Если мы обнаруживаем совпадение какой-либо из таких пар с нашим пользователем, связанной учетной записи присваивается высокая степень риска. 

>[!NOTE]
> Для вашего клиента будут обрабатываться только новые утечки учетных данных, обнаруженные после включения синхронизации хэша паролей. Проверка по ранее найденным парам учетных данных не выполняется.


При необходимости можно настроить синхронизацию хэша паролей в качестве резервного решения, если вы хотите использовать [федерацию со службами федерации Active Directory (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) в качестве метода входа.

Чтобы применить синхронизацию хэшированных паролей в своей среде выполните следующие действия.

* Установите Azure AD Connect.  
* Настройте синхронизацию каталогов между локальным экземпляром службы Active Directory и облачным экземпляром Azure Active Directory.
* Включите синхронизацию хэшированных паролей.



Дополнительные сведения см. в руководстве по [гибридной идентификации](whatis-hybrid-identity.md).




## <a name="next-steps"></a>Next Steps

- [Что собой представляет гибридная идентификация](whatis-hybrid-identity.md)
- [Использование Azure AD Connect с расширением Azure AD Connect Health](whatis-azure-ad-connect.md)
- [Использование сквозной проверки подлинности](how-to-connect-pta.md)
- [Использование федерации](whatis-fed.md)
- [Использование единого входа](how-to-connect-sso.md)
- [Принцип работы синхронизации хэшей паролей](how-to-connect-password-hash-synchronization.md)
