---
title: Collaborate with others - LUIS
titleSuffix: Azure Cognitive Services
description: An app owner can add contributors to the authoring resource. These contributors can modify the model, train, and publish the app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: efeb8b28ef2b8eec480fcf5090e0173d6451deef
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225527"
---
# <a name="add-contributors-to-your-app"></a>Add contributors to your app

An app owner can add contributors to apps. Они могут изменять модель, обучать и публиковать приложение. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Add contributor to Azure authoring resource

The following procedure is for all users that have **migrated** to use the Azure authoring resource.

You have migrated if your LUIS authoring experience is tied to an Authoring resource on the **Manage -> Azure resources** page in the LUIS portal.

1. In the Azure portal, find the Language Understanding (LUIS) authoring resource. It has the type `LUIS.Authoring`.
1. On this resource's **Access Control (IAM)** page, select **+Add** then select **Add role assignment**.

    ![In Azure portal, add role assignment on authoring resource.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. In the **Add role assignment** window, select the **Role** of Contributor. In the **Assign access to** option, select **Azure AD user, group, or service principal**. In the **Select** option, enter the user's email address. If the user is known by more than 1 email address for the same domain, make sure the enter the _primary_ email account.

    ![Add user's email to the contributor role for Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    When the user's email is found, select the account and select **Save**. 

    If you have trouble with this role assignment, review [Azure role assignments](../../role-based-access-control/role-assignments-portal.md) and [Azure access control troubleshooting](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Add collaborator to LUIS app

The following procedure is for all users that have **not migrated** to use the Azure authoring resource.

You have not migrated if your LUIS authoring experience is not tied to an Authoring resource on the **Manage -> Azure resources** page in the LUIS portal.

У приложения есть один владелец, он же автор. Кроме него, у приложения может быть много участников совместной работы. Чтобы участники совместной работы могли изменять приложение, их адреса электронной почты, используемые для доступа к порталу LUIS, необходимо добавить в список участников совместной работы. Приложение появится на их портале LUIS после добавления.

1. Выберите **Управление** в правом верхнем меню, а затем **Участники совместной работы** в меню слева.

1. На панели инструментов следует выбрать **Add Collaborator** (Добавить участника совместной работы).

1. Чтобы войти на портал LUIS необходимо выбрать адрес электронной почты, используемый участником совместной работы.

    ![Добавления адресов электронной почты участников совместной работы](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Users with multiple emails 

If you add contributors/collaborators to a LUIS app, you are specifying the exact email address. While Azure Active Directory (Azure AD) allows a single user to have more than one email account used interchangeably, LUIS requires the user to sign in with the email address specified when adding the contributor/collaborator.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Ресурсы Azure Active Directory

Если вы используете [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) в вашей организации, для Распознавания речи (LUIS) необходимо разрешение на доступ к информации о ваших пользователях, когда они хотят использовать LUIS. Для LUIS требуются минимальные ресурсы. 

При попытке входа в учетную запись с согласием администратора, или для которой данное согласие не требуется, появится подробное описание.

* Позволяет войти в приложение с учетной записью организации, а приложению — считать данные вашего профиля и основные сведения об организации. Это позволяет LUIS считывать такие основные данные профиля, как идентификатор пользователя, адрес электронной почты и имя.
* Позволяет приложению просматривать и изменять данные, даже в том случае, если вы сейчас его не используете. Для обновления маркера доступа пользователя необходимо разрешение.


### <a name="azure-active-directory-tenant-user"></a>Пользователь клиента Azure Active Directory

Служба LUIS использует стандартный поток согласия Azure Active Directory (Azure AD). 

Администратор клиента должен работать непосредственно с пользователем, которому нужно предоставить доступ на использование службы LUIS в Azure AD. 

* Сначала пользователю необходимо зарегистрироваться в службе LUIS. При этом он увидит всплывающее диалоговое окно, требующее подтверждения администратора. Перед тем как продолжить, пользователю необходимо обратится к администратору клиента. 
* Затем администратор клиента войдет в службу LUIS и увидит всплывающее окно. Чтобы предоставить разрешение пользователю, во всплывающем окне администратор должен дать разрешение. Пользователь сможет продолжить работу со службой LUIS после подтверждения администратора. Если администратор клиента не войдет в службу LUIS, он может войти в [согласие](https://account.activedirectory.windowsazure.com/r#/applications) для LUIS, как показано на следующем изображении. Обратите внимание на то, что список будет отфильтрован для отображения элементов, которые включают имя `LUIS`.

![Разрешение Azure Active Directory на веб-сайте приложения](./media/luis-how-to-collaborate/tenant-permissions.png)

Если администратору клиента требуется, чтобы только определенные пользователи могли применять LUIS, для этого есть несколько возможных решений.
* Предоставить "согласие администратора" (согласия для всех пользователей Azure AD), а затем установить значение "Да" для параметра "Требуется назначение пользователей" в свойствах приложения уровня "Корпоративный" и наконец назначить или добавить только нужных пользователей к приложению. При использовании этого метода администратор по-прежнему предоставляет "согласие администратора", имея при этом возможность контролировать доступ пользователей к приложению.
* Второе решение — использовать [API Graph Azure AD](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) для предоставления согласия для каждого конкретного пользователя. 

Узнайте больше о пользователях и согласии Azure Active Directory. 
* [Ограничьте набор пользователей](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) приложения.

## <a name="next-steps"></a>Дальнейшие действия

* Learn [how to use versions](luis-how-to-manage-versions.md) to control your app life cycle.
* Understand the concepts including the [authoring resource](luis-concept-keys.md#authoring-key) and [contributors](luis-concept-keys.md#contributions-from-other-authors) on that resource.
* Learn [how to create](luis-how-to-azure-subscription.md) authoring and runtime resources
* Migrate to the new [authoring resource](luis-migration-authoring.md) 
