---
title: 'Учебник: Налаживание Harness для автоматического подготовки пользователей с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и декрета учетных записей пользователей в Harness.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057844"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Учебник: Настройка Harness для автоматического подготовки пользователей

В этой статье вы узнаете, как настроить активный каталог Azure (Azure AD) для автоматического предоставления и депрегировании пользователей или групп в Harness.

> [!NOTE]
> В этой статье описывается разъем, построенный поверх службы обеспечения пользователей Azure AD. Для получения важной информации об этой [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md)службе и ответов на часто задаваемые вопросы см.
>
> Сейчас этот соединитель доступен в режиме предварительной версии. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

Сценарий, изложенный в этой статье, предполагает, что у вас уже есть следующие предпосылки:

* клиент Azure AD;
* [Арендатор Harness](https://harness.io/pricing/)
* Учетная запись пользователя в Harness с разрешениями *Админа*

## <a name="assign-users-to-harness"></a>Назначить пользователям Harness

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи или группы, назначенные приложению Azure AD.

Прежде чем настроить и включить автоматическое представление о пользователях, решите, каким пользователям или группам в Azure AD нужен доступ к Harness. Затем можно назначить этих пользователей или групп в Harness, следуя инструкциям в [присваивать пользователю или группе корпоративное приложение.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Важные советы по назначению пользователей Harness

* Мы рекомендовали назначить одному пользователю Azure AD возможность тестирования автоматической конфигурации подготовки пользователей. Дополнительные пользователи или группы могут быть назначены позже.

* При назначении пользователя Harness необходимо выбрать любую действительную роль приложения (если она имеется) в поле диалога **назначения.** Пользователи с ролью *доступа по умолчанию* исключены из подготовки.

## <a name="set-up-harness-for-provisioning"></a>Настройка Harness для подготовки

1. Войдите в систему на консоль [Harness Admin,](https://app.harness.io/#/login)а затем перейдите к непрерывному**управлению доступом к** **безопасности.** > 

    ![Консоль Harness Admin](media/harness-provisioning-tutorial/admin.png)

1. Выберите **клавиши API**.

    ![Ссылка на API Harness](media/harness-provisioning-tutorial/apikeys.png)

1. Выберите **добавление ключа API**. 

    ![Harness Добавить API Ключ ссылку](media/harness-provisioning-tutorial/addkey.png)

1. В панели **Добавить Api Key,** сделать следующее:

    ![Harness Добавить Api ключ панели](media/harness-provisioning-tutorial/title.png)
   
   а. В поле **Name** укажите имя ключа.  
   b. В **разрешении, унаследованных от** списка выпадающих, выберите опцию. 
   
1. Нажмите кнопку **Отправить**.

1. Копируйте **ключ** для последующего использования в этом учебнике.

    ![Harness Создать токен](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Добавить Харнесс из галереи

Прежде чем настроить Harness для автоматической подготовки пользователей с помощью Azure AD, необходимо добавить Harness из галереи приложений Azure AD в список управляемых приложений SaaS.

1. На [портале Azure](https://portal.azure.com) в области слева щелкните **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

1. Выберите **приложения** > Enterprise**Все приложения.**

    ![Ссылка "Все приложения"](common/enterprise-applications.png)

1. Чтобы добавить новое приложение, выберите кнопку **Нового приложения** в верхней части панели.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

1. В поле поиска введите **Harness,** выберите **Harness** в списке результатов, а затем выберите кнопку **Добавить,** чтобы добавить приложение.

    ![Упряжь в списке результатов](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Настройка автоматического предоставления пользователям айнесс 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей или групп в Harness на основе заданий пользователей или групп в Azure AD.

> [!TIP]
> Вы также можете включить SAML основе одного знака для Harness, следуя инструкциям в [Harness одного знака на учебник](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). Вы можете настроить один знак независимо от автоматического подготовки пользователей, хотя эти две функции дополняют друг друга.

> [!NOTE]
> Чтобы узнать больше о конечную точку Harness SCIM, смотрите статью [Harness API Keys.](https://docs.harness.io/article/smloyragsm-api-keys)

Для настройки автоматического пользовательского обеспечения для Harness в Azure AD сделайте следующее:

1. На [портале Azure](https://portal.azure.com)выберите **корпоративные приложения** > **Все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

1. В списке приложений выберите **Harness**.

    ![Ссылка Harness в списке приложений](common/all-applications.png)

1. Выберите **Обеспечение**.

    ![Кнопка подготовки](common/provisioning.png)

1. В **списке** отбрасывания режима подготовки выберите **Автоматический**.

    ![Список "Режим обеспечения"](common/provisioning-automatic.png)

1. В соответствии с **полномочиями админа,** сделать следующее:

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)
 
   а. В поле **URL-адреса арендатора** введите **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`**.  
   b. В поле **Secret Token** введите значение маркера подлинности SCIM, сохраненное в шаге 6 раздела «Настройка харнесса для подготовки».  
   c. Выберите **тестовое соединение,** чтобы гарантировать, что Azure AD может подключиться к Harness. Если соединение выходит из строя, убедитесь, что учетная запись Harness имеет разрешения *Admin,* а затем повторите попытку.

1. В поле **электронной почты уведомления** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке, а затем выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Коробка "Уведомление по электронной почте"](common/provisioning-notification-email.png)

1. Нажмите кнопку **Сохранить**.

1. Под **картографами**выберите **синхронизацию активных пользователей каталога Azure для Harness.**

    ![Ссылка Harness "Синхронизация активных пользователей каталога Azure для использования"](media/harness-provisioning-tutorial/usermappings.png)

1. В соответствии с **атрибутами Mappings**просмотрите атрибуты пользователя, синхронизированные с Azure AD с Harness. Атрибуты, выбранные как *соответствие,* используются для сопоставления учетных записей пользователей в Harness для операций обновления. Чтобы зафиксировать изменения, щелкните **Сохранить**.

    ![Панель пользователя Harness "Attribute Mappings"](media/harness-provisioning-tutorial/userattributes.png)

1. Под **картографами**выберите **синхронизацию групп активных каталогов Azure для Harness.**

    ![Ссылка Harness "Синхронизация активных групп каталогов Azure для использования"](media/harness-provisioning-tutorial/groupmappings.png)

1. В соответствии с **атрибутами Mappings**просмотрите атрибуты группы, синхронизированные с Azure AD с Harness. Атрибуты, выбранные в качестве свойств *соответствия,* используются для соответствия группам в Harness для операций обновления. Чтобы зафиксировать изменения, щелкните **Сохранить**.

    ![Панель харнесса "Атрибут карт" панель](media/harness-provisioning-tutorial/groupattributes.png)

1. Для настройки фильтров для [Attribute-based application provisioning with scoping filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)просмотра см.

1. В **настройках,** чтобы включить службу предоставления Azure AD для Harness, переключите **на** **On.**

    ![Переключатель статуса подготовки переключился на "On"](common/provisioning-toggle-on.png)

1. В **настройках**в списке выпадающих **областей** выберите, как вы хотите синхронизировать пользователей или группы, которые вы создаете для Harness.

    ![Область действия подготовки](common/provisioning-scope.png)

1. Когда вы будете готовы к предоставлению, выберите **Сохранить**.

    ![Кнопка "Сохранение"](common/provisioning-configuration-save.png)

Эта операция запускает начальную синхронизацию пользователей или групп, которые вы предоставляете. Начальная синхронизация занимает больше времени, чем более поздние. Синхронизация происходит примерно каждые 40 минут при запуске службы обеспечения Azure AD. Чтобы следить за прогрессом, перейдите в раздел **Сведения о синхронизации.** Вы также можете следовать ссылкам на отчет о деятельности подготовки, в котором описаны все действия, выполняемые службой предоставления AD Azure AD на Harness.

Для получения дополнительной информации о том, как читать [Report on automatic user account provisioning](../app-provisioning/check-status-user-account-provisioning.md)журналы подготовки Azure AD, см.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
