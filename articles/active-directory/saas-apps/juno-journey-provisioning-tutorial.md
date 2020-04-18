---
title: 'Учебник: Накондиция Juno Journey для автоматического предоставления пользовательского паранджи с помощью Active Directory Документы Майкрософт'
description: Узнайте, как автоматически предоставлять и депредоставлять учетные записи пользователей из Azure AD в Juno Journey.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 08de07a52d1e43dea91e6684d33027d8bcad61fc
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641853"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Учебник: Настройка Juno Путешествие для автоматического подготовки пользователей

В этом уроке описаны шаги, которые необходимо выполнить как в Juno Journey, так и в Active Directory Azure (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически провизии и де-положения пользователей и групп [к Juno Journey](https://www.junojourney.com/) с помощью службы обеспечения AD Azure. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Возможности, поддерживаемые
> [!div class="checklist"]
> * Создание пользователей в Juno Journey
> * Удалите пользователей в Juno Journey, если им больше не требуется доступ
> * Сохраняйте синхронизацию атрибутов пользователя между Azure AD и Juno Journey
> * [Однократный знак на](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) Juno Путешествие (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложений или глобальный администратор). 
*  [Арендатор Juno Путешествие](https://www.junojourney.com/getstartedwithjuno).
*  Учетная запись пользователя в Juno Journey с разрешениями Admin.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания
1. Узнайте о [том, как работает служба обеспечения.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Определите, кто будет находиться в [сфере подготовки.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Определите, какие данные следует [сопоставить между Azure AD и Juno Journey.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>Шаг 2. Нанастройка Juno Journey для поддержки подготовки с Azure AD

1. Для **Secret Token** и **Арендатор URL** связаться с командой поддержки Juno Путешествие на support@the-juno.com. Это значение будет введено в поля **хитаве** **Секретных токенов** и URL-адресов арендатора соответственно в вкладке «Подготовка» приложения Juno Journey на портале Azure. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавить Juno Путешествие из галереи приложений Azure AD

Добавьте Juno Journey из галереи приложений Azure AD, чтобы начать управление подготовкой к Juno Journey. Если вы ранее настраивали Juno Journey для SSO, вы можете использовать то же приложение. Однако рекомендуется создать отдельное приложение при тестировании интеграции на начальном этапе. Подробнее о добавлении приложения из галереи [можно](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)здесь. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определить, кто будет находиться в сфере подготовки 

Служба предоставления AD Azure AD позволяет использовать область охвата, которая будет подготовлена на основе назначения приложения и или на основе атрибутов пользователя/группы. Если вы выбираете область, которая будет подготовлена к вашему приложению на основе назначения, вы можете использовать следующие [шаги](../manage-apps/assign-user-or-group-access-portal.md) для назначения пользователей и групп приложения. Если вы выбираете область, которая будет подготовлена исключительно на основе атрибутов пользователя или группы, вы можете использовать фильтр проверки, как описано [здесь.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* При назначении пользователей и групп ВJuno Journey необходимо выбрать роль, не связанную с **доступом к номеру По умолчанию.** Пользователи с ролью доступа по умолчанию исключены из подготовки и будут помечены как не имеющие эффективного права в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) чтобы добавить дополнительные роли. 

* Начните с малого. Тест с небольшим набором пользователей и групп, прежде чем выкатить для всех. Когда область подготовки устанавливается для назначенных пользователей и групп, вы можете управлять этим, назначив одному или двум пользователям или группам приложение. Когда область расположена для всех пользователей и групп, можно указать [фильтр сканирования на основе атрибутов.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>Шаг 5. Настройка автоматического пользовательского обеспечения к Juno Journey 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в TestApp на основе пользовательских и/или групповых заданий в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>Для настройки автоматического пользовательского обеспечения для Juno Journey в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Juno Journey**.

    ![Ссылка Juno Путешествие в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **"Учетные данные admin"** ввешайте значение URL-адреса клиента, извлеченное ранее в **URL-адресе арендатора.** Ввейте секретное значение маркера, извлеченное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к Juno Journey. Если соединение не удается, убедитесь, что ваша учетная запись Juno Journey имеет разрешения для админовима и повторите попытку.

    ![Подготовка](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. В поле **"Уведомление email"** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке и выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в Juno Journey.**

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD на Juno Journey в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Juno Journey для операций обновления. Если вы решите изменить [соответствующий целевой атрибут,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)необходимо убедиться, что Juno Journey API поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |Переменная|Тип|
   |---|---|
   |userName|Строка|
   |externalId|Строка|
   |displayName|Строка|
   |title|Строка|
   |active|Логическое|
   |preferredLanguage|Строка|
   |emails[type eq "work"].value|Строка|
   |адреса »тип eq «работа»».|Строка|
   |адреса eq "работа".регион|Строка|
   |адреса eq "работа"..местность|Строка|
   |addresses[type eq "work"].postalCode|Строка|
   |адреса eq "работа"..formatted|Строка|
   |addresses[type eq "work"].streetAddress|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |имя.среднееимя|Строка|
   |name.formatted|Строка|
   |phoneNumbers[type eq "fax"].value|Строка|
   |phoneNumbers[type eq "mobile"].value|Строка|
   |phoneNumbers[type eq "work"].value|Строка|
   |урна:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Строка|
   |урна:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Строка|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Строка|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Строка|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organisation|Строка|


10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу предоставления AD Azure AD для Juno Journey, измените **статус подготовки** в разделе **«Настройки».** **On**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и/или группы, которые вы хотели бы предоставить Juno Journey, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

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

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
