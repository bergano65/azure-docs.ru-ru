---
title: Руководство. Настройка Алертмедиа для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в Алертмедиа.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: a5df0dd7-05a3-4744-9d51-ec33e89a934f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2020
ms.author: Zhchia
ms.openlocfilehash: 98cca99ab0e088bbae047fa64ec52429e531dfed
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096093"
---
# <a name="tutorial-configure-alertmedia-for-automatic-user-provisioning"></a>Учебник. Настройка Алертмедиа для автоматической подготовки пользователей

В этом руководстве описываются действия, которые необходимо выполнить в Алертмедиа и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп для [алертмедиа](https://www.alertmedia.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в Алертмедиа
> * Удалить пользователей в Алертмедиа, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и Алертмедиа
> * Подготавливайте группы и членство в группах в Алертмедиа
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial) в алертмедиа (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) настраивать подготовку (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* [Клиент алертмедиа](https://dashboard.alertmedia.com/#/login).
* Учетная запись пользователя в Алертмедиа с разрешениями администратора для настройки интеграции API.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и алертмедиа](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-alertmedia-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка Алертмедиа для поддержки подготовки с помощью Azure AD

1. Войдите в учетную запись Алертмедиа. Перейдите к **API > компании** .
2. Щелкните **Добавить новый** .
3. Выберите, чтобы присвоить вашей **интеграции API** имя, которое поможет легко понять, где используются ключи.
4. Выберите администратора, с которым вы хотите связать интеграцию.
5. Нажмите кнопку **создать ключи** и **сохранить** .
6. Скопируйте и сохраните **токен клиента** из интеграции. Он используется в качестве **токена секрета** на вкладке Подготовка приложения алертмедиа в портал Azure.


## <a name="step-3-add-alertmedia-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Алертмедиа из коллекции приложений Azure AD

Добавьте Алертмедиа из коллекции приложений Azure AD, чтобы начать управление подготовкой в Алертмедиа. Если вы ранее настроили Алертмедиа для единого входа, можно использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для Алертмедиа необходимо выбрать роль, отличную от **доступа по умолчанию** . Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-alertmedia"></a>Шаг 5. Настройка автоматической подготовки пользователей в Алертмедиа 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-alertmedia-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Алертмедиа в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения** , а затем **Все приложения** .

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **AlertMedia** .

    ![Ссылка на Алертмедиа в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка** .

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Автоматическая вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** алертмедиа в виде одного из следующих.
      * (нет пользовательского домена) https://docs.gitlab.com/ee/api/scim.html

      * (личный домен) https://developer.github.com/v3/scim/

      Введите **секретный токен** , полученный ранее на шаге 2. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к алертмедиа. Если подключение не выполняется, убедитесь, что у учетной записи Алертмедиа есть разрешения администратора, и повторите попытку.

      ![Токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое** .

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить** .

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с алертмедиа** .

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Алертмедиа в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в алертмедиа для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что API алертмедиа поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить** , чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |active|Логическое|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: first_name|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: last_name|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: адрес электронной почты|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: email2|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: email3|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: Title|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: mobile_phone|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: mobile_phone_post_dial|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: mobile_phone2|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: mobile_phone2_post_dial|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: mobile_phone3|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: mobile_phone3_post_dial|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: home_phone|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: home_phone_post_dial|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: office_phone|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: office_phone_post_dial|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: адрес|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: Address2|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: город|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: State|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: страна|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: ZipCode|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: Примечания|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: customer_user_id|Строка|
   |urn: IETF: params: scim: схемы: Extension: алертмедиа: 2.0: CustomAttribute: пользователь: user_type|Строка|

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы с алертмедиа** .

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD в Алертмедиа в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в алертмедиа для операций обновления. Нажмите кнопку **Сохранить** , чтобы зафиксировать все изменения.

      |attribute|Тип|
      |---|---|
      |displayName|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для Алертмедиа, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать к Алертмедиаалертмедиа, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить** .

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры** . Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

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
