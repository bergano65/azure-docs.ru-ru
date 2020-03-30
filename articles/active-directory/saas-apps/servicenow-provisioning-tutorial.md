---
title: Руководство по настройке ServiceNow для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить автоматическую подготовку и отзыв учетных записей пользователей из Azure AD в ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205146"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Учебник: Настройте ServiceNow для автоматического подготовки пользователей

В этом уроке описаны шаги, которые необходимо выполнить как в ServiceNow, так и в Active Directory Azure (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически провизии и де-положения пользователей и групп [в ServiceNow](https://www.servicenow.com/) с помощью службы обеспечения Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Возможности, поддерживаемые
> [!div class="checklist"]
> * Создание пользователей в ServiceNow
> * Удалите пользователей в ServiceNow, когда им больше не требуется доступ
> * Сохраняйте синхронизацию атрибутов пользователя между Azure AD и ServiceNow
> * Группы по предоставлению и членству в группах в ServiceNow
> * [Единый входной знак на](servicenow-tutorial.md) ServiceNow (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложений или глобальный администратор). 
* [ServiceNow экземпляр](https://www.servicenow.com/) Калгари или выше
* [Экземпляр ServiceNow Express](https://www.servicenow.com/) в Хельсинки или выше
* Учетная запись пользователя в ServiceNow с ролью админ

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания
1. Узнайте о [том, как работает служба обеспечения.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Определите, кто будет находиться в [сфере подготовки.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Определите, какие данные следует [сопоставить между Azure AD и ServiceNow.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка ServiceNow для поддержки подготовки с Azure AD

1. Определите имя экземпляра ServiceNow. Имя экземпляра можно найти в URL-адресе, который используется для доступа к ServiceNow. В приведенном ниже примере имя экземпляра — dev35214.

![ServiceNow Instance](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Получение учетных данных для админа в ServiceNow. Перейдите к профилю пользователя в ServiceNow и убедитесь, что пользователь имеет роль администратора. 

![ServiceNow роль админа](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление ServiceNow из галереи приложений Azure AD

Добавьте ServiceNow из галереи приложений Azure AD, чтобы начать управление подготовкой к ServiceNow. Если у вас есть ранее настроенная служба ServiceNow для SSO, вы можете использовать то же приложение. Однако рекомендуется создать отдельное приложение при тестировании интеграции на начальном этапе. Подробнее о добавлении приложения из галереи [можно](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)здесь. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определить, кто будет находиться в сфере подготовки 

Служба предоставления AD Azure AD позволяет использовать область охвата, которая будет подготовлена на основе назначения приложения и или на основе атрибутов пользователя/группы. Если вы выбираете область, которая будет подготовлена к вашему приложению на основе назначения, вы можете использовать следующие [шаги](../manage-apps/assign-user-or-group-access-portal.md) для назначения пользователей и групп приложения. Если вы выбираете область, которая будет подготовлена исключительно на основе атрибутов пользователя или группы, вы можете использовать фильтр проверки, как описано [здесь.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* При назначении пользователей и групп в ServiceNow необходимо выбрать роль, не связанную с **доступом к по умолчанию.** Пользователи с ролью доступа по умолчанию исключены из подготовки и будут помечены как не имеющие эффективного права в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) чтобы добавить дополнительные роли. 

* Начните с малого. Тест с небольшим набором пользователей и групп, прежде чем выкатить для всех. Когда область подготовки устанавливается для назначенных пользователей и групп, вы можете управлять этим, назначив одному или двум пользователям или группам приложение. Когда область расположена для всех пользователей и групп, можно указать [фильтр сканирования на основе атрибутов.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Шаг 5. Настройка автоматической подготовки пользователя к ServiceNow 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в TestApp на основе пользовательских и/или групповых заданий в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Для настройки автоматического обеспечения пользователей для ServiceNow в Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **ServiceNow**.

    ![Ссылка на ServiceNow в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Учетные данные Admin** ввейди свои учетные данные И имя пользователя ServiceNow. Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к ServiceNow. Если соединение выходит из строя, убедитесь, что учетная запись ServiceNow имеет разрешения Admin и повторите попытку.

    ![Подготовка](./media/servicenow-provisioning-tutorial/provisioning.png)

6. В поле **"Уведомление email"** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке и выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в ServiceNow.**

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD на ServiceNow в разделе **«Отображение атрибутов».** Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в ServiceNow для операций обновления. Если вы решите изменить [соответствующий целевой атрибут,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)необходимо убедиться, что API ServiceNow поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

10. В разделе **Картпинги** выберите **синхронизацию активных групп каталогов Azure в ServiceNow.**

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с ServiceNow в разделе **«Отображение атрибутов».** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления групп в ServiceNow для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу предоставления услуг Azure AD для ServiceNow, измените **статус подготовки** в разделе **«Настройки».** **On**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и/или группы, которые вы хотели бы предоставить ServiceNow, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **разделе Область** в разделе **Настройки.** Первоначальный цикл занимает больше времени, чем последующие циклы, которые происходят примерно каждые 40 минут при запуске службы обеспечения Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

1. Используйте [журналы подготовки,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) чтобы определить, какие пользователи были подготовлены успешно или безуспешно
2. Проверьте [планку прогресса,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) чтобы увидеть состояние цикла подготовки и насколько он близок к завершению
3. Если конфигурация подготовки, как представляется, в неработоспособном состоянии, приложение будет идти в карантин. Узнайте больше о карантинных состояниях [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
* **InvalidLookupСправка:** При подготовке определенных атрибутов, таких как отдел и местоположение в ServiceNow, значения уже должны существовать в справочной таблице в ServiceNow. Например, в таблице **имен вставки** в ServiceNow могут быть два местоположения (Сиэтл, Лос-Анджелес) и три отдела (продажи, финансы, маркетинг). Если вы попытаетесь предоставить пользователю, где его отдел "Продажи" и местоположение "Сиэтл", он будет подготовлен успешно. Если вы попытаетесь предоставить пользователю отдел "Продажи" и местоположение "LA", пользователь не будет подготовлен. Местоположение LA должно быть добавлено к справочной таблице в ServiceNow, либо атрибут пользователя в Azure AD должен быть обновлен в соответствии с форматом в ServiceNow. 
* **EntryJoiningPropertyValueisMissing:** Просмотрите [отображение атрибутов,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) чтобы определить соответствующий атрибут. Это значение должно присутствовать на пользователе или группе, которая вы пытаетесь предоставить. 
* Просмотрите [API ServiceNow SOAP,](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) чтобы понять любые требования или ограничения (например, формат для указания кодов страны для пользователя)
* Некоторые развертывания ServiceNow требуют разрешения диапазонов IP для службы предоставления Azure AD. Зарезервированные диапазоны IP для службы предоставления Azure AD можно найти [здесь,](https://www.microsoft.com/download/details.aspx?id=56519) в рамках "AzureActiveDirectoryDomainServices".
* Запросы на предоставление по умолчанию отправляются на https:/»ваш-экземпляр-имя.service-now.com/»table-name. Если требуется пользовательский URL-адрес клиента, можно предоставить весь URL-адрес в поле имени экземпляра.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
