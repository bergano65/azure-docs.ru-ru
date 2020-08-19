---
title: Руководство по Настройка MediusFlow для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить автоматическую подготовку и учетных записей пользователей и ее отмену из Azure AD в MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: e8a64b3f42e52363f70d536963594bb6083a0992
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548078"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Руководство по Настройка MediusFlow для автоматической подготовки пользователей

Это руководство описывает действия, которые необходимо выполнить в MediusFlow и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически осуществляет и подготовку пользователей и групп и ее отмену для [MediusFlow](https://www.mediusflow.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в MediusFlow.
> * Удаление пользователей из MediusFlow, когда им больше не нужен доступ.
> * Синхронизация атрибутов пользователей между Azure AD и MediusFlow.
> * Подготовка групп и членства в группах в MediusFlow.
> * Единый вход в MediusFlow (рекомендуется).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) настраивать подготовку (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* Активная подписка MediusFlow с контролем качества или производственным арендатором.
* Учетная запись пользователя в MediusFlow с правами администратора на доступ, которая может выполнять настройку в MediusFlow.
* Компании, добавленные в арендатор MediusFlow, где должны быть подготовлены пользователи.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные следует [сопоставить между Azure AD и MediusFlow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка MediusFlow для поддержки подготовки с помощью Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Активация приложения Microsoft 365 в MediusFlow
Для начала включите доступ в MediusFlow для имени входа Azure AD и функции настройки через Azure AD, сделав следующее.

#### <a name="user-login"></a>Вход пользователя
Чтобы включить поток входа в Microsoft 365 или Azure AD, обратитесь к [This] ( https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) статья.

#### <a name="user-transfer-configuration"></a>Конфигурация передачи пользователей
Сведения о включении портала конфигурации пользователей для подготовки из Azure AD см. в [этой](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) статье.

#### <a name="configure-user-provisioning"></a>Настроить подготовку учетных записей пользователей

1.  Войдите в [консоль администрирования MediusFlow](https://office365.cloudapp.mediusflow.com/), указав идентификатор арендатора.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. Проверьте подключение к MediusFlow.

    ![Проверка](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Укажите идентификатор арендатора Azure AD.

    ![Предоставление идентификатора арендатора](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Сведения о том, как его получить, см. в [разделе часто задаваемых вопросов](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id).

4. Сохраните конфигурацию.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. Выберите подготовку пользователей и щелкните **ОК**.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. Щелкните **Создать секретный ключ**. Скопируйте и сохраните это значение. Его нужно будет ввести в поле **Секретный токен** на вкладке **Подготовка** для приложения MediusFLow на портале Azure.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. Нажмите кнопку **ОК**.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. Чтобы получить пользователей, импортированных с помощью предварительно определенного набора ролей, компаний и других общих конфигураций в MediusFlow, необходимо сначала настроить его. Для начала добавьте конфигурацию, щелкнув **Добавить конфигурацию**.

    ![Настройка пользователей](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. Предоставьте параметры по умолчанию для пользователей. В этом представлении можно задать атрибут по умолчанию. Если вас устраивают стандартные значения, можно просто указать допустимое название компании. Так как эти параметры конфигурации извлекаются из Mediusflow, их нужно сначала настроить. Дополнительные сведения см. в разделе **Предварительные условия** этой статьи.

    ![Настройка пользователей](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. Щелкните **Сохранить**, чтобы сохранить конфигурацию пользователей.

    ![Настройка пользователей](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. Чтобы получить ссылку для подготовки пользователей, щелкните **Копировать ссылку SCIM**. Скопируйте и сохраните это значение. Его нужно будет ввести в поле **URL-адрес арендатора** на вкладке **Подготовка** для приложения MediusFLow на портале Azure.
 
    ![Настройка пользователей](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление MediusFlow из коллекции приложений Azure AD

Добавьте MediusFlow из коллекции приложений Azure AD, чтобы начать управление подготовкой в MediusFlow. Если вы ранее настроили MediusFlow для единого входа, вы можете использовать это же приложение. Но мы рекомендуем создать отдельное приложение для исходной проверки интеграции. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили определить пользователей на основе назначения, выполните следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили определить пользователей только на основе атрибутов пользователя или группы, примените фильтр области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для MediusFlow необходимо выбрать роль, отличающуюся от роли **Доступ по умолчанию**. Пользователи с такой ролью исключаются из подготовки и будут помечены в журналах подготовки как не имеющие разрешений. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Шаг 5. Настройка автоматической подготовки пользователей в MediusFlow 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в MediusFlow, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **MediusFlow**.

    ![Ссылка на MediusFlow в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "Подготовка"](common/provisioning-automatic.png)

5. В разделе **Учетные данные администратора** введите в поле **URL-адрес арендатора** значение URL-адреса, полученное ранее. Введите значение секретного токена, полученное ранее на шаге **Секретный токен**. Щелкните **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к MediusFlow. Если установить подключение не удалось, убедитесь, что у учетной записи MediusFlow есть разрешения администратора, и повторите попытку.

      ![Подготовка](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. В поле **Электронная почта для уведомлений** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с MediusFlow**.

9. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизируются из Azure AD в MediusFlow. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в MediusFlow в операциях обновления. Если вы решили изменить [целевой атрибут сопоставления](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), сначала убедитесь, что API MediusFlow поддерживает фильтрацию пользователей по этому атрибуту. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |emails[type eq "work"].value|Строка|
   |name.displayName|Строка|
   |active|Логическое|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |name.formatted|Строка|
   |externalID|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Справочник|


10. В разделе **Сопоставления** выберите **Синхронизировать группы Azure Active Directory с MediusFlow**.

11. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизируются из Azure AD в MediusFlow. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления групп в MediusFlow в операциях обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |attribute|Тип|
      |---|---|
      |displayName|Строка|
      |externalID|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для MediusFlow, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы для подготовки в MediusFlow, выбрав нужные значения в поле **Область** в разделе **Параметры**.

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
Настроив подготовку, используйте следующие ресурсы для мониторинга развертывания:

1. Используйте [Журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки находится в неработоспособном состоянии, приложение перейдет в режим карантина. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
