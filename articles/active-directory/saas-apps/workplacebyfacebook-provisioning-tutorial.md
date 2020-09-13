---
title: Руководство по настройке Workplace by Facebook для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Workplace by Facebook.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: 4b4953cbca4d08e911fd043413387a1602bd8e08
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006097"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Руководство по настройке Workplace by Facebook для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить в Facebook и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп на [рабочем месте](https://work.workplace.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей на рабочем месте по Facebook
> * Удаление пользователей на рабочем месте с помощью Facebook, если им больше не нужен доступ
> * Обеспечение синхронизации атрибутов пользователей между Azure AD и рабочей областью Facebook
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) на рабочее место с помощью Facebook (рекомендуется)

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор)
* подписка Workplace by Facebook с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и рабочей](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)областью Facebook.

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка рабочей области с помощью Facebook для поддержки подготовки с помощью Azure AD

Перед настройкой и включением службы подготовки необходимо решить, какие пользователи или группы в Azure AD представляют пользователей, которым требуется доступ к приложению Workplace by Facebook. Приняв это решение, можно будет назначить этих пользователей для приложения Workplace by Facebook, выполнив следующие действия.

*   Рекомендуется назначить одного пользователя Azure AD в Workplace by Facebook для тестирования конфигурации подготовки. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя Workplace by Facebook необходимо выбрать допустимую роль пользователя. Роль "Доступ по умолчанию" не работает для подготовки.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление рабочей области с помощью Facebook из коллекции приложений Azure AD

Добавьте рабочую область с помощью Facebook из коллекции приложений Azure AD, чтобы начать управление подготовкой на рабочее место с помощью Facebook. Если вы ранее настроили на рабочее место с помощью Facebook для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп на рабочем месте с помощью Facebook необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Workplace by Facebook**.

    ![Ссылка на Workplace by Facebook в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "Подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** щелкните **авторизовать**. Вы будете перенаправлены на страницу авторизации Facebook на рабочем месте. Введите имя вашего рабочего места в Facebook и нажмите кнопку **продолжить** . Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключаться к рабочему месту с помощью Facebook. В случае сбоя подключения убедитесь, что учетная запись вашего рабочего места имеет разрешения администратора, и повторите попытку.

    ![Подготовка](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![Авторизация](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с рабочей**областью Facebook.

9. Ознакомьтесь с атрибутами пользователей, которые синхронизированы из Azure AD, с рабочей областью по Facebook в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Workplace by Facebook для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что на рабочем месте в API Facebook поддерживается фильтрация пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |displayName|Строка|
   |active|Логическое|
   |title|Логическое значение|
   |emails[type eq "work"].value|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |name.formatted|Строка|
   |addresses[type eq "work"].formatted|Строка|
   |addresses[type eq "work"].streetAddress|Строка|
   |addresses[type eq "work"].locality|Строка|
   |addresses[type eq "work"].region|Строка|
   |addresses[type eq "work"].country|Строка|
   |addresses[type eq "work"].postalCode|Строка|
   |адреса [Введите EQ "Other"]. форматированный|Строка|
   |phoneNumbers[type eq "work"].value|Строка|
   |phoneNumbers[type eq "mobile"].value|Строка|
   |phoneNumbers[type eq "fax"].value|Строка|
   |externalId|Строка|
   |preferredLanguage|Строка|
   |urn: scim: схемы: расширение: Enterprise: 1.0. Manager|Строка|
   |urn: scim: схемы: расширение: корпоративный: 1.0. Department|Строка|
   |urn: scim: схемы: расширение: Enterprise: 1.0. деление|Строка|
   |urn: scim: схемы: расширение: Enterprise: 1.0. Организация|Строка|
   |urn: scim: схемы: расширение: Enterprise: 1.0. costCenter|Строка|
   |urn: scim: схемы: расширение: Enterprise: 1.0. Емплойинумбер|Строка|
   |urn: scim: schemas: расширение: Facebook: auth_method: 1.0: auth_method|Строка|
   |urn: scim: schemas: расширение: Facebook: оказался: 1.0. is_frontline|Логическое значение|
   |urn: scim: schemas: расширение: Facebook: старттермдатес: 1.0. startDate|Целое число|


10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для рабочих мест в Facebook, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и (или) группы, которые вы хотите подготавливать к работе с помощью Facebook, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
*  Если вы видите, что пользователь неудачно создал событие журнала аудита с кодом "1789003", это означает, что пользователь из непроверенного домена.

## <a name="change-log"></a>Журнал изменений

* 09/10/2020 — добавлена поддержка корпоративных атрибутов "деление", "Организация", "costCenter" и "Емплойинумбер". Добавлена поддержка пользовательских атрибутов "startDate", "auth_method" и "оказался"

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
