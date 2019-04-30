---
title: Руководство по Настройка Tableau Online для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory настроить автоматическую подготовку и отзыв учетных записей пользователей в Tableau Online.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123760"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Руководство по Настройка Tableau Online для автоматической подготовки пользователей

Этот учебник демонстрирует шаги в Tableau Online и Azure Active Directory (Azure AD) для настройки Azure AD настроить автоматическую подготовку и отзыв пользователей и групп в Tableau Online.

> [!NOTE]
> В данном учебнике соединитель, который является надстройкой служба подготовки пользователей Azure AD. Сведения о назначении эта служба, как это работает и часто задаваемые вопросы см. в разделе [Автоматическая подготовка пользователей и ее отзыв для приложений программного обеспечения как услуга (SaaS) с Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Технические условия

Сценарий, описанный в этом руководстве предполагается, что у вас есть:

*   Клиент Azure AD.
*   Объект [клиент Tableau Online](https://www.tableau.com/).
*   Учетная запись пользователя в Tableau Online с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Этот API доступен для разработчиков, Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Добавить Tableau Online из Azure Marketplace
Перед настройкой Tableau Online для автоматической подготовки пользователей в Azure AD, добавьте Tableau Online из Azure Marketplace в список управляемых приложений SaaS.

Чтобы добавить Tableau Online из Marketplace, выполните следующие действия.

1. В [портала Azure](https://portal.azure.com), в области навигации слева выберите **Azure Active Directory**.

    ![Значок Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Tableau Online** и выберите **Tableau Online** на панели результатов. Чтобы добавить это приложение, выберите **добавить**.

    ![Tableau Online в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Назначить пользователей в Tableau Online

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи или группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, решите, каким пользователям или группам в Azure AD требуется доступ к Tableau Online. Чтобы назначить этим пользователям или группам в Tableau Online, следуйте инструкциям в [назначение пользователя или группы корпоративному приложению](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Важные советы по назначению пользователей в приложении Tableau Online

*   Мы рекомендуем назначить одного пользователя Azure AD в Tableau Online, для тестирования конфигурации автоматической подготовки пользователей. Позже можно назначить дополнительных пользователей или групп.

*   При назначении пользователя Tableau Online, выберите действительную роль для конкретного приложения, если он доступен, в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Настройка автоматической подготовки пользователей в Tableau Online

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD. Используйте его для создания, обновления и отключения пользователей или групп в Tableau Online на основе пользователя или группы назначений в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Tableau Online. Следуйте инструкциям в [Tableau Online входа в руководстве по настройке единого](tableauonline-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, несмотря на то, что эти две возможности дополняют друг друга.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Tableau Online в Azure AD

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **все приложения** > **Tableau Online**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Tableau Online**.

    ![Ссылка на Tableau Online в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Режим подготовки tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **учетные данные администратора** раздел, введите домен, имя пользователя администратора, пароль администратора и URL-адрес содержимого учетной записи Tableau Online:

   * В **домена** окне укажите поддомен на основе шага 6.

   * В **имя пользователя администратора** окне введите имя пользователя учетной записи администратора в клиенте Clarizen. Например, admin@contoso.com.

   * В **пароль администратора** окне введите пароль, соответствующий имени пользователя администратора учетной записи администратора.

   * В **URL-адрес содержимого** окне укажите поддомен на основе шага 6.

6. После входа в учетную запись администратора для Tableau Online, можно получить значения для **домена** и **URL-адрес содержимого** из URL-адрес для страницы.

    * Скопировать **домен** для учетной записи Tableau Online можно из этой части URL-адреса:

        ![Tableau Online домена](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **URL-адрес содержимого** в Tableau Online для учетной записи можно скопировать из этого раздела. Это значение, определенное при настройке учетной записи. В этом примере используется значение "contoso":

        ![Tableau Online URL-адрес содержимого](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Ваш **домена** может отличаться от приведенного здесь.

7. После ввода в поля, указанного в шаге 5, выберите **проверить подключение** и убедитесь, что Azure AD может подключиться к Tableau Online. Если подключение отсутствует, убедитесь, что ваша учетная запись Tableau Online есть разрешения администратора и повторите попытку.

    ![Tableau Online проверить подключение](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. В **уведомление по электронной почте** поле, введите адрес электронной почты пользователя или группу, чтобы получать уведомления об ошибках подготовки. Выберите **отправить уведомление по электронной почте при сбое** "флажок".

    ![Tableau Online сообщения электронной почты](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Щелкните **Сохранить**.

10. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Tableau Online**.

    ![Tableau Online пользователя синхронизации](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Tableau Online, в **сопоставления атрибутов** раздел. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Tableau Online для операций обновления. Чтобы сохранить изменения, выберите **Сохранить**.

    ![Tableau Online сопоставления атрибутов пользователя](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. В разделе **Сопоставления** выберите **Синхронизировать группы Azure Active Directory с Tableau Online**.

    ![Tableau Online группы синхронизации.](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Просмотрите атрибуты группы, которые синхронизированы из Azure AD в Tableau Online, в **сопоставления атрибутов** раздел. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Tableau Online для операций обновления. Чтобы сохранить изменения, выберите **Сохранить**.

    ![Tableau Online сопоставления атрибутов группы](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Чтобы настроить фильтры области, следуйте инструкциям в [области руководстве по фильтрам](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Для включения подготовки службы для Tableau Online, в Azure AD **параметры** измените **состояние подготовки** для **на**.

    ![Состояние подготовки tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Определите пользователей или группы, необходимые для подготовки в Tableau Online. В **параметры** выберите значения в **область**.

    ![Tableau Online область](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Когда вы будете готовы для подготовки, выберите **Сохранить**.

    ![Tableau Online, сохранение](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

После этого начнется начальная синхронизация всех пользователей или группы, определенные в **область** в **параметры** раздел. Начальная синхронизация занимает больше времени, чем более поздней версии синхронизации. Они происходят примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчета о действиях. Отчет зафиксированы все действия, выполняемые с Tableau Online службой подготовки Azure AD.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Managing user account provisioning for enterprise apps in the Azure portal](../manage-apps/configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
