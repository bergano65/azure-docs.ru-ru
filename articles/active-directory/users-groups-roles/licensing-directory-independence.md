---
title: Характеристики взаимодействия нескольких клиентов с Azure AD | Документация Майкрософт
description: Общие сведения о независимость данных от Azure Active Directory организаций
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbe7b0211775e59504443d30fa253cfa14af13b1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582740"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Принципы взаимодействия нескольких Azure Active Directory организаций

В Azure Active Directory (Azure AD) каждая организация Azure AD полностью независима: одноранговый узел, который логически не зависит от других организаций Azure AD, которыми вы управляете. Такая независимость между организациями включает независимость ресурсов, административную независимость и независимость от синхронизации. Между организациями нет связи "родители-потомки".

## <a name="resource-independence"></a>Независимость ресурсов

* Если вы создаете или удаляете ресурс Azure AD в одной организации, он не влияет на ресурсы в другой организации с частичным исключением внешних пользователей.
* Если одно из доменных имен регистрируется в одной организации, оно не может использоваться в других организациях.

## <a name="administrative-independence"></a>Административная независимость

Если пользователь, не являющийся администратором организации Contoso, создает тестовую организацию Test, то:

* По умолчанию пользователь, создающий организацию, добавляется в эту новую организацию в качестве внешнего пользователя и назначает роль глобального администратора в этой Организации.
* Администраторы организации "contoso" не имеют прямых административных привилегий для организации "тест", если администратор "тест" специально не предоставит эти права доступа. Однако администраторы "contoso" могут контролировать доступ к Организации "тест", если они управляют учетной записью пользователя, создавшего "тест".
* Если добавить или удалить роль Azure AD для пользователя в одной организации, это изменение не повлияет на роли, назначенные пользователю в любой другой организации Azure AD.

## <a name="synchronization-independence"></a>Независимость синхронизации

Каждую организацию Azure AD можно настроить независимо для получения данных, синхронизируемых из одного экземпляра:

* инструмент Azure AD Connect для синхронизации данных с одним лесом AD;
* соединитель Azure Active Directory для Forefront Identity Manager для синхронизации данных с одним или несколькими локальными лесами либо источниками данных за пределами Azure AD.

## <a name="add-an-azure-ad-organization"></a>Добавление Организации Azure AD

Чтобы добавить организацию Azure AD в портал Azure, войдите в [портал Azure](https://portal.azure.com) с помощью учетной записи глобального администратора Azure AD и выберите **создать**.

> [!NOTE]
> В отличие от других ресурсов Azure, Организации Azure AD не являются дочерними ресурсами подписки Azure. Если ваша подписка Azure отменена или истек срок ее действия, вы по-прежнему можете получать доступ к данным Организации Azure AD с помощью Azure PowerShell, API Microsoft Graph или центра администрирования Microsoft 365. Можно также [связать другую подписку с Организацией](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Дальнейшие действия

Рекомендации по лицензированию Azure AD и рекомендации см. в статье [что такое лицензирование Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
