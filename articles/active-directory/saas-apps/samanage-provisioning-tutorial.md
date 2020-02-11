---
title: Руководство по настройке Samanage для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отмены предоставления учетных записей пользователей в Samanage.
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
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060508"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Руководство по настройке Samanage для автоматической подготовки пользователей

В этом руководстве описываются действия, которые необходимо выполнить в Samanage и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отмены наполнения пользователей и групп для Samanage.

> [!NOTE]
> В этом руководстве описывается соединитель, созданный на основе службы подготовки пользователей Azure AD. Сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. [в статье Автоматизация подготовки пользователей и ее отработки в приложения SaaS (программное обеспечение как услуга) с Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>предварительные требования

В сценарии, описанном в этом учебнике, предполагается, что у вас есть:

* Клиент Azure AD.
* [Клиент Samanage](https://www.samanage.com/pricing/) с профессиональным пакетом.
* Учетная запись пользователя в Samanage с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [API-интерфейса Samanage](https://www.samanage.com/api/). Этот API доступен для разработчиков Samanage для учетных записей с профессиональным пакетом.

## <a name="add-samanage-from-the-azure-marketplace"></a>Добавление Samanage из Azure Marketplace

Перед настройкой Samanage для автоматической подготовки пользователей с помощью Azure AD добавьте Samanage из Azure Marketplace в список управляемых приложений SaaS.

Чтобы добавить Samanage из Marketplace, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)в области навигации слева выберите **Azure Active Directory**.

    ![Значок Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, в верхней части диалогового окна выберите **Новое приложение**.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. В поле поиска введите **Samanage** и выберите **Samanage** на панели результатов. Чтобы добавить приложение, нажмите кнопку **Добавить**.

    ![Samanage в списке результатов](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Назначение пользователей Samanage

Azure Active Directory использует концепцию, называемую *назначениями* , чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только те пользователи или группы, которые были назначены приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей решите, каким пользователям или группам в Azure AD нужен доступ к Samanage. Чтобы назначить этих пользователей или группы Samanage, следуйте инструкциям в статье [Назначение пользователя или группы корпоративному приложению](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Важные советы по назначению пользователей в Samanage

*    Сейчас роли Samanage автоматически заполняются в пользовательском интерфейсе портал Azure. Прежде чем назначать роли Samanage пользователям, убедитесь, что начальная синхронизация выполнена для Samanage, чтобы получить последние роли в клиенте Samanage.

*    Мы рекомендуем назначить одного пользователя Azure AD для Samanage, чтобы протестировать начальную конфигурацию автоматической подготовки пользователей. После успешного выполнения тестов можно назначить дополнительных пользователей и группы позднее.

*    При назначении пользователя в Samanage выберите любую допустимую роль конкретного приложения, если она доступна, в диалоговом окне Назначение. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Настройка автоматической подготовки пользователей в Samanage

В этом разделе описывается процедура настройки службы подготовки Azure AD. Используйте его для создания, обновления и отключения пользователей или групп в Samanage на основе назначений пользователей или групп в Azure AD.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Samanage. Следуйте инструкциям в руководстве по [единому входу в Samanage](samanage-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Samanage в Azure AD

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения** > **все приложения** > **Samanage**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Samanage**.

    ![Ссылка на Samanage в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** Samanage и **маркер секрета**. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Samanage. Если установить подключение не удалось, убедитесь, что у учетной записи Samanage есть разрешения администратора, и повторите попытку.

    ![Samanage тестовое подключение](./media/samanage-provisioning-tutorial/provisioning.png)

6. В поле e-mail **Notification (уведомление** ) введите адрес электронной почты пользователя или группы для получения уведомлений об ошибках подготовки. Установите флажок **отправлять уведомление по электронной почте при возникновении сбоя** .

    ![Сообщение электронной почты с уведомлением Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Щелкните **Сохранить**.

8. В разделе **Сопоставления** выберите **Synchronize Azure Active Directory Users to Samanage** (Синхронизировать пользователей Azure Active Directory с Samanage).

    ![Синхронизация пользователей Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

9. В разделе **Сопоставления атрибутов** просмотрите пользовательские атрибуты, синхронизированные из Azure AD в Samanage. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Samanage для операций обновления. Чтобы сохранить изменения, нажмите кнопку **сохранить**.

    ![Samanage, соответствующие атрибутам пользователя](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Чтобы включить сопоставление групп, в разделе **Сопоставления** выберите **Synchronize Azure Active Directory Groups to Samanage** (Синхронизировать группы Azure Active Directory с Samanage).

    ![Синхронизация группы Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. В столбце **Включено** выберите значение **Да** для синхронизации групп. В разделе **Сопоставления атрибутов** просмотрите атрибуты группы, синхронизированные из Azure AD в Samanage. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Samanage для операций обновления. Чтобы сохранить изменения, нажмите кнопку **сохранить**.

    ![Samanage совпадающие атрибуты группы](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Чтобы настроить фильтры области, следуйте инструкциям в [руководстве по фильтрации областей](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для Samanage, в разделе **Параметры** измените значение параметра **состояние подготовки** на **включено**.

    ![Состояние подготовки Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Определите пользователей или группы, которые вы хотите подготавливать для Samanage. В разделе **Параметры** выберите нужные значения в **области**. При выборе параметра **синхронизировать все пользователи и группы** следует учитывать ограничения, описанные в следующем разделе "ограничения соединителей".

    ![Область Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Когда вы будете готовы к подготовке к работе, нажмите кнопку **сохранить**.

    ![Samanage сохранить](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Эта операция запускает начальную синхронизацию всех пользователей или групп, определенных в **области** , в разделе **параметров** . Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Они происходят примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам в отчет о действиях по подготовке. В отчете описаны все действия, выполняемые службой подготовки Azure AD в Samanage.

Сведения о журналах подготовки Azure AD см. в руководстве [по отчетам об автоматической подготовке учетных записей пользователей](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

Если выбран параметр **синхронизировать все пользователи и группы** и задано значение атрибута Samanage **roles** , то значение в поле **по умолчанию, если значение null (необязательное)** должно быть выражено в следующем формате:

- {"displayName": "role"}, где по умолчанию используется значение Role.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Managing user account provisioning for enterprise apps in the Azure portal](../app-provisioning/configure-automatic-user-provisioning-portal.md) (Управление подготовкой учетных записей пользователей для корпоративных приложений на портале Azure)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
