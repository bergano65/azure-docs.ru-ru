---
title: Руководство. Настройка Tableau Online для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отмены предоставления учетных записей пользователей в Tableau Online.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064225"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Руководство. Настройка Tableau Online для автоматической подготовки пользователей

В этом руководстве описываются действия, которые необходимо выполнить в Tableau Online и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отмены наполнения пользователей и групп в Tableau Online.

> [!NOTE]
> В этом руководстве описывается соединитель, созданный на основе службы подготовки пользователей Azure AD. Сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. [в статье Автоматизация подготовки пользователей и ее отработки в приложения SaaS (программное обеспечение как услуга) с Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>предварительные требования

В сценарии, описанном в этом учебнике, предполагается, что у вас есть:

*   Клиент Azure AD.
*   [Клиент Tableau Online](https://www.tableau.com/).
*   Учетная запись пользователя в Tableau Online с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [Интернет-интерфейса Tableau Online](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Этот API доступен для разработчиков Tableau в Интернете.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Добавление Tableau Online из Azure Marketplace
Перед настройкой Tableau Online для автоматической подготовки пользователей с помощью Azure AD добавьте Tableau Online из Azure Marketplace в список управляемых приложений SaaS.

Чтобы добавить Tableau Online из Marketplace, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)в области навигации слева выберите **Azure Active Directory**.

    ![Значок Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Tableau Online** и выберите **Tableau Online** на панели результатов. Чтобы добавить приложение, нажмите кнопку **Добавить**.

    ![Tableau Online в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Назначение пользователей в Tableau Online

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только те пользователи или группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей решите, каким пользователям или группам в Azure AD нужен доступ к Tableau Online. Чтобы назначить этих пользователей или группы в Tableau Online, следуйте инструкциям в статье [Назначение пользователя или группы корпоративному приложению](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Важные советы по назначению пользователей в приложении Tableau Online

*   Мы рекомендуем назначить одного пользователя Azure AD для Tableau Online, чтобы протестировать конфигурацию автоматической подготовки пользователей. Позднее можно назначить дополнительных пользователей или группы.

*   При назначении пользователя в Tableau Online выберите любую допустимую роль приложения, если она доступна, в диалоговом окне Назначение. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Настройка автоматической подготовки пользователей в Tableau Online

В этом разделе описывается процедура настройки службы подготовки Azure AD. Используйте его для создания, обновления и отключения пользователей или групп в Tableau Online на основе назначений пользователей или групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Tableau Online. Следуйте инструкциям в руководстве по [единому входу в Tableau Online](tableauonline-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Tableau Online в Azure AD

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **все приложения** > **Tableau в сети**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Tableau Online**.

    ![Ссылка на Tableau Online в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Режим подготовки Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **учетные данные администратора** введите домен, имя пользователя администратора, пароль администратора и URL-адрес содержимого учетной записи Tableau Online:

   * В поле **домен** заполните поддомен на основе шага 6.

   * В поле **имя администратора** введите имя пользователя учетной записи администратора в клиенте Clarizen. Например, admin@contoso.com.

   * В поле **пароль администратора** введите пароль учетной записи администратора, соответствующий имени пользователя администратора.

   * В поле **URL-адрес содержимого** заполните поддомен на основе шага 6.

6. После входа в учетную запись администратора для Tableau Online можно получить значения для **домена** и **URL-адреса содержимого** , используя URL-адрес страницы администрирования.

    * Скопировать **домен** для учетной записи Tableau Online можно из этой части URL-адреса:

        ![Домен Tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **URL-адрес содержимого** учетной записи Tableau Online можно скопировать из этого раздела. Это значение определяется во время настройки учетной записи. В этом примере используется значение "contoso":

        ![URL-адрес содержимого Tableau Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Ваш **домен** может отличаться от показанного здесь.

7. После заполнения полей, показанных на шаге 5, выберите **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Tableau Online. В случае сбоя подключения убедитесь, что учетная запись Tableau Online имеет разрешения администратора, и повторите попытку.

    ![Подключение к оперативному тесту Tableau](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. В поле e-mail **Notification (уведомление** ) введите адрес электронной почты пользователя или группы для получения уведомлений об ошибках подготовки. Установите флажок **отправлять уведомление по электронной почте при возникновении сбоя** .

    ![Электронное уведомление Tableau Online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Щелкните **Сохранить**.

10. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Tableau Online**.

    ![Синхронизация пользователей Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Tableau Online в разделе **сопоставления атрибутов** . Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Tableau Online для операций обновления. Чтобы сохранить изменения, нажмите кнопку **сохранить**.

    ![Tableau сетевые соответствующие атрибуты пользователя](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. В разделе **Сопоставления** выберите **Синхронизировать группы Azure Active Directory с Tableau Online**.

    ![Tableau Интернет-синхронизации групп](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Проверьте атрибуты группы, которые синхронизированы из Azure AD в Tableau Online в разделе **сопоставления атрибутов** . Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Tableau Online для операций обновления. Чтобы сохранить изменения, нажмите кнопку **сохранить**.

    ![Tableau сетевые соответствующие атрибуты группы](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Чтобы настроить фильтры области, следуйте инструкциям в [руководстве по фильтрации областей](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Чтобы включить службу подготовки Azure AD для Tableau Online, в разделе **Параметры** измените значение параметра **состояние подготовки** на **включено**.

    ![Состояние подготовки Tableau в сети](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Определите пользователей или группы, которые вы хотите подготавливать в Tableau Online. В разделе **Параметры** выберите нужные значения в **области**.

    ![Область Tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Когда вы будете готовы к подготовке к работе, нажмите кнопку **сохранить**.

    ![Tableau оперативное сохранение](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Эта операция запускает начальную синхронизацию всех пользователей или групп, определенных в **области** , в разделе **параметров** . Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Они происходят примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам в отчет о действиях по подготовке. В этом отчете описываются все действия, выполняемые службой подготовки Azure AD в Tableau Online.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Managing user account provisioning for enterprise apps in the Azure portal](../app-provisioning/configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
