---
title: Руководство. Настройка консоли облачной инфраструктуры Oracle для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в консоли облачной инфраструктуры Oracle.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 665e4870619751bbda062473d0c2549b26352d94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361608"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Руководство. Настройка консоли облачной инфраструктуры Oracle для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить в консоли облачной инфраструктуры Oracle и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп к [консоли облачной инфраструктуры Oracle](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в консоли облачной инфраструктуры Oracle
> * Удаление пользователей в консоли облачной инфраструктуры Oracle, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и консолью облачной инфраструктуры Oracle
> * Предоставление групп и членств в группах в консоли облачной инфраструктуры Oracle
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) в консоль инфраструктуры облака Oracle (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* [Клиент](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)управления облачной инфраструктурой Oracle.
* Учетная запись пользователя в системе управления облачной инфраструктурой Oracle с разрешениями администратора.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между консолью облачной инфраструктуры Azure AD и Oracle](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка консоли облачной инфраструктуры Oracle для поддержки подготовки с помощью Azure AD

1. Войдите на портал администрирования для консоли облачной инфраструктуры Oracle. В левом верхнем углу экрана перейдите к **удостоверению > Федерация**.

    ![Администратор Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Щелкните URL-адрес, отображаемый на странице рядом с консолью облачной службы удостоверения Oracle.

    ![URL-адрес Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Щелкните **Добавить поставщик удостоверений** , чтобы создать новый поставщик удостоверений. Сохраните идентификатор IdP, который будет использоваться как часть URL-адреса клиента. Щелкните значок "плюс" рядом с вкладкой " **приложения** ", чтобы создать клиент OAuth и предоставить идкс AppRole "Администратор домена удостоверений".

    ![Значок Oracle Cloud](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Следуйте приведенным ниже снимкам экрана, чтобы настроить приложение. После завершения настройки нажмите кнопку **сохранить**.

    ![Конфигурация Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Политика выдачи маркеров Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. На вкладке конфигурации приложения разверните параметр **Общие сведения** , чтобы получить идентификатор клиента и секрет клиента.

    ![Создание маркеров Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Чтобы создать маркер секрета Base64, необходимо закодировать идентификатор клиента и секрет клиента в формате **идентификатор клиента: Секрет клиента**. Сохраните секретный токен. Это значение будет указано в поле **секретный токен** на вкладке Подготовка консольного приложения облачной инфраструктуры Oracle на портал Azure.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление консоли облачной инфраструктуры Oracle из коллекции приложений Azure AD

Добавьте консоль облачной инфраструктуры Oracle из коллекции приложений Azure AD, чтобы начать управление подготовкой в консоль облачной инфраструктуры Oracle. Если вы ранее настроили консоль инфраструктуры облака Oracle для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для консоли облачной инфраструктуры Oracle необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Шаг 5. Настройка автоматической подготовки пользователей в консоли облачной инфраструктуры Oracle 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для консоли облачной инфраструктуры Oracle в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Oracle Cloud Infrastructure Console**.

    ![Ссылка на консоль инфраструктуры облака Oracle в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Снимок экрана параметров управления с вызываемым параметром подготовки.](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Снимок экрана: раскрывающийся список режима подготовки с вызываемым автоматическим параметром.](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите **URL-адрес клиента** в формате `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Например, `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Введите значение секретного токена, полученное ранее на шаге **Секретный токен**. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к консоли облачной инфраструктуры Oracle. В случае сбоя подключения убедитесь, что учетная запись консоли облачной инфраструктуры Oracle имеет разрешения администратора, и повторите попытку.

    ![На снимке экрана отображается диалоговое окно учетные данные администратора, в котором можно ввести клиент U R и секретный маркер.](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с консолью облачной инфраструктуры Oracle**.

9. Ознакомьтесь с атрибутами пользователей, которые синхронизированы из Azure AD с консолью облачной инфраструктуры Oracle, в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в консоли облачной инфраструктуры Oracle для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что API консоли облачной инфраструктуры Oracle поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |attribute|Тип|
      |---|---|
      |displayName|Строка|
      |userName|Строка|
      |active|Логическое|
      |title|Строка|
      |emails[type eq "work"].value|Строка|
      |preferredLanguage|Строка|
      |name.givenName|Строка|
      |name.familyName|Строка|
      |addresses[type eq "work"].formatted|Строка|
      |addresses[type eq "work"].locality|Строка|
      |addresses[type eq "work"].region|Строка|
      |addresses[type eq "work"].postalCode|Строка|
      |addresses[type eq "work"].country|Строка|
      |addresses[type eq "work"].streetAddress|Строка|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Строка|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Строка|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Строка|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Справочник|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Строка|
      |urn: IETF: params: scim: схемы: Oracle: идкс: Extension: пользователь: пользователь: Бипасснотификатион|Логическое|
      |urn: IETF: params: scim: схемы: Oracle: идкс: Extension: пользователь: пользователь: Исфедератедусер|Логическое|

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы с консолью облачной инфраструктуры Oracle**.

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD в консоль облачной инфраструктуры Oracle, в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в консоли облачной инфраструктуры Oracle для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |attribute|Тип|
      |---|---|
      |displayName|Строка|
      |externalId|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для консоли облачной инфраструктуры Oracle, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать к консоли облачной инфраструктуры Oracle, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

* Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
* Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
* Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
