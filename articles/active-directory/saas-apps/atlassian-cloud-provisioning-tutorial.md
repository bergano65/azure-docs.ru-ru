---
title: Руководство по Настройка Atlassian Cloud для автоматической подготовки пользователей с Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Atlassian Cloud.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.openlocfilehash: 4e028429ca8a22915eff2b90ca63c6d05a67741b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64692237"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Руководство по Настройка Atlassian Cloud для автоматической подготовки пользователей

Цель данного учебника — продемонстрировать действия, выполняемые в Atlassian Cloud и Azure Active Directory (Azure AD) для настройки Azure AD настроить автоматическую подготовку и отмену подготовки пользователей и групп в Atlassian Cloud.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения о общие Microsoft Azure условия использования предварительных версий функций, см. в разделе [дополнительным условиям использования предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Технические условия

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент Atlassian Cloud](https://www.atlassian.com/licensing/cloud)
* Учетная запись пользователя в Atlassian Cloud с правами администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от **Atlassian Cloud SCIM API**, доступного для команд Atlassian Cloud.

## <a name="add-atlassian-cloud-from-the-gallery"></a>Добавление Atlassian Cloud из коллекции

Прежде чем настроить Atlassian Cloud для автоматической подготовки пользователей в Azure AD, необходимо добавить Atlassian Cloud из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Atlassian Cloud из коллекции приложений Azure AD, выполните следующие действия:**

1. В  **[портала Azure](https://portal.azure.com)** , на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, выберите **новое приложение** кнопку в верхней части области.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Atlassian Cloud**выберите **Atlassian Cloud** в панели результатов и нажмите кнопку **добавить** кнопку, чтобы добавить это приложение.

    ![Atlassian Cloud в списке результатов](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Назначение пользователей в Atlassian Cloud

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только те пользователи и группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, следует решить, какие пользователи или группы в Azure AD требуется доступ к Atlassian Cloud. Сделав это, можно назначить этих пользователей и групп, к Atlassian Cloud, следуя инструкциям ниже:

* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Важные рекомендации по назначению пользователей в Atlassian Cloud

* Рекомендуется один назначенный пользователем Azure AD Atlassian Cloud для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Atlassian Cloud, необходимо выбрать действительную роль конкретного приложения (если доступно) в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Настройка автоматической подготовки пользователей в Atlassian Cloud 

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD для создания, изменения и отключения пользователей и групп в Atlassian Cloud на основе назначений в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Atlassian Cloud, следуйте инструкциям, указанным на [Atlassian Cloud входа в руководстве по настройке единого](atlassian-cloud-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Atlassian Cloud в Azure AD.

1. Войдите в [портала Azure](https://portal.azure.com) и выберите **корпоративные приложения**выберите **все приложения**, а затем выберите **Atlassian Cloud**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Atlassian Cloud**.

    ![Ссылка на Atlassian Cloud в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** и **секретный токен** учетной записи приложения Atlassian Cloud. Примеры этих значений:

   * В **URL-адрес клиента** поле «Заливка» конечная точка конкретного клиента, получаемых от Atlassian, как описано на шаге 6. Например: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * В поле **маркер секрета** введите маркер секрета, как описано в шаге 6.

6. Перейдите к [Manager организации Atlassian](https://admin.atlassian.com) **> Подготовка пользователей** и щелкнуть **создать токен**. Копировать **базовый URL-адрес каталога** и **маркера носителя** для **URL-адрес клиента** и **секретный токен** поля соответственно.

    ![Atlassian Cloud Подготовка](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian Cloud Подготовка](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. После заполнения полей, указанных в шаге 5, щелкните **проверить подключение** и убедиться, что Azure AD может подключиться к Atlassian Cloud. Если подключение отсутствует, убедитесь, что учетная запись Atlassian Cloud имеет разрешения администратора и повторите попытку.

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/notification.png)

9. Выберите команду **Сохранить**.

10. В разделе **сопоставления** выберите **синхронизировать пользователей Azure Active Directory с Atlassian Cloud**.

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Atlassian Cloud в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления учетных записей пользователей в Atlassian Cloud для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

12. В разделе **сопоставления** выберите **синхронизировать группы Azure Active Directory в Atlassian Cloud**.

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

13. Просмотрите атрибуты группы, которые синхронизированы из Azure AD в Atlassian Cloud в **сопоставление атрибутов** раздел. Атрибуты, выбранные как **Matching** свойства используются для сопоставления групп в Atlassian Cloud для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

14. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Чтобы включить службу для Atlassian Cloud подготовки Azure AD, измените **состояние подготовки** для **на** в **параметры** раздел.

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

16. Определите пользователей или группы, которые вы хотите для подготовки к Atlassian Cloud, выбрав нужные значения в **область** в **параметры** раздел.

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

17. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка Atlassian Cloud](./media/atlassian-cloud-provisioning-tutorial/save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов, которых зафиксированы все действия, выполняемые службой Atlassian Cloud подготовки Azure AD о подготовке.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Atlassian Cloud можно подготовить пользователей только из [проверенных доменов](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian Cloud не поддерживает переименование группы уже сегодня. Это означает, что любые изменения в параметр displayName группы в Azure AD не будет обновляться и отражаются в Atlassian Cloud.
* Значение **mail** атрибут пользователя в Azure AD заполняется только в том случае, если у пользователя почтового ящика Microsoft Exchange. Если пользователь не имеет ни одного, рекомендуется для сопоставления различных нужный атрибут для **сообщений электронной почты** атрибут в Atlassian Cloud.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
