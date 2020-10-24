---
title: Руководство. Настройка синхронизации удостоверений глобального ретранслятора для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в глобальную синхронизацию удостоверений ретранслятора.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 0c4a3bf0-d0a6-4eab-909b-6cf9f9234e4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2020
ms.author: Zhchia
ms.openlocfilehash: c1e2d64bb30b6451e232c85a5892771157d32928
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514407"
---
# <a name="tutorial-configure-global-relay-identity-sync-for-automatic-user-provisioning"></a>Руководство по настройке глобальной синхронизации удостоверений ретранслятора для автоматической подготовки пользователей

В этом руководстве описываются действия, которые необходимо выполнить в глобальной синхронизации удостоверений ретранслятора и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп для [синхронизации удостоверений глобальной ретрансляции](https://portalalpha1.globalrelay.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в глобальной синхронизации удостоверений ретранслятора
> * Удалить пользователей в глобальной синхронизации удостоверений ретранслятора, если им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и синхронизацией глобальной ретрансляции удостоверений
> * Предоставление групп и членств в группах в синхронизации удостоверений глобального ретранслятора

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) настраивать подготовку (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор).

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и синхронизацией удостоверений глобального ретранслятора](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-global-relay-identity-sync-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка синхронизации удостоверений глобального ретранслятора для поддержки подготовки с помощью Azure AD

Чтобы получить URL-адрес клиента, обратитесь к своему представителяу службы синхронизации удостоверений глобального ретранслятора. Это значение будет указано в поле **URL-адрес клиента** на вкладке Подготовка приложения синхронизации удостоверений глобального ретранслятора в портал Azure.

## <a name="step-3-add-global-relay-identity-sync-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление синхронизации удостоверений глобального ретранслятора из коллекции приложений Azure AD

Добавьте синхронизацию удостоверений глобального ретранслятора из коллекции приложений Azure AD, чтобы начать управление подготовкой к синхронизации удостоверений глобальной ретрансляции. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-global-relay-identity-sync"></a>Шаг 5. Настройка автоматической подготовки пользователей для синхронизации удостоверений глобальной ретрансляции 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в приложении синхронизации удостоверений глобального ретранслятора на основе назначений пользователей и групп в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-global-relay-identity-sync-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей для синхронизации удостоверений глобального ретранслятора в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Глобальная синхронизация удостоверений ретранслятора**.

    ![Ссылка синхронизации глобального ретранслятора удостоверений в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Автоматическая вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента**синхронизации удостоверений глобального ретранслятора. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к глобальной службе синхронизации удостоверений. В случае сбоя подключения убедитесь, что у учетной записи синхронизации удостоверений глобальной ретрансляции есть разрешения администратора, и обратитесь к своему партнеру глобального ретранслятора, чтобы устранить проблему.

    ![Кнопка авторизации](media/global-relay-identity-sync-provisioning-tutorial/authorization.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с глобальной синхронизацией удостоверений ретрансляции**.

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в глобальную синхронизацию удостоверений ретранслятора, в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в глобальной синхронизации удостоверений ретранслятора для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что интерфейс API синхронизации удостоверений глобального ретранслятора поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |active|Логическое|
   |displayName|Строка|
   |title|Строка|
   |emails[type eq "work"].value|Строка|
   |preferredLanguage|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |name.formatted|Строка|
   |addresses[type eq "work"].formatted|Строка|
   |addresses[type eq "work"].streetAddress|Строка|
   |emails[type eq "work"].value|Строка|
   |addresses[type eq "work"].locality|Строка|
   |addresses[type eq "work"].region|Строка|
   |addresses[type eq "work"].postalCode|Строка|
   |addresses[type eq "work"].country|Строка|
   |адреса [Введите EQ "Other"]. форматированный|Строка|
   |phoneNumbers[type eq "work"].value|Строка|
   |phoneNumbers[type eq "mobile"].value|Строка|
   |phoneNumbers[type eq "fax"].value|Строка|
   |externalId|Строка|
   |name.honorificPrefix|Строка|
   |имя. Хонорификсуффикс|Строка|
   |nickName|Строка|
   |userType|Строка|
   |локаль|Строка|
   |timezone|Строка|
   |сообщения электронной почты [Type EQ "Home"]. значение|Строка|
   |сообщения электронной почты [Введите EQ "другое"]. значение|Строка|
   |Фоненумберс [Type EQ "Home"]. значение|Строка|
   |Фоненумберс [Введите EQ "другое"]. Value|Строка|
   |Фоненумберс [Type EQ "пейджер"]. Value|Строка|
   |адреса [Type EQ "Home"]. streetAddress|Строка|
   |адреса [Type EQ "Home"]. Localization|Строка|
   |адреса [тип EQ "Домашняя страница"]. регион|Строка|
   |адреса [Type EQ "Home"]. postalCode|Строка|
   |адреса [Введите EQ "Домашняя страница"]. Country|Строка|
   |адреса [Type EQ "Home"]. форматируются|Строка|
   |адреса [Введите EQ "Other"]. streetAddress|Строка|
   |адреса [Введите EQ "Other"]. локальность|Строка|
   |адреса [Введите EQ "Other"]. регион|Строка|
   |адреса [Type EQ "Other"]. postalCode|Строка|
   |адреса [Введите EQ "другое"]. Country|Строка|
   |роли [основной EQ "true"]. Отображение|Строка|
   |роли [основной EQ "true"]. Type|Строка|
   |roles[primary eq "True"].value|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Справочник|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: proxyAddresses|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute1|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute2|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute3|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute4|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute5|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute6|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute7|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute8|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute9|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute10|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute11|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute12|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute13|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute14|Строка|
   |urn: IETF: params: scim: схемы: Extension: Глобалрелай: 2.0: пользователь: extensionAttribute15|Строка|



10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directoryные группы с глобальной синхронизацией удостоверений ретрансляции**.

11. В разделе **сопоставление атрибутов** Проверьте атрибуты группы, которые синхронизированы из Azure AD в глобальную синхронизацию удостоверений ретрансляции. Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в глобальной синхронизации удостоверений ретранслятора для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |attribute|Тип|
      |---|---|
      |displayName|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для синхронизации удостоверений глобального ретранслятора, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать к синхронизации удостоверений глобального ретранслятора, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
