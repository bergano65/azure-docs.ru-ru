---
title: Удаление Azure Active Directory доменных служб | Документация Майкрософт
description: Узнайте, как отключить или удалить управляемый домен доменных служб Azure Active Directory с помощью портал Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: ee4c41ca3179eead0e4fd470a02a5cdfa4a6d43a
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705233"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Удаление управляемого домена Azure Active Directory доменных служб с помощью портал Azure

Если управляемый домен больше не нужен, можно удалить экземпляр Azure Active Directory доменных служб (Azure AD DS). Нет возможности отключить или временно отключить управляемый домен AD DS Azure. Удаление управляемого домена AD DS Azure не приводит к удалению или иным образом отрицательно скажется на клиенте Azure AD. В этой статье показано, как с помощью портал Azure удалить управляемый домен AD DS Azure.

> [!WARNING]
> **Удаление является постоянным и не может быть отменено.**
> При удалении управляемого домена AD DS Azure выполняются следующие действия.
>   * Контроллеры управляемого домена отзываются и удаляются из виртуальной сети.
>   * Данные управляемого домена удаляются без возможности восстановления. Эти данные включают в себя пользовательские подразделения, объекты групповой политики, пользовательские записи DNS, субъекты-службы, Gmsa и т. д., которые вы создали.
>   * Компьютеры, присоединенные к управляемому домену, теряют отношение доверия с доменом и должны отключиться от него.
>       * Вы не можете войти на эти компьютеры, используя учетные данные организации AD. Вместо этого необходимо использовать учетные данные локального администратора для компьютера.

## <a name="delete-the-managed-domain"></a>Удаление управляемого домена

Чтобы удалить управляемый домен AD DS Azure, выполните следующие действия.

1. В портал Azure найдите и выберите **доменные службы Azure AD**.
1. Выберите имя управляемого домена Azure AD DS, например *aadds.contoso.com*.
1. На странице **Обзор** выберите **Удалить**. Чтобы подтвердить удаление, введите доменное имя управляемого домена еще раз, а затем выберите **Удалить**.

Удаление управляемого домена AD DS Azure может занять 15-20 минут или более.

## <a name="next-steps"></a>Дальнейшие действия

Рассмотрите возможность [предоставления отзывов][feedback] о функциях, которые вы хотели бы увидеть в AD DS Azure.

Если вы хотите начать работу с Azure AD DS еще раз, см. статью [Создание и настройка Azure Active Directory экземпляра доменных служб][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
