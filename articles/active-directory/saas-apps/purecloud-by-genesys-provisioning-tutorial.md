---
title: 'Учебник: Налаживайте PureCloud от Genesys для автоматического подготовки пользователей с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как автоматически предоставлять и депредоставлять учетные записи пользователей из Azure AD в PureCloud by Genesys.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370685"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Учебник: Настройка PureCloud от Genesys для автоматического подготовки пользователей

В этом уроке описаны шаги, которые необходимо выполнить как в PureCloud от Genesys, так и в Active Directory Azure (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически провизии и де-положения пользователей и групп [в PureCloud по Genesys](https://www.genesys.com) с помощью службы обеспечения Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Возможности, поддерживаемые
> [!div class="checklist"]
> * Создание пользователей в PureCloud от Genesys
> * Удалите пользователей в PureCloud по Genesys, если они не требуют доступа больше
> * Сохраняйте синхронизацию атрибутов пользователя между Azure AD и PureCloud от Genesys
> * Группы предоставления и членство в группах в PureCloud by Genesys
> * [Одноместный знак на](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) PureCloud от Genesys (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложений или глобальный администратор). 
* [Организация](https://help.mypurecloud.com/?p=81984)PureCloud .
* Пользователь с [разрешениями](https://help.mypurecloud.com/?p=24360) на создание Клиента Oauth.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания
1. Узнайте о [том, как работает служба обеспечения.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Определите, кто будет находиться в [сфере подготовки.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Определите, какие данные следует [сопоставить между Azure AD и PureCloud с помощью Genesys.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Шаг 2. Нанастройка PureCloud от Genesys для поддержки подготовки с Azure AD

1. Создайте [клиент Oauth,](https://help.mypurecloud.com/?p=188023) настроенный в вашей организации PureCloud.
2. Создайте токен [с вашим клиентом oauth.](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)
3. Если вы хотите автоматически предоставлять членство в группе в PureCloud, необходимо [создать группы](https://help.mypurecloud.com/?p=52397) в PureCloud с одинаковым именем группы в Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавить PureCloud по Genesys из галереи приложений Azure AD

Добавьте PureCloud by Genesys из галереи приложений Azure AD, чтобы начать управление подготовкой к PureCloud by Genesys. Если вы ранее настраивали PureCloud по Genesys для SSO, вы можете использовать то же приложение. Однако рекомендуется создать отдельное приложение при тестировании интеграции на начальном этапе. Подробнее о добавлении приложения из галереи [можно](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)здесь. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определить, кто будет находиться в сфере подготовки 

Служба предоставления AD Azure AD позволяет использовать область охвата, которая будет подготовлена на основе назначения приложения и или на основе атрибутов пользователя/группы. Если вы выбираете область, которая будет подготовлена к вашему приложению на основе назначения, вы можете использовать следующие [шаги](../manage-apps/assign-user-or-group-access-portal.md) для назначения пользователей и групп приложения. Если вы выбираете область, которая будет подготовлена исключительно на основе атрибутов пользователя или группы, вы можете использовать фильтр проверки, как описано [здесь.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* При назначении пользователей и групп PureCloud по Genesys необходимо выбрать роль, не связанную с **доступом к по умолчанию.** Пользователи с ролью доступа по умолчанию исключены из подготовки и будут помечены как не имеющие эффективного права в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) чтобы добавить дополнительные роли. 

* Начните с малого. Тест с небольшим набором пользователей и групп, прежде чем выкатить для всех. Когда область подготовки устанавливается для назначенных пользователей и групп, вы можете управлять этим, назначив одному или двум пользователям или группам приложение. Когда область расположена для всех пользователей и групп, можно указать [фильтр сканирования на основе атрибутов.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Шаг 5. Настройка автоматического подготовки пользователя к PureCloud от Genesys 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в TestApp на основе пользовательских и/или групповых заданий в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователя для PureCloud от Genesys в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **PureCloud от Genesys**.

    ![Ссылка на PureCloud от Genesys в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** ввейди свой URL-адрес PureCloud от Genesys API и токен Oauth в полях **URL-адреса арендатора** и **секретных токенов** соответственно. URL-адрес API будет структурирован `{{API Url}}/api/v2/scim/v2`как URL-адрес API для вашего региона PureCloud из [Центра разработчиков PureCloud.](https://developer.mypurecloud.com/api/rest/index.html) Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к PureCloud от Genesys. Если соединение не удается, убедитесь, что ваша учетная запись PureCloud by Genesys имеет разрешения для админовима и повторите попытку.

    ![Подготовка](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. В поле **"Уведомление email"** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке и выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в PureCloud by Genesys.**

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD на PureCloud по Genesys в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в PureCloud Genesys для операций обновления. Если вы решите изменить [соответствующий целевой атрибут,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)необходимо убедиться, что PureCloud by Genesys API поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

     |Атрибут|Тип|
     |---|---|
     |userName|Строка|
     |active|Логическое|
     |displayName|Строка|
     |emails[type eq "work"].value|Строка|
     |title|Строка|
     |phoneNumbers[type eq "mobile"].value|Строка|
     |phoneNumbers[type eq "work"].value|Строка|
     |урна:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|
     |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Справочник|

10. В разделе **Картпинги** выберите **синхронизацию групп активных каталогов Azure в PureCloud by Genesys.**

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с PureCloud по Genesys в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления групп в PureCloud Genesys для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения. PureCloud by Genesys не поддерживает создание или удаление группы и поддерживает только обновление групп.

      |Атрибут|Тип|
      |---|---|
      |displayName|Строка|
      |externalId|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления Услуг Azure AD для PureCloud by Genesys, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить PureCloud по Genesys, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

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
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
