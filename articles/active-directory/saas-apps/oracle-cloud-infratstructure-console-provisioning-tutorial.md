---
title: 'Учебник: Налаживайте консоль облачной инфраструктуры Oracle для автоматического предоставления пользовательских запросов с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как автоматически предоставлять и депредоставлять учетные записи пользователей от Azure AD до Oracle Cloud Infrastructure Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378955"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Учебник: Настройка облачной инфраструктуры Oracle для автоматического подготовки пользователей

В этом уроке описаны шаги, которые необходимо выполнить как в консоли Oracle Cloud Infrastructure Console, так и в Active Directory Azure (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически провизии и де-положения пользователей и групп [Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) с помощью службы обеспечения AD Azure. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Возможности, поддерживаемые
> [!div class="checklist"]
> * Создание пользователей на консоли облачной инфраструктуры Oracle
> * Удалите пользователей в консоли облачной инфраструктуры Oracle, если им больше не требуется доступ
> * Сохраняйте синхронизацию атрибутов пользователя между Azure AD и консолью облачной инфраструктуры Oracle
> * Предоставление групп и членство группы в oracle Cloud Infrastructure Console
> * [Однократное входное вставку на](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) консоль облачной инфраструктуры Oracle (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложений или глобальный администратор). 
* [Арендатор](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton)Oracle Cloud Infrastructure Control.
* Учетная запись пользователя в Oracle Cloud Infrastructure Control с разрешениями Admin.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания
1. Узнайте о [том, как работает служба обеспечения.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Определите, кто будет находиться в [сфере подготовки.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Определите, какие данные следует [сопоставить между Azure AD и консолью облачной инфраструктуры Oracle.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка облачной инфраструктуры Oracle для поддержки подготовки с помощью Azure AD

1. Войти в админ-портал Oracle Cloud Infrastructure Console. В верхнем левом углу экрана перейдите к **Identity > Федерации**.

    ![Админ Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Нажмите на URL-адрес, отображаемый на странице рядом с консолью облачных услуг Oracle Identity.

    ![URL-адрес Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Нажмите на **Add Identity Provider,** чтобы создать нового поставщика идентификационных данных. Сохранить идентификатор IdP для использования в качестве части URL-адреса клиента. Нажмите на значок plus рядом со вкладкой **Приложения,** чтобы создать Клиент OAuth и Grant IDCS Identity Domain Administrator AppRole.

    ![Значок облака Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Следуйте скриншоты ниже, чтобы настроить приложение. После того, как конфигурация сделана, нажмите на **Save**.

    ![Конфигурация Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Политика выдачи токенов Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Под вкладкой конфигураций вашего приложения расширяйте опцию **«Общая информация»** для получения идентификатора клиента и секретклиента.

    ![Поколение токенов Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Для создания секретного токена Base64 кодирует идентификатор клиента и секрет клиента в формате **Client ID:Client Secret.** Сохранить секретный маркер. Это значение будет введено в поле **Secret Token** в вкладке подготовки приложения Oracle Cloud Infrastructure Console на портале Azure.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление консоли облачной инфраструктуры Oracle из галереи приложений Azure AD

Добавьте консоль Oracle Cloud Infrastructure из галереи приложений Azure AD, чтобы начать управление подготовкой к консоли Oracle Cloud Infrastructure Console. Если вы ранее настраивали консоль Oracle Cloud Infrastructure Console для SSO, вы можете использовать то же приложение. Однако рекомендуется создать отдельное приложение при тестировании интеграции на начальном этапе. Подробнее о добавлении приложения из галереи [можно](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)здесь. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определить, кто будет находиться в сфере подготовки 

Служба предоставления AD Azure AD позволяет использовать область охвата, которая будет подготовлена на основе назначения приложения и или на основе атрибутов пользователя/группы. Если вы выбираете область, которая будет подготовлена к вашему приложению на основе назначения, вы можете использовать следующие [шаги](../manage-apps/assign-user-or-group-access-portal.md) для назначения пользователей и групп приложения. Если вы выбираете область, которая будет подготовлена исключительно на основе атрибутов пользователя или группы, вы можете использовать фильтр проверки, как описано [здесь.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* При назначении пользователей и групп консоли облачной инфраструктуры Oracle необходимо выбрать роль, не связанную с **доступом к по умолчанию.** Пользователи с ролью доступа по умолчанию исключены из подготовки и будут помечены как не имеющие эффективного права в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) чтобы добавить дополнительные роли. 

* Начните с малого. Тест с небольшим набором пользователей и групп, прежде чем выкатить для всех. Когда область подготовки устанавливается для назначенных пользователей и групп, вы можете управлять этим, назначив одному или двум пользователям или группам приложение. Когда область расположена для всех пользователей и групп, можно указать [фильтр сканирования на основе атрибутов.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Шаг 5. Настройка автоматического обеспечения пользователя для консоли облачной инфраструктуры Oracle 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в TestApp на основе пользовательских и/или групповых заданий в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Для настройки автоматической подготовки пользователей для консоли Oracle Cloud Infrastructure В Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Oracle Cloud Infrastructure Console**.

    ![Ссылка на консоль облачной инфраструктуры Oracle в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** введомните `https://<IdP ID>.identity.oraclecloud.com/admin/v1` **URL-адрес клиента** в формате . Например, `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Ввейте секретное значение маркера, извлеченное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к консоли oracle Cloud Infrastructure. Если подключение не удается, убедитесь, что учетная запись Oracle Cloud Infrastructure Console имеет разрешения для админирований и повторите попытку.

    ![Подготовка](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. В поле **"Уведомление email"** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке и выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталогов Azure с консолью облачной инфраструктуры Oracle.**

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с Oracle Cloud Infrastructure Console в разделе **«Атрибут-Картирование».** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в консоли Oracle Cloud Infrastructure Console для операций обновления. Если вы решите изменить [соответствующий целевой атрибут,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)необходимо убедиться, что Oracle Cloud Infrastructure Console API поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |Атрибут|Тип|
      |---|---|
      |displayName|Строка|
      |userName|Строка|
      |active|Логическое|
      |title|Строка|
      |emails[type eq "work"].value|Строка|
      |preferredLanguage|Строка|
      |name.givenName|Строка|
      |name.familyName|Строка|
      |адреса eq "работа"..formatted|Строка|
      |адреса eq "работа"..местность|Строка|
      |адреса eq "работа".регион|Строка|
      |addresses[type eq "work"].postalCode|Строка|
      |адреса »тип eq «работа»».|Строка|
      |addresses[type eq "work"].streetAddress|Строка|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Строка|
      |урна:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|
      |урна:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Строка|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Строка|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Справочник|
      |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organisation|Строка|
      |urn:ietf:params:scim:schemas:oracle:idcs:user:User:bypassNotification|Логическое|
      |урна:ietf:params:scim:schemas:oracle:idcs:user:User:isFederatedUser|Логическое|

10. В разделе **Картпинги** выберите **синхронизацию групп активных каталогов Azure для консоли oracle Cloud Infrastructure Console.**

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с Oracle Cloud Infrastructure Console в разделе **«Атрибут-Картирование».** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам в консоли Oracle Cloud Infrastructure Console для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |Атрибут|Тип|
      |---|---|
      |displayName|Строка|
      |externalId|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу обеспечения Azure AD для консоли облачной инфраструктуры Oracle, измените **статус подготовки** **в** разделе **"Настройки".**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить консоли облачной инфраструктуры Oracle, выбрав желаемые значения в **разделе Область** в разделе **Настройки.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **разделе Область** в разделе **Настройки.** Первоначальный цикл занимает больше времени, чем последующие циклы, которые происходят примерно каждые 40 минут при запуске службы обеспечения Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

* Используйте [журналы подготовки,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) чтобы определить, какие пользователи были подготовлены успешно или безуспешно
* Проверьте [планку прогресса,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) чтобы увидеть состояние цикла подготовки и насколько он близок к завершению
* Если конфигурация подготовки, как представляется, в неработоспособном состоянии, приложение будет идти в карантин. Узнайте больше о карантинных состояниях [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Следующие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
