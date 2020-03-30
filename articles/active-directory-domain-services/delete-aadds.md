---
title: Удаление служб ыидомена активных каталогов Azure (ru) Документы Майкрософт
description: Узнайте, как отключить или удалить домена active Directory Domain Services, управляемый с помощью портала Azure
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
ms.openlocfilehash: e1836f91b8afc1bb4f5b7e141949f3724c57c857
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614036"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Удаление домена Active Directory Domain Services, управляемого с помощью портала Azure

Если вам больше не нужен управляемый домен, можно удалить экземпляр Azure Active Directory Domain Services (Azure AD DS). Нет возможности отключить или временно отключить домена Azure AD DS. Удаление домена Azure AD DS не удаляет или иным образом отрицательно влияет на арендатора Azure AD. В этой статье показано, как использовать портал Azure для удаления домена Azure AD DS.

> [!WARNING]
> **Удаление является постоянным и не может быть отменено.**
> При удалении домена Azure AD DS происходит следующее:
>   * Контроллеры управляемого домена отзываются и удаляются из виртуальной сети.
>   * Данные управляемого домена удаляются без возможности восстановления. Эти данные включают пользовательские OUs, ГП, пользовательские записи DNS, принципы обслуживания, GMSAs и т.д., которые вы создали.
>   * Компьютеры, присоединенные к управляемому домену, теряют отношение доверия с доменом и должны отключиться от него.
>       * Вы не можете войти в эти машины, используя корпоративные учетные данные AD. Вместо этого необходимо использовать учетные данные локального администратора для машины.

## <a name="delete-the-managed-domain"></a>Удалить управляемый домен

Чтобы удалить домен Azure AD DS, выполните следующие действия:

1. На портале Azure ищите и выбирайте **службы домена Azure AD.**
1. Выберите имя домена Azure AD DS, *например, aaddscontoso.com.*
1. На странице **Обзор** выберите **Удалить**. Чтобы подтвердить удаление, введите доменное имя управляемого домена снова, а затем выберите **Удалить**.

Удаление домена Azure AD DS может занять 15-20 минут или более.

## <a name="next-steps"></a>Дальнейшие действия

Рассмотрите [возможность обмена отзывами][feedback] о функциях, которые вы хотели бы видеть в Azure AD DS.

Если вы хотите снова начать работу с DS Azure AD, [см.][create-instance]

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
