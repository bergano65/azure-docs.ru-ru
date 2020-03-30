---
title: 'Учебник: Налаживайте Rollbar для автоматического подготовки пользователей с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как настроить Active Directory Azure для автоматического предоставления и де-предоставления учетных записей пользователей Rollbar.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d737aa16-8ab4-4c0c-a68b-2911623b41eb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: 27a26a0c8378f34794afd87cf11b6bb878f7b53c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248457"
---
# <a name="tutorial-configure-rollbar-for-automatic-user-provisioning"></a>Учебник: Настройка Rollbar для автоматического подготовки пользователей

В этом уроке описаны шаги, которые необходимо выполнить как в Rollbar, так и в Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически провизии и де-положения пользователей и групп [Rollbar](https://rollbar.com/pricing/) с помощью службы обеспечения Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Возможности, поддерживаемые
> [!div class="checklist"]
> * Создание пользователей в Rollbar
> * Удалите пользователей в Rollbar, если им больше не требуется доступ
> * Сохраняйте синхронизацию атрибутов пользователя между Azure AD и Rollbar
> * Группы по предоставлению и членству в группах в Роллбаре
> * [Однократный указатель на](https://docs.microsoft.com/azure/active-directory/saas-apps/rollbar-tutorial) Роллбар (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложений или глобальный администратор). 
* [Арендатор Rollbar](https://rollbar.com/pricing/) с корпоративным планом.
* Учетная запись пользователя в Rollbar с разрешениями Admin.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания
1. Узнайте о [том, как работает служба обеспечения.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Определите, кто будет находиться в [сфере подготовки.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Определите, какие данные следует [сопоставить между Azure AD и Rollbar.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-rollbar-to-support-provisioning-with-azure-ad"></a>Шаг 2. Налажить Rollbar для поддержки подготовки с Azure AD

Прежде чем настроить Rollbar для автоматического подготовки пользователей с Azure AD, необходимо включить SCIM-подготовку на Rollbar.

1. Войти на [консоль Rollbar Admin.](https://rollbar.com/login/) Нажмите на **настройки учетной записи**.

    ![Консоль для админ-консоли Rollbar](media/rollbar-provisioning-tutorial/image00.png)

2. Перейдите к **своему телу арендатора Rollbar > Identity Provider.**

    ![Поставщик идентификационных данных Rollbar](media/rollbar-provisioning-tutorial/idp.png)

3. Прокрутите вниз до **параметры подготовки.** Копирование токена доступа. Это значение будет введено в поле **Secret Token** в вкладке подготовки приложения Rollbar на портале Azure. Выберите **флажок пользователя и команды Enable** и нажмите кнопку **«Сохранить».**

    ![Токен доступа Rollbar](media/rollbar-provisioning-tutorial/token.png)


## <a name="step-3-add-rollbar-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Rollbar из галереи приложений Azure AD

Добавьте Rollbar из галереи приложений Azure AD, чтобы начать управление подготовкой к Rollbar. Если у вас есть ранее настройка Rollbar для SSO, вы можете использовать то же приложение. Однако рекомендуется создать отдельное приложение при тестировании интеграции на начальном этапе. Подробнее о добавлении приложения из галереи [можно](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)здесь. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определить, кто будет находиться в сфере подготовки 

Служба предоставления AD Azure AD позволяет использовать область охвата, которая будет подготовлена на основе назначения приложения и или на основе атрибутов пользователя/группы. Если вы выбираете область, которая будет подготовлена к вашему приложению на основе назначения, вы можете использовать следующие [шаги](../manage-apps/assign-user-or-group-access-portal.md) для назначения пользователей и групп приложения. Если вы выбираете область, которая будет подготовлена исключительно на основе атрибутов пользователя или группы, вы можете использовать фильтр проверки, как описано [здесь.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* При назначении пользователей и групп Rollbar необходимо выбрать роль, не связанную с **доступом к по умолчанию.** Пользователи с ролью доступа по умолчанию исключены из подготовки и будут помечены как не имеющие эффективного права в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) чтобы добавить дополнительные роли. 

* Начните с малого. Тест с небольшим набором пользователей и групп, прежде чем выкатить для всех. Когда область подготовки устанавливается для назначенных пользователей и групп, вы можете управлять этим, назначив одному или двум пользователям или группам приложение. Когда область расположена для всех пользователей и групп, можно указать [фильтр сканирования на основе атрибутов.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-rollbar"></a>Шаг 5. Настройка автоматического пользовательского обеспечения rollbar 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в TestApp на основе пользовательских и/или групповых заданий в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-rollbar-in-azure-ad"></a>Для настройки автоматического пользовательского обеспечения Rollbar в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Rollbar**.

    ![Ссылка на Rollbar в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** ввейте значение маркера доступа, полученное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к Rollbar. Если соединение не удается, убедитесь, что ваша учетная запись Rollbar имеет разрешения для админора, и повторите попытку.

    ![Подготовка](./media/rollbar-provisioning-tutorial/admin.png)

6. В поле **"Уведомление email"** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке и выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в Rollbar.**

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с Rollbar в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Rollbar для операций обновления. Если вы решите изменить [соответствующий целевой атрибут,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)необходимо убедиться, что API Rollbar поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |Атрибут|Тип|
   |---|---|
   |userName|Строка|
   |externalId|Строка|
   |active|Логическое|
   |name.familyName|Строка|
   |name.givenName|Строка|
   |электронные письма (тип eq «работа»)|Строка|

10. В разделе **Картпинги** выберите **синхронизацию активных групп каталогов Azure в Rollbar.**

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с Rollbar в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам в Rollbar для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |Атрибут|Тип|
      |---|---|
      |displayName|Строка|
      |externalId|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления Azure AD для Rollbar, измените **статус подготовки** в разделе **«Настройки».** **On**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить Rollbar, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **разделе Область** в разделе **Настройки.** Первоначальный цикл занимает больше времени, чем последующие циклы, которые происходят примерно каждые 40 минут при запуске службы обеспечения Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

1. Используйте [журналы подготовки,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) чтобы определить, какие пользователи были подготовлены успешно или безуспешно
2. Проверьте [планку прогресса,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) чтобы увидеть состояние цикла подготовки и насколько он близок к завершению
3. Если конфигурация подготовки, как представляется, в неработоспособном состоянии, приложение будет идти в карантин. Узнайте больше о карантинных состояниях [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
