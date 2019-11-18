---
title: Учебник. Настройка Oracle Fusion ERP для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в Oracle Fusion ERP.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 73991efa2e98ff033987f1ce172d24fe3ecddb96
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144581"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>Руководство по настройке системы ERP Fusion в Oracle для автоматической подготовки пользователей

Цель этого учебника — продемонстрировать шаги, которые необходимо выполнить в Oracle Fusion ERP и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и (или) групп в Oracle Fusion ERP.

> [!NOTE]
>  В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Сейчас этот соединитель доступен в режиме предварительной версии. Дополнительные сведения об общих Microsoft Azure условиях использования предварительных версий функций см. в разделе Дополнительные [условия использования для предварительного просмотра Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="prerequisites"></a>предварительным требованиям

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* клиент Azure AD;
* [Клиент ERP слиянием Oracle](https://www.oracle.com/applications/erp/).
* Учетная запись пользователя в Oracle Fusion ERP с разрешениями администратора.

## <a name="assign-users-to-oracle-fusion-erp"></a>Назначение пользователей для ERP слиянием Oracle 
Azure Active Directory использует концепцию, называемую назначениями, чтобы определить, какие пользователи должны получать доступ к выбранным приложениям. В контексте автоматической подготовки учетных записей пользователей синхронизируются только пользователи и группы, назначенные приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей следует решить, каким пользователям и (или) группам в Azure AD нужен доступ к ERP-данным Oracle Fusion. После этого вы можете назначить этих пользователей и (или) группы в Oracle Fusion ERP, следуя приведенным ниже инструкциям.
 
* [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Важные советы по назначению пользователей в ERP Fusion в Oracle 

 * Рекомендуется назначить одного пользователя Azure AD ERP Oracle Fusion для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

* При назначении пользователя в Oracle Fusion ERP необходимо выбрать в диалоговом окне назначения любую допустимую роль конкретного приложения (если она доступна). Пользователи с ролью доступа по умолчанию исключаются из подготовки.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>Настройка Oracle Fusion ERP для подготовки

Перед настройкой Oracle Fusion ERP для автоматической подготовки пользователей с помощью Azure AD необходимо включить подготовку SCIM в Oracle Fusion ERP.

1. Вход в [консоль администрирования системы Oracle Fusion ERP](https://cloud.oracle.com/sign-in)

2. Щелкните навигатор в левом верхнем углу. В разделе **средства**выберите **консоль безопасности**.

    ![Добавление SCIM в Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. Перейдите к разделу **Пользователи**.
    
    ![Добавление SCIM в Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Сохраните имя пользователя и пароль для учетной записи администратора, которая будет использоваться для входа в консоль администрирования Oracle Fusion ERP. Эти значения необходимо указать в полях **имя пользователя** и **пароль** администратора на вкладке Подготовка приложения Oracle Fusion ERP на портал Azure.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>Добавление Oracle Fusion ERP из коллекции

Чтобы настроить Oracle Fusion ERP для автоматической подготовки пользователей с помощью Azure AD, необходимо добавить Oracle Fusion ERP из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Oracle Fusion ERP из коллекции приложений Azure AD, выполните следующие действия.**

1. В **[портал Azure](https://portal.azure.com)** на панели навигации слева выберите **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](common/select-azuread.png)

2. Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

3. Чтобы добавить новое приложение, нажмите кнопку **новое приложение** в верхней части области.

    ![Кнопка "Новое приложение"](common/add-new-app.png)

4. В поле поиска введите **Oracle Fusion ERP**, выберите **Oracle Fusion ERP** на панели результатов.

    ![Oracle Fusion ERP в списке результатов](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Настройка автоматической подготовки пользователей для ERP в Oracle Fusion 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в ERP-данных Azure Fusion на основе назначений пользователей и (или) групп.

> [!TIP]
> Вы также можете включить единый вход на основе SAML для Oracle Fusion ERP, следуя инструкциям в [руководстве по единому входу в систему Oracle Fusion ERP](oracle-fusion-erp-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две функции дополняют друг друга.

> [!NOTE]
> Дополнительные сведения о конечной точке SCIM в ERP слиянием Oracle см. в статье [REST API для общих функций в облаке приложений Oracle](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html).

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Fuze в Azure AD, сделайте следующее:

1. Войдите на [портале Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Oracle Fusion ERP**.

    ![Ссылка на Oracle Fusion ERP в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` в поле **URL-адрес клиента**. Введите имя пользователя и пароль администратора, полученные ранее, в поля **имя пользователя** и **пароль** администратора. Щелкните **Проверка подключения** между Azure AD и ERP Fusion в Oracle. 

    ![Добавление SCIM в Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Выберите команду **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Oracle Fusion ERP**.

    ![Добавление SCIM в Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. Проверьте пользовательские атрибуты, которые синхронизированы из Azure AD в Oracle Fusion ERP в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Oracle Fusion ERP для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Добавление SCIM в Oracle Fusion ERP](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы с Oracle Fusion ERP**.

    ![Сопоставления групп ERP слияний Oracle](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD в Oracle Fusion ERP в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в Oracle Fusion ERP для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Атрибуты группы ERP Fusion для Oracle](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для ERP слияний Oracle, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать в Oracle Fusion ERP, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

    После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов по подготовке, в которых описаны все действия, выполняемые службой подготовки Azure AD в Oracle Fusion ERP.

    Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* В Oracle Fusion ERP поддерживается только обычная проверка подлинности для конечной точки SCIM.
* ERP Fusion в Oracle не поддерживает подготовку групп.
* Роли в Oracle Fusion ERP сопоставляются с группами в Azure AD. Чтобы назначить роли пользователям в Oracle Fusion ERP из Azure AD, вам потребуется назначить пользователям нужные группы Azure AD, имена которых указаны после ролей в Oracle Fusion ERP.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
