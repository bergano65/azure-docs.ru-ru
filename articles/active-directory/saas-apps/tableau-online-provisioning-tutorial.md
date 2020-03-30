---
title: Руководство. Настройка Tableau Online для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и депрезации учетных записей пользователей в Tableau Online.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064225"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Руководство. Настройка Tableau Online для автоматической подготовки пользователей

В этом уроке показаны шаги для выполнения в каталогах Tableau Online и Azure Active Directory (Azure AD) для настройки Azure AD для автоматического предоставления и деформатива пользователей и групп в Tableau Online.

> [!NOTE]
> В этом уроке описывается разъем, построенный поверх службы предоставления пользовательского обеспечения Azure AD. Для получения информации о том, что эта служба делает, как она работает, и часто задаваемые вопросы, см [Автоматизировать пользовательские подготовки и деобеспечения программного обеспечения как службы (SaaS) приложений с Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Предварительные требования

Сценарий, изложенный в этом учебнике предполагает, что у вас есть:

*   Клиент Azure AD.
*   [Tableau Интернет арендатора](https://www.tableau.com/).
*   Учетная запись пользователя в Tableau Online с разрешениями администратора.

> [!NOTE]
> Интеграция Azure AD основана на [API для отдыха Tableau Online Rest.](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm) Этот API доступен разработчикам Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Добавить Tableau Онлайн с рынка Azure
Перед настройкой Tableau Online для автоматического предоставления пользовательской подготовки с помощью Azure AD добавьте Tableau Online из Azure Marketplace в список управляемых приложений SaaS.

Чтобы добавить Tableau Online из Marketplace, выполните следующие действия.

1. На [портале Azure](https://portal.azure.com)в навигационном стеле слева выберите **Active Directory Azure.**

    ![Значок активного каталога Azure](common/select-azuread.png)

2. Перейдите к **корпоративным приложениям,** а затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Tableau Online** и выберите **Tableau Online** из панели результатов. Чтобы добавить приложение, **выберите Добавить**.

    ![Tableau Online в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Назначить пользователей Tableau Online

Активный каталог Azure использует концепцию, называемую *назначениями,* чтобы определить, какие пользователи должны получить доступ к выбранным приложениям. В контексте автоматической подготовки пользователей синхронизируются только пользователи или группы, назначенные приложению Azure AD.

Прежде чем настроить и включить автоматическую подготовку пользователей, решите, какие пользователи или группы в Azure AD нуждаются в доступе к Tableau Online. Чтобы назначить этих пользователей или групп в Tableau Online, следуйте инструкциям в [назначить пользователя или группу корпоративному приложению.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Важные советы по назначению пользователей в приложении Tableau Online

*   Мы рекомендуем назначить одного пользователя Azure AD в Tableau Online для тестирования автоматической конфигурации подготовки пользователей. Вы можете назначить дополнительных пользователей или групп ы позже.

*   При назначении пользователя Втаблице онлайн выберите любую действительную роль, конкретную для приложения, если она имеется, в поле диалога назначения. Пользователи с ролью **доступа по умолчанию** исключены из подготовки.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Настройка автоматического предоставления пользовательу для Tableau Online

В этом разделе вы проведете шаги по настройке службы обеспечения AD Azure AD. Используйте его для создания, обновления и отработки пользователей или групп в Tableau Online на основе пользовательских или групповых заданий в Azure AD.

> [!TIP]
> Вы также можете включить SAML основе одного знака для Tableau Online. Следуйте инструкциям в [Tableau Интернет один знак на учебник](tableauonline-tutorial.md). Единый набор может быть настроен независимо от автоматического подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Настройка автоматического подготовки пользователя для Tableau Online в Azure AD

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **Все приложения** > **Tableau Online**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Tableau Online**.

    ![Ссылка Tableau Online в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Подготовка Tableau Online](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Режим онлайн-подготовки таблицы](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **"Учетные данные администратора"** ввейте домен, имя пользователя администратора, пароль администратора и URL-адрес содержимого вашей учетной записи Tableau Online:

   * В поле **домена** заполните поддомен на основе шага 6.

   * В поле **Admin Username** заполните имя пользователя учетной записи админ на вашем Clarizen Tenant. Например, admin@contoso.com.

   * В поле **Admin Password** заполните пароль учетной записи админ, соответствующий имени пользователя.

   * В поле **URL-адреса содержимого** заполните поддомен на основе шага 6.

6. После вступления в административную учетную запись Tableau Online вы можете получить значения для **URL-адреса домена** и **содержимого** по URL-адресу страницы администратора.

    * Скопировать **домен** для учетной записи Tableau Online можно из этой части URL-адреса:

        ![Онлайн домен Tableau](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **URL-адрес содержимого** для вашей учетной записи Tableau Online можно скопировать из этого раздела. Это значение, определяемое при настройке учетной записи. В этом примере используется значение "contoso":

        ![Таблица Онлайн Содержание URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Ваш **домен** может отличаться от того, который показан здесь.

7. После заполнения коробок, отображаемых в шаге 5, выберите **тестовое соединение,** чтобы убедиться, что Azure AD может подключиться к Tableau Online. Если подключение не удается, убедитесь, что ваша учетная запись Tableau Online имеет разрешения администратора и повторите попытку.

    ![Соединение теста Tableau online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. В поле **электронной почты уведомления** введите адрес электронной почты человека или группы для получения уведомлений об ошибке. Выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой** флажка.

    ![Таблица Онлайн Уведомление Email](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Нажмите кнопку **Сохранить**.

10. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Tableau Online**.

    ![Синхронизация пользователей Tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с Tableau Online в разделе **Атрибут карт.** Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Tableau Online для операций обновления. Чтобы сохранить все изменения, выберите **Сохранить**.

    ![Таблица Онлайн соответствия атрибуты пользователя](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. В разделе **Сопоставления** выберите **Синхронизировать группы Azure Active Directory с Tableau Online**.

    ![Синхронизация группы Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Просмотрите атрибуты группы, синхронизированные с Azure AD с Tableau Online в разделе **АтрибутКарты.** Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Tableau Online для операций обновления. Чтобы сохранить все изменения, выберите **Сохранить**.

    ![Таблица Онлайн соответствия групповых атрибутов](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Для настройки фильтров для обсажания следуйте инструкциям в [учебнике по отслеживанию фильтров.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Чтобы включить службу предоставления услуг Azure AD для Tableau Online в разделе **«Настройки»,** измените **статус «Обеспечение»** **на On**.

    ![Tableau Онлайн Обеспечение Статус](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Определите пользователей или группы, которые вы хотите предоставить Tableau Online. В разделе **Настройки** выберите значения, которые вы хотите в **области области.**

    ![Таблица Онлайн Сфера](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Когда вы будете готовы к предоставлению, выберите **Сохранить**.

    ![Таблица Интернет Сохранить](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Эта операция запускает начальную синхронизацию всех пользователей или групп, определенных в **разделе Область** в разделе **Настройки.** Начальная синхронизация занимает больше времени, чем более поздняя синхронизация. Они возникают примерно каждые 40 минут при запуске службы обеспечения AD Azure AD. 

Раздел **«Детали синхронизации»** можно использовать для мониторинга прогресса и отслеживания ссылок на отчет о деятельности подготовки. В отчете описаны все действия, выполняемые службой предоставления AD Azure AD в Tableau Online.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
