---
title: Руководство. Настройка облака SAP Analytics для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в облаке SAP Analytics.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2020
ms.author: Zhchia
ms.openlocfilehash: 7d9f5792fe3da9f47dd1391d1e7cb0162fd36fad
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92670830"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>Учебник. Настройка SAP Analytics Cloud для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить в SAP Analytics Cloud и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп в [облаке SAP Analytics](https://www.sapanalytics.cloud/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в SAP Analytics Cloud
> * Удаление пользователей в облаке SAP Analytics, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и облаком SAP Analytics
> * [Единый вход](sapboc-tutorial.md) в систему SAP Analytics Cloud (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](../develop/quickstart-create-new-tenant.md) 
* Учетная запись пользователя в Azure AD с [разрешением](../users-groups-roles/directory-assign-admin-roles.md) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* Облачный клиент SAP Analytics
* Учетная запись пользователя в консоли администрирования для подготовки удостоверений SAP с разрешениями администратора. Убедитесь, что у вас есть доступ к системным прокси-серверам в консоли администрирования "Подготовка удостоверений". Если вы не видите плитку " **системы прокси** ", создайте инцидент для компонента **BC-IAM-IP** , чтобы запросить доступ к этой плитке.
* Клиент OAuth с авторизацией предоставления учетных данных клиента в SAP Analytics Cloud. Дополнительные сведения см. в статье [Управление клиентами OAuth и доверенными поставщиками удостоверений](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html) .

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки

1. Узнайте, [как работает служба подготовки](../app-provisioning/user-provisioning.md).
2. Определите, кто будет находиться в [области подготовки](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Определите, какие данные должны [сопоставляться между Azure AD и облаком SAP Analytics](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка SAP Analytics Cloud для поддержки подготовки с помощью Azure AD

1. Войдите в [консоль администрирования для подготовки удостоверений SAP](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/) с помощью учетной записи администратора, а затем выберите пункт **прокси-системы** .

   ![Прокси-системы SAP](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png.png)

2. Выберите **Свойства** .

   ![Свойства систем прокси-сервера SAP](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. Скопируйте **URL-адрес** и добавьте `/api/v1/scim` к URL-адресу. Сохраните его для дальнейшего использования в поле **URL-адрес клиента** .

   ![URL-адрес свойств систем прокси-сервера SAP](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. Используйте [POST](https://www.postman.com/) для выполнения запроса POST по протоколу HTTPS к адресу: `<Token URL>?grant_type=client_credentials` где `Token URL` — это URL-адрес в поле **OAuth2TokenServiceURL** . Этот шаг необходим для создания маркера доступа, который будет использоваться в поле секретного токена при настройке автоматической подготовки.

   ![Свойства систем прокси-сервера SAP IP OAuth](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. В окне Post используйте **обычную проверку подлинности** и задайте идентификатор клиента OAuth в качестве пользователя и секрет пароля. Этот вызов возвращает маркер доступа. Не заключайте этот код для дальнейшего использования в поле **секретного токена** .

   ![Отправить запрос POST](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление SAP Analytics Cloud из коллекции приложений Azure AD

Добавление SAP Analytics Cloud из коллекции приложений Azure AD для начала управления подготовкой в облако SAP Analytics. Если вы ранее настроили службу SAP Analytics Cloud для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* При назначении пользователей и групп для SAP Analytics Cloud необходимо выбрать роль, отличную от **доступа по умолчанию** . Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](../develop/howto-add-app-roles-in-azure-ad-apps.md), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>Шаг 5. Настройка автоматической подготовки пользователей в облаке SAP Analytics 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для облака SAP Analytics в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения** , а затем **Все приложения** .

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **SAP Analytics Cloud** .

    ![Облачная ссылка SAP Analytics в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка** .

    ![Снимок экрана параметров управления с вызываемым параметром подготовки.](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Снимок экрана: раскрывающийся список режима подготовки с вызываемым автоматическим параметром.](common/provisioning-automatic.png)

5. В разделе **Учетные данные администратора** введите в поле **URL-адрес арендатора** значение URL-адреса, полученное ранее. Введите значение маркера доступа, полученное ранее в **маркере секрета** . Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключаться к подготовке. В случае сбоя подключения убедитесь, что облачная учетная запись SAP Analytics имеет разрешения администратора, и повторите попытку.

    ![На снимке экрана отображается диалоговое окно учетные данные администратора, в котором можно ввести клиент U R и секретный маркер.](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое** .

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить** .

8. В разделе **Сопоставления** выберите **Подготовка пользователей Azure Active Directory** .

9. Изучите пользовательские атрибуты, которые синхронизированы из Azure AD в SAP Analytics Cloud, в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в облаке SAP Analytics для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](../app-provisioning/customize-application-attributes.md), необходимо убедиться, что ОБЛАЧНЫЙ API SAP Analytics поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить** , чтобы зафиксировать все изменения.

   |attribute|Тип|Поддерживается для фильтрации|
   |---|---|---|
   |userName|Строка|&check;|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |active|Логическое|
   |emails[type eq "work"].value|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Строка|

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для SAP Analytics Cloud, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и (или) группы, которые вы хотите подготавливать к облаку SAP Analytics, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить** .

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры** . Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](../reports-monitoring/concept-provisioning-logs.md), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)