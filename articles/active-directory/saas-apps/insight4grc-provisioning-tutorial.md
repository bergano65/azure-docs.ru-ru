---
title: 'Учебник: Налаживайте Insight4GRC для автоматического предоставления пользовательского запроса с помощью Active Directory Azure (ru) Документы Майкрософт'
description: Узнайте, как автоматически предоставлять и депредоставлять учетные записи пользователей из Azure AD в Insight4GRC.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0eab8a0-571b-4609-96b1-bdbc761a25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2020
ms.author: Zhchia
ms.openlocfilehash: 1404854e054c8fc4967ba863486969b8a87db526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621513"
---
# <a name="tutorial-configure-insight4grc-for-automatic-user-provisioning"></a>Учебник: Настройка Insight4GRC для автоматического подготовки пользователей

В этом уроке описаны шаги, которые необходимо выполнить как в Insight4GRC, так и в Active Directory Azure (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически провизии и де-положения пользователей и групп [Insight4GRC](https://www.rsmuk.com/) с помощью службы обеспечения Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Возможности, поддерживаемые
> [!div class="checklist"]
> * Создание пользователей в Insight4GRC
> * Удалите пользователей в Insight4GRC, если им больше не требуется доступ
> * Сохраняйте синхронизацию атрибутов пользователя между Azure AD и Insight4GRC
> * Группы по предоставлению и членству в группах в Insight4GRC
> * [Единый знак на](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial) Insight4GRC (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложений или глобальный администратор). 
* Учетная запись пользователя в Insight4GRC с разрешениями Admin.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания
1. Узнайте о [том, как работает служба обеспечения.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Определите, кто будет находиться в [сфере подготовки.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Определите, какие данные следует [сопоставить между Azure AD и Insight4GRC.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-insight4grc-to-support-provisioning-with-azure-ad"></a>Шаг 2. Нанастройка Insight4GRC для поддержки подготовки с Azure AD

Прежде чем настроить Insight4GRC для автоматического предоставления пользовательских данных с Azure AD, необходимо включить SCIM в подготовку на Insight4GRC.

1. Чтобы получить токен на предъявителя, конечным клиентам необходимо связаться с [группой поддержки.](mailto:support.ss@rsmuk.com)
2. Чтобы получить URL-адрес endpoint SCIM, вам нужно будет подготовить доменное имя Insight4GRC, поскольку оно будет использоваться для построения URL-адреса sciM. Вы можете получить доменное имя Insight4GRC в рамках первоначальной покупки программного обеспечения с Insight4GRC.

## <a name="step-3-add-insight4grc-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Insight4GRC из галереи приложений Azure AD

Добавьте Insight4GRC из галереи приложений Azure AD, чтобы начать управление подготовкой к Insight4GRC. Если вы ранее настраивали Insight4GRC для SSO, вы можете использовать то же приложение. Однако рекомендуется создать отдельное приложение при тестировании интеграции на начальном этапе. Подробнее о добавлении приложения из галереи [можно](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)здесь. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определить, кто будет находиться в сфере подготовки 

Служба предоставления AD Azure AD позволяет использовать область охвата, которая будет подготовлена на основе назначения приложения и или на основе атрибутов пользователя/группы. Если вы выбираете область, которая будет подготовлена к вашему приложению на основе назначения, вы можете использовать следующие [шаги](../manage-apps/assign-user-or-group-access-portal.md) для назначения пользователей и групп приложения. Если вы выбираете область, которая будет подготовлена исключительно на основе атрибутов пользователя или группы, вы можете использовать фильтр проверки, как описано [здесь.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* При назначении пользователей и групп Insight4GRC необходимо выбрать роль, не связанную с **доступом к по умолчанию.** Пользователи с ролью доступа по умолчанию исключены из подготовки и будут помечены как не имеющие эффективного права в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) чтобы добавить дополнительные роли. 

* Начните с малого. Тест с небольшим набором пользователей и групп, прежде чем выкатить для всех. Когда область подготовки устанавливается для назначенных пользователей и групп, вы можете управлять этим, назначив одному или двум пользователям или группам приложение. Когда область расположена для всех пользователей и групп, можно указать [фильтр сканирования на основе атрибутов.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-insight4grc"></a>Шаг 5. Настройка автоматического представления о пользователе на Insight4GRC 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в TestApp на основе пользовательских и/или групповых заданий в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-insight4grc-in-azure-ad"></a>Для настройки автоматического представления о предоставлении пользователям Insight4GRC в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Insight4GRC**.

    ![Ссылка на Insight4GRC в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Учетные данные Admin** ввейди URL-адрес endpoint SCIM в **URL-адрес клиента.** URL-адрес должен находиться `https://<Insight4GRC Domain Name>.insight4grc.com/public/api/scim/v2 ` в формате, в котором **доменное имя Insight4GRC** — это значение, извлеченное в предыдущих шагах. Ввейте значение маркера предъявителя, полученное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к Insight4GRC. Если соединение не удается, убедитесь, что ваша учетная запись Insight4GRC имеет разрешения admin и повторите попытку.

    ![Подготовка](./media/insight4grc-provisioning-tutorial/provisioning.png)

6. В поле **"Уведомление email"** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке и выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в Insight4GRC.**

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с Insight4GRC в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Insight4GRC для операций обновления. Если вы решите изменить [соответствующий целевой атрибут,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)необходимо убедиться, что API Insight4GRC поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |Атрибут|Тип|
   |---|---|
   |userName|Строка|
   |externalId|Строка|
   |active|Логическое|
   |title|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |emails[type eq "work"].value|Строка|
   |phoneNumbers[type eq "work"].value|Строка|

10. В разделе **Картпинги** выберите **синхронизацию групп активных каталогов Azure в Insight4GRC.**

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с Insight4GRC в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для соответствия группам в Insight4GRC для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |Атрибут|Тип|
      |---|---|
      |displayName|Строка|
      |externalId|Строка|
      |members|Справочник|

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления Azure AD для Insight4GRC, измените **статус подготовки** в разделе **«Настройки».** **On**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить Insight4GRC, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **разделе Область** в разделе **Настройки.** Первоначальный цикл занимает больше времени, чем последующие циклы, которые происходят примерно каждые 40 минут при запуске службы обеспечения Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

* Используйте [журналы подготовки,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) чтобы определить, какие пользователи были подготовлены успешно или безуспешно.
* Проверьте [панель прогресса,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) чтобы увидеть состояние цикла подготовки и насколько он близок к завершению.
* Если конфигурация подготовки, как представляется, в неработоспособном состоянии, приложение будет идти в карантин. Узнайте больше о карантинных состояниях [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетной записи пользователя для корпоративных приложений.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте, как просматривать журналы и получать отчеты о деятельности по подготовке.](../app-provisioning/check-status-user-account-provisioning.md)
