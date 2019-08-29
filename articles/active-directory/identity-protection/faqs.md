---
title: Вопросы и ответы о защите идентификации Azure Active Directory | Документация Майкрософт
description: Часто задаваемые вопросы о защите идентификации Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e30f8264870272fc12dcb7e15b37e5fd30688bcb
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126527"
---
# <a name="azure-active-directory-identity-protection-faq"></a>Вопросы и ответы о защите идентификации Azure Active Directory

Эта статья содержит ответы на часто задаваемые вопросы о защите идентификации Azure Active Directory (Azure AD). Дополнительные сведения см. в статье [Защита идентификации Azure Active Directory](../active-directory-identityprotection.md). 

## <a name="why-do-some-risk-detections-have-closed-system-status"></a>Почему некоторые определения рисков имеют состояние "закрыто (система)"?

**Ответ.** Эти обнаружения рисков были обнаружены защитой идентификации и позже закрыты, так как события больше не считаются рискованными. Эти события не учитываются в уровне риска пользователя. 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>Требуется ли роль глобального администратора для использования защиты идентификации на портале Azure?
**Ответ.** Нет. Для использования защиты идентификации можно иметь роль читателя сведений о безопасности, администратора безопасности или глобального администратора.

---

## <a name="how-do-i-get-identity-protection"></a>Как начать использовать защиту идентификации?

**Ответ.** Ответ на этот вопрос есть в статье [Регистрация для работы с выпусками Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md).

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>Как сортировать пользователей в разделе "Пользователи, находящиеся в группе риска"?

**Ответ.** Скачайте отчет по пользователям, находящимся в группе риска, нажав кнопку **Загрузить**  в верхней части страницы **Пользователи, находящиеся в группе риска**. Затем можно отсортировать скачанные данные на основе доступных полей, таких как "Последнее обновление (в формате UTC)".

---
