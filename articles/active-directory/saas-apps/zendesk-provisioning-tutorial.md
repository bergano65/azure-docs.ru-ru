---
title: Руководство. Настройка автоматической подготовки пользователей с помощью Azure Active Directory для приложения Zendesk | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory настроить автоматическую подготовку и отзыв учетных записей пользователей в Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 166b7727362549aaf054e3f0282c564eca687eb9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672879"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Учебник. Настройка Zendesk для автоматической подготовки пользователей

Этот учебник демонстрирует шаги в Zendesk и Azure Active Directory (Azure AD) для настройки Azure AD настроить автоматическую подготовку и отзыв пользователей и групп в Zendesk.

> [!NOTE]
> В данном учебнике соединитель, который является надстройкой служба подготовки пользователей Azure AD. Сведения о назначении эта служба, как это работает и часто задаваемые вопросы см. в разделе [Автоматическая подготовка пользователей и ее отзыв для приложений программного обеспечения как услуга (SaaS) с Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>предварительные требования

Сценарий, описанный в этом руководстве предполагается, что у вас есть:

* Клиент Azure AD.
* Клиент Zendesk с [Enterprise](https://www.zendesk.com/product/pricing/) плана или выше;.
* Учетная запись пользователя в Zendesk с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction). Этот API доступен для групп Zendesk, использующих план Enterprise или выше.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Добавить Zendesk из Azure Marketplace

Перед настройкой Zendesk для автоматической подготовки пользователей в Azure AD добавьте Zendesk из Azure Marketplace в список управляемых приложений SaaS.

Чтобы добавить Zendesk из Marketplace, выполните следующие действия.

1. В [портала Azure](https://portal.azure.com), в области навигации слева выберите **Azure Active Directory**.

    ![Значок Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Zendesk** и выберите **Zendesk** на панели результатов. Чтобы добавить это приложение, выберите **добавить**.

    ![Zendesk в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Назначить пользователей в Zendesk

Azure Active Directory используется концепция, называемая *назначения* для определения, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи или группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, решите, каким пользователям или группам в Azure AD требуется доступ к Zendesk. Чтобы назначить этих пользователей или групп в Zendesk, следуйте инструкциям в [назначение пользователя или группы корпоративному приложению](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Важные советы по назначению пользователей в Zendesk

* В настоящее время Zendesk роли автоматически и динамически заполняются в пользовательском Интерфейсе портала Azure. Прежде чем назначить пользователям роли Zendesk, убедитесь, что завершение начальной синхронизации на Zendesk для получения последних ролей в клиенте Zendesk.

* Мы рекомендуем назначить одного пользователя Azure AD в Zendesk для тестирования вашей начальной конфигурации автоматической подготовки пользователей. Можно назначить дополнительных пользователей или групп позже после успешных проверок.

* При назначении пользователя в Zendesk, выберите действительную роль для конкретного приложения, если он доступен, в диалоговом окне назначения. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Настройка автоматической подготовки пользователей в Zendesk 

В этом разделе поможет выполнить действия, чтобы настроить службу подготовки Azure AD. Используйте для создания, обновления и отключения пользователей или групп в Zendesk на основе назначений пользователя или группы в Azure AD.

> [!TIP]
> Вы также можете включить на основе SAML единого входа для Zendesk. Следуйте инструкциям в [Zendesk входа в руководстве по настройке единого](zendesk-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, несмотря на то, что эти две возможности дополняют друг друга.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Zendesk в Azure AD

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **все приложения** > **Zendesk**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Zendesk**.

    ![Ссылка на Zendesk в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Режим подготовки к работе Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. В разделе **учетные данные администратора** раздел, введите имя пользователя администратора, маркер секрета и домен учетной записи Zendesk. Примеры этих значений:

   * В **имя пользователя администратора** окне введите имя пользователя учетной записи администратора в клиенте Zendesk. Например, admin@contoso.com.

   * В **секретный токен** окне заполните маркер секрета, как описано в шаге 6.

   * В **домена** окне введите поддомен вашего клиента Zendesk. Например, для учетной записи с URL-адрес клиента из `https://my-tenant.zendesk.com`, поддомен **мой клиент**.

6. Маркер секрета для учетной записи Zendesk находится в **администратора** > **API** > **параметры**. Убедитесь, что **маркер доступа** присваивается **включено**.

    ![Параметры администратора Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Маркер секрета Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. После ввода в поля, указанного в шаге 5, выберите **проверить подключение** чтобы убедиться в том, что Azure AD может подключиться к Zendesk. Если подключение отсутствует, убедитесь, что у учетной записи Zendesk есть разрешения администратора и повторите попытку.

    ![Zendesk проверить подключение](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. В **уведомление по электронной почте** поле, введите адрес электронной почты пользователя или группу, чтобы получать уведомления об ошибках подготовки. Выберите **отправить уведомление по электронной почте при сбое** "флажок".

    ![Уведомление по электронной почте в Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Щелкните **Сохранить**.

10. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Zendesk**.

    ![Синхронизация пользователей Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Zendesk в **сопоставления атрибутов** раздел. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей Zendesk для операций обновления. Чтобы сохранить изменения, выберите **Сохранить**.

    ![Атрибуты сопоставления пользователей для Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. В разделе **сопоставления** выберите **синхронизировать группы Azure Active Directory с Zendesk**.

    ![Zendesk группы синхронизации.](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Просмотрите атрибуты группы, которые синхронизированы из Azure AD в Zendesk в **сопоставления атрибутов** раздел. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления групп в Zendesk для операций обновления. Чтобы сохранить изменения, выберите **Сохранить**.

    ![Атрибуты сопоставления группы Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Чтобы настроить фильтры области, следуйте инструкциям в [области руководстве по фильтрам](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Для включения подготовки службы для Zendesk, в Azure AD **параметры** измените **состояние подготовки** для **на**.

    ![Состояние подготовки Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Определите пользователей или группы, которые следует для подготовки в Zendesk. В **параметры** выберите значения в **область**.

    ![Область Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Когда вы будете готовы для подготовки, выберите **Сохранить**.

    ![Сохранить Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

После этого начнется начальная синхронизация всех пользователей или группы, определенные в **область** в **параметры** раздел. Начальная синхронизация занимает больше времени, чем более поздней версии синхронизации. Они происходят примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

Можно использовать **сведения о синхронизации** раздел, чтобы отслеживать ход выполнения и переходить по ссылкам для подготовки отчета о действиях. Отчет зафиксированы все действия, выполняемые службой Zendesk подготовки Azure AD.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Zendesk поддерживает использование групп для пользователей с **агента** только для ролей. Дополнительные сведения см. в разделе [Zendesk документации](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* При назначении настраиваемую роль пользователю или группе Azure AD автоматической подготовки пользователей службы также назначает роль по умолчанию **агента**. Настраиваемую роль можно назначить только агенты. Дополнительные сведения см. в разделе [документации по Zendesk API](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Managing user account provisioning for enterprise apps in the Azure portal](../manage-apps/configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
