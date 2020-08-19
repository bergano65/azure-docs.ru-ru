---
title: Руководство. Настройка 15Five для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в 15Five.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: b42833056d6c9c0e6053dbf34c7de17b4136a797
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539187"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Учебник. Настройка 15Five для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в 15Five и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и групп в [15Five](https://www.15five.com/pricing/). Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory.

> [!NOTE]
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в 15Five
> * Удалить пользователей в 15Five, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и 15Five
> * Подготавливайте группы и членство в группах в 15Five
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/15five-tutorial) в 15Five (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) .
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор).
* [Клиент 15Five](https://www.15five.com/pricing/).
* Учетная запись пользователя в 15Five с разрешениями администратора.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и 15Five](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка 15Five для поддержки подготовки с помощью Azure AD

Перед настройкой 15Five для автоматической подготовки пользователей с помощью Azure AD необходимо включить подготовку SCIM для 15Five.

1. Войдите в [консоль администратора 15Five](https://my.15five.com/). Перейдите к **функции > интеграции**.

    ![Консоль администрирования 15Five](media/15five-provisioning-tutorial/integration.png)

2.  Щелкните **SCIM 2,0**.

    ![Консоль администрирования 15Five](media/15five-provisioning-tutorial/image00.png)

3.  Перейдите к **scim integration > создать токен OAuth**.

    ![15Five добавить SCIM](media/15five-provisioning-tutorial/image02.png)

4.  Скопируйте значения для **базового URL-адреса SCIM 2,0** и **маркера доступа**. Это значение будет указано в поле **URL-адрес клиента** и **секретный токен** на вкладке подготовка приложения 15Five в портал Azure.
    
    ![15Five добавить SCIM](media/15five-provisioning-tutorial/image03.png)

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление 15Five из коллекции приложений Azure AD

Добавьте 15Five из коллекции приложений Azure AD, чтобы начать управление подготовкой в 15Five. Если вы ранее настроили 15Five для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для 15Five необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>Шаг 5. Настройка автоматической подготовки пользователей в 15Five 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в 15Five на основе назначений пользователей и групп в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для 15Five в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **15Five**.

    ![Ссылка на 15Five в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "Подготовка"](common/provisioning-automatic.png)

5.  В разделе Учетные данные администратора введите **базовый URL-адрес SCIM 2,0 и значения маркера доступа** , полученные ранее в полях **URL-адрес клиента** и **секретный токен** соответственно. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к 15Five. Если подключение не выполняется, убедитесь, что у учетной записи 15Five есть разрешения администратора, и повторите попытку.

    ![URL-адрес клиента + токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с 15Five**.

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в 15Five в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в 15Five для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.


   |attribute|Тип|
   |---|---|
   |active|Логическое|
   |title|Строка|
   |emails[type eq "work"].value|Строка|
   |userName|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |externalId|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Справочник|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Строка|
   |urn: IETF: params: scim: схемы: расширение: 15Five: 2.0: пользователь: расположение|Строка|
   |urn: IETF: params: scim: схемы: расширение: 15Five: 2.0: User: startDate|Строка|

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы с 15Five**.

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD в 15Five в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в 15Five для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |attribute|Тип|
      |---|---|
      |externalId|Строка|
      |displayName|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для 15Five, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать к 15Five, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

    После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут.

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  
    
## <a name="connector-limitations"></a>Ограничения соединителя

* 15Five не поддерживает обратимое удаление для пользователей.

## <a name="change-log"></a>Журнал изменений

* 06/16/2020 — добавлена поддержка "диспетчера" атрибута расширения предприятия и пользовательских атрибутов "расположение" и "Дата начала" для пользователей.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте, как просматривать журналы и получать отчеты о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md).
