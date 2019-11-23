---
title: 'Tutorial: Configure Infor CloudSuite for automatic user provisioning with Azure Active Directory | Microsoft Docs'
description: Learn how to configure Azure Active Directory to automatically provision and de-provision user accounts to Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: a1a274d9a20a5c7e487536e2850f253c3230c0cc
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74408720"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Tutorial: Configure Infor CloudSuite for automatic user provisioning

The objective of this tutorial is to demonstrate the steps to be performed in Infor CloudSuite and Azure Active Directory (Azure AD) to configure Azure AD to automatically provision and de-provision users and/or groups to Infor CloudSuite.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Технические условия

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [A Infor CloudSuite tenant](https://www.infor.com/products/infor-os)
* A user account in Infor CloudSuite with Admin permissions.

## <a name="assigning-users-to-infor-cloudsuite"></a>Assigning users to Infor CloudSuite

Azure Active Directory uses a concept called *assignments* to determine which users should receive access to selected apps. In the context of automatic user provisioning, only the users and/or groups that have been assigned to an application in Azure AD are synchronized.

Before configuring and enabling automatic user provisioning, you should decide which users and/or groups in Azure AD need access to Infor CloudSuite. Once decided, you can assign these users and/or groups to Infor CloudSuite by following the instructions here:
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Important tips for assigning users to Infor CloudSuite

* It is recommended that a single Azure AD user is assigned to Infor CloudSuite to test the automatic user provisioning configuration. Дополнительные пользователи и/или группы можно назначить позднее.

* When assigning a user to Infor CloudSuite, you must select any valid application-specific role (if available) in the assignment dialog. Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Set up Infor CloudSuite for provisioning

1. Sign in to your [Infor CloudSuite Admin Console](https://www.infor.com/customer-center). Click on the user icon and then navigate to **user management**.

    ![Infor CloudSuite Admin Console](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Click on the menu icon on the left top corner of the screen. Click on **Manage**.

    ![Infor CloudSuite Add SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Navigate to **SCIM Accounts**.

    ![Infor CloudSuite SCIM Account](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Add an admin user by clicking on the plus icon. Provide a **SCIM Password** and type the same password under **Confirm Password**. Click on the folder icon to save the password. You will then see an **User Identifier** generated for the admin user.

    ![Infor CloudSuite Admin user](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Infor CloudSuite password](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Infor CloudSuite identifier](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. To generate the bearer token, copy the **User Identifier** and **SCIM Password**. Paste them in notepad++ separated by a colon. Encode the string value by navigating to **Plugins > MIME Tools > Basic64 Encode**. 

    ![Infor CloudSuite identifier](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Copy the bearer token. This value will be entered in the Secret Token field in the Provisioning tab of your Infor CloudSuite application in the Azure portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Add Infor CloudSuite from the gallery

Before configuring Infor CloudSuite for automatic user provisioning with Azure AD, you need to add Infor CloudSuite from the Azure AD application gallery to your list of managed SaaS applications.

**To add Infor CloudSuite from the Azure AD application gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)** , in the left navigation panel, select **Azure Active Directory**.

    ![Кнопка Azure Active Directory](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. To add a new application, select the **New application** button at the top of the pane.

    ![Кнопка "Создать приложение"](common/add-new-app.png)

4. In the search box, enter **Infor CloudSuite**, select **Infor CloudSuite** in the results panel, and then click the **Add** button to add the application.

    ![Infor CloudSuite в списке результатов](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Configuring automatic user provisioning to Infor CloudSuite 

This section guides you through the steps to configure the Azure AD provisioning service to create, update, and disable users and/or groups in Infor CloudSuite based on user and/or group assignments in Azure AD.

> [!TIP]
> You may also choose to enable SAML-based single sign-on for Infor CloudSuite , following the instructions provided in the [Infor CloudSuite Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

> [!NOTE]
> To learn more about Infor CloudSuite's SCIM endpoint, refer [this](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>To configure automatic user provisioning for Infor CloudSuite in Azure AD:

1. Войдите на [портале Azure](https://portal.azure.com). Select **Enterprise Applications**, then select **All applications**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Infor CloudSuite**.

    ![Ссылка на Infor CloudSuite в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Provisioning tab](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Provisioning tab](common/provisioning-automatic.png)

5. Under the **Admin Credentials** section, input `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` in **Tenant URL**. Input the bearer token value retrieved earlier in **Secret Token**. Click **Test Connection** to ensure Azure AD can connect to Infor CloudSuite. If the connection fails, ensure your Infor CloudSuite account has Admin permissions and try again.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. В нижней части страницы нажмите кнопку **Save**.

8. Under the **Mappings** section, select **Synchronize Azure Active Directory Users to Infor CloudSuite**.

    ![Infor CloudSuite User Mappings](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Review the user attributes that are synchronized from Azure AD to Infor CloudSuite in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the user accounts in Infor CloudSuite for update operations. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Infor CloudSuite User Attributes](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. Under the **Mappings** section, select **Synchronize Azure Active Directory Groups to Infor CloudSuite**.

    ![Infor CloudSuite Group Mappings](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Review the group attributes that are synchronized from Azure AD to Infor CloudSuite in the **Attribute Mapping** section. The attributes selected as **Matching** properties are used to match the groups in Infor CloudSuite for update operations. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Infor CloudSuite Group Attributes](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. To enable the Azure AD provisioning service for Infor CloudSuite, change the **Provisioning Status** to **On** in the **Settings** section.

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Define the users and/or groups that you would like to provision to Infor CloudSuite by choosing the desired values in **Scope** in the **Settings** section.

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. You can use the **Synchronization Details** section to monitor progress and follow links to provisioning activity report, which describes all actions performed by the Azure AD provisioning service on Infor CloudSuite.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)