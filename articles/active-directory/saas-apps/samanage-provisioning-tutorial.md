---
title: Руководство. Настройка Samanage для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory настроить автоматическую подготовку и отзыв учетных записей пользователей в Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67cfe5a26740837508ea3a3e76295a896c3cc107
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670925"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Учебник. Настройка Samanage для автоматической подготовки пользователей

Этот учебник демонстрирует шаги в Samanage и Azure Active Directory (Azure AD) для настройки Azure AD настроить автоматическую подготовку и отзыв пользователей и групп в Samanage.

> [!NOTE]
> В данном учебнике соединитель, который является надстройкой служба подготовки пользователей Azure AD. Сведения о назначении эта служба, как это работает и часто задаваемые вопросы см. в разделе [Автоматическая подготовка пользователей и ее отзыв для приложений программного обеспечения как услуга (SaaS) с Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Предварительные требования

Сценарий, описанный в этом руководстве предполагается, что у вас есть:

* Клиент Azure AD.
* Объект [клиент Samanage](https://www.samanage.com/pricing/) с пакет Professional.
* Учетная запись пользователя в Samanage с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [Samanage Rest API](https://www.samanage.com/api/). Этот API доступен разработчикам Samanage для учетных записей с пакет Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Добавить Samanage из Azure Marketplace

Перед настройкой Samanage для автоматической подготовки пользователей в Azure AD добавьте Samanage из Azure Marketplace в список управляемых приложений SaaS.

Чтобы добавить Samanage из Marketplace, выполните следующие действия.

1. В [портала Azure](https://portal.azure.com), в области навигации слева выберите **Azure Active Directory**.

    ![Значок Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Samanage** и выберите **Samanage** на панели результатов. Чтобы добавить это приложение, выберите **добавить**.

    ![Samanage в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Назначить пользователей в Samanage

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи или группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, решите, каким пользователям или группам в Azure AD требуется доступ к Samanage. Чтобы назначить этих пользователей или групп в Samanage, следуйте инструкциям в [назначение пользователя или группы корпоративному приложению](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Важные советы по назначению пользователей в Samanage

*    В настоящее время Samanage роли автоматически и динамически заполняются в пользовательском Интерфейсе портала Azure. Прежде чем назначить пользователям роли Samanage, убедитесь, что завершение начальной синхронизации на Samanage для получения последних ролей в клиенте Samanage.

*    Мы рекомендуем назначить одного пользователя Azure AD в Samanage для тестирования вашей начальной конфигурации автоматической подготовки пользователей. Можно назначить дополнительных пользователей и групп позже после успешных проверок.

*    При назначении пользователя в Samanage, выберите действительную роль для конкретного приложения, если он доступен, в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Настройка автоматической подготовки пользователей в Samanage

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD. Используйте для создания, обновления и отключения пользователей или групп в Samanage на основе назначений пользователя или группы в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Samanage. Следуйте инструкциям в [Samanage входа в руководстве по настройке единого](samanage-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, несмотря на то, что эти две возможности дополняют друг друга.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Samanage в Azure AD

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **все приложения** > **Samanage**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Samanage**.

    ![Ссылка на Samanage в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Режим подготовки к работе в Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **учетные данные администратора** введите имя пользователя администратора и пароль администратора учетной записи Samanage. Примеры этих значений:

   * В **имя пользователя администратора** окне введите имя пользователя учетной записи администратора в клиенте Samanage. Например, admin@contoso.com.

   * В **пароль администратора** окне введите пароль, соответствующий имени пользователя администратора учетной записи администратора.

6. После ввода в поля, указанного в шаге 5, выберите **проверить подключение** чтобы убедиться в том, что Azure AD может подключиться к Samanage. Если подключение отсутствует, убедитесь, что учетной записи Samanage есть разрешения администратора и повторите попытку.

    ![Samanage проверить подключение](./media/samanage-provisioning-tutorial/TestConnection.png)

7. В **уведомление по электронной почте** поле, введите адрес электронной почты пользователя или группу, чтобы получать уведомления об ошибках подготовки. Выберите **отправить уведомление по электронной почте при сбое** "флажок".

    ![Уведомление по электронной почте в Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Щелкните **Сохранить**.

9. В разделе **Сопоставления** выберите **Synchronize Azure Active Directory Users to Samanage** (Синхронизировать пользователей Azure Active Directory с Samanage).

    ![Синхронизация пользователей Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. В разделе **Сопоставления атрибутов** просмотрите пользовательские атрибуты, синхронизированные из Azure AD в Samanage. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Samanage для операций обновления. Чтобы сохранить изменения, выберите **Сохранить**.

    ![Атрибуты сопоставления пользователей для Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Чтобы включить сопоставление групп, в разделе **Сопоставления** выберите **Synchronize Azure Active Directory Groups to Samanage** (Синхронизировать группы Azure Active Directory с Samanage).

    ![Samanage группы синхронизации.](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. В столбце **Включено** выберите значение **Да** для синхронизации групп. В разделе **Сопоставления атрибутов** просмотрите атрибуты группы, синхронизированные из Azure AD в Samanage. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Samanage для операций обновления. Чтобы сохранить изменения, выберите **Сохранить**.

    ![Атрибуты сопоставления группы Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Чтобы настроить фильтры области, следуйте инструкциям в [области руководстве по фильтрам](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Для включения подготовки службы для Samanage, в Azure AD **параметры** измените **состояние подготовки** для **на**.

    ![Состояние подготовки Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Определите пользователей или группы, которые следует для подготовки в Samanage. В **параметры** выберите значения в **область**. При выборе **синхронизация всех пользователей и групп** , то необходимо учитывать ограничения, как описано в следующем разделе «Соединитель ограничения».

    ![Область Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Когда вы будете готовы для подготовки, выберите **Сохранить**.

    ![Сохранить Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


После этого начнется начальная синхронизация всех пользователей или группы, определенные в **область** в **параметры** раздел. Начальная синхронизация занимает больше времени, чем более поздней версии синхронизации. Они происходят примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчета о действиях. Отчет зафиксированы все действия, выполняемые службой Samanage подготовки Azure AD.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

При выборе **синхронизация всех пользователей и групп** и настроить значение для Samanage **ролей** атрибута, значение в разделе **значение по умолчанию в случае, если значение равно null (необязательно)** поле должно быть выражено в следующем формате:

- {«displayName»: «роль»}, где роль — это желаемое значение по умолчанию.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Managing user account provisioning for enterprise apps in the Azure portal](../manage-apps/configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
