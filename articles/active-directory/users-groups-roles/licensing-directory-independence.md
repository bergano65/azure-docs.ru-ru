---
title: Характеристики многократного взаимодействия с арендаторами - Azure AD Документы Майкрософт
description: Понимание ваших клиентов Azure Active Directory как полностью независимых организаций
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878125"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Понять, как взаимодействуют несколько активных каталогов Azure

В Active Directory Azure (Azure AD) каждый арендатор является полностью независимой организацией: узла, который логически не зависит от других управляемых адомимой организаций Azure. Эта независимость между организациями включает в себя независимость ресурсов, административную независимость и независимость синхронизации. Между организациями нет отношений между родителями и детьми.

## <a name="resource-independence"></a>Независимость ресурсов

* При создании или удалении ресурса Azure AD в одной организации он не влияет ни на один ресурс в другой организации, за частичным исключением внешних пользователей.
* Если вы регистрируете одно из доменных имен в одной организации, оно не может быть использовано какой-либо другой организацией.

## <a name="administrative-independence"></a>Административная независимость

Если неадминистративный пользователь организации 'Contoso' создает тестовую организацию 'Test', то:

* По умолчанию пользователь, создающий организацию, добавляется в качестве внешнего пользователя в этой новой организации и назначает глобальную роль администратора в этой организации.
* Администраторы организации 'Contoso' не имеют прямых административных привилегий для организации 'Test', если администратор 'Test' специально не предоставляет им эти привилегии. Тем не менее, администраторы 'Contoso' могут контролировать доступ к организации 'Test', если они контролируют учетную запись пользователя, которая создала 'Test'.
* При добавлении или удалении роли Azure AD для пользователя в одной организации изменение не влияет на роли, которые пользователь присваивает в любой другой организации Azure AD.

## <a name="synchronization-independence"></a>Независимость синхронизации

Можно настроить каждую организацию Azure AD независимо для синхронизации данных из одного экземпляра:

* инструмент Azure AD Connect для синхронизации данных с одним лесом AD;
* соединитель Azure Active Directory для Forefront Identity Manager для синхронизации данных с одним или несколькими локальными лесами либо источниками данных за пределами Azure AD.

## <a name="add-an-azure-ad-organization"></a>Добавление организации Azure AD

Чтобы добавить организацию Azure AD на портал Azure, восвай на [портал Azure](https://portal.azure.com) с учетной записью, которая является глобальным администратором Azure AD, и выберите **Новый.**

> [!NOTE]
> В отличие от других ресурсов Azure, ваши ad-организации Azure не являются детскими ресурсами подписки Azure. Если подписка Azure отменена или истекла, вы все равно можете получить доступ к данным организации Azure AD с помощью Azure PowerShell, API Microsoft Graph или центра админатора Microsoft 365. Вы также можете [связать другую подписку с организацией.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
>

## <a name="next-steps"></a>Дальнейшие действия

Для соображений лицензирования Azure AD и передового опыта [см.](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
