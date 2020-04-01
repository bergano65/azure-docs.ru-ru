---
title: Краткое руководство. Создание клиента и доступ к нему в Azure AD
description: Инструкции по поиску Azure Active Directory и созданию клиента для организации.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f29d103ce1be426fb0b5c462cc1d831fefe87b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050008"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Краткое руководство. Создание клиента в Azure Active Directory
С помощью портала Azure Active Directory (Azure AD) можно выполнять все административные задачи, включая создание клиента для своей организации. 

В этом кратком руководстве вы узнаете, как получить доступ к порталу Azure и Azure Active Directory. Кроме того, вы узнаете, как создать базовый клиент для своей организации.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="create-a-new-tenant-for-your-organization"></a>Создание клиента для организации
После входа на портал Azure можно создать клиент для своей организации. Новый клиент представляет организацию и помогает управлять определенным экземпляром облачных служб Майкрософт для внутренних и внешних пользователей.

### <a name="to-create-a-new-tenant"></a>Создание клиента

1. Войдите на [портал Azure](https://portal.azure.com/) своей организации.

1. В меню портала Azure выберите **Создать ресурс**.  

    ![Страница создания ресурса Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Выберите **Удостоверение**, а затем щелкните **Azure Active Directory**.

    Откроется страница **Создание каталога**.

    ![Страница создания каталога Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  На странице **Создание каталога** введите следующие сведения.
    
    - В поле **Название организации** введите _Contoso_.

    - В поле **Первоначальное доменное имя** введите _Contoso_.

    - Оставьте значение _США_ в поле **Страна или регион**.

1. Нажмите кнопку **создания**.

Будет создан клиент с доменом contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы не собираетесь использовать это приложение в дальнейшем, можно удалить клиент, выполнив следующие действия:

- Войдите в каталог, который нужно удалить, с помощью фильтра **Каталог и подписка** на портале Azure и при необходимости переключитесь на целевой каталог.
- Выберите **Azure Active Directory**, а затем на странице **Contoso — обзор** выберите **Удалить каталог**.

    Клиент и связанные с ним данные будут удалены.

    ![Странице "Обзор" с выделенной кнопки "Удалить каталог"](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Дальнейшие действия
- Изменение и добавление доменных имен описывается в статье [Добавление имени личного домена в Azure Active Directory](add-custom-domain.md).

- Добавление пользователей описывается в статье [Краткое руководство по добавлению новых пользователей в Azure Active Directory](add-users-azure-active-directory.md).

- Добавление групп и участников описывается в статье [Создание группы и добавление в нее пользователей в Azure Active Directory](active-directory-groups-create-azure-portal.md).

- Узнайте о [доступе на основе ролей с помощью управления привилегированными пользователями](../../role-based-access-control/pim-azure-resource.md) и [условном доступе](../../role-based-access-control/conditional-access-azure-management.md), чтобы упростить управление доступом к приложениям и ресурсам в своей организации.

- Узнайте об Azure AD, в частности о [лицензировании, терминологии и связанных функциях](active-directory-whatis.md).
