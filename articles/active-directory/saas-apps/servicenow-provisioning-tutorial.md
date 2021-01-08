---
title: Руководство по Настройка ServiceNow для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить автоматическую подготовку и отзыв учетных записей пользователей из Azure AD в ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 71f4cfa9e7b69e51bafa5364f857f8ab689f2ad0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955473"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Руководство по Настройка ServiceNow для автоматической подготовки пользователей

В этом руководстве описаны действия, которые необходимо выполнить в ServiceNow и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически осуществляет и отменяет подготовку пользователей и групп для [ServiceNow](https://www.servicenow.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в ServiceNow.
> * Удаление пользователей из ServiceNow, когда им больше не нужен доступ.
> * Синхронизация атрибутов пользователей между Azure AD и ServiceNow.
> * Подготовка групп и членства в группах в ServiceNow.
> * [Единый вход](servicenow-tutorial.md) в ServiceNow (рекомендуется).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](../develop/quickstart-create-new-tenant.md) 
* Учетная запись пользователя в Azure AD с [разрешением](../roles/permissions-reference.md) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* [Экземпляр ServiceNow](https://www.servicenow.com/) версии Calgary или выше.
* [Экземпляр ServiceNow Express](https://www.servicenow.com/) версии Helsinki или выше.
* Учетная запись пользователя в ServiceNow с разрешениями администратора группы.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](../app-provisioning/user-provisioning.md).
2. Определите, кто будет находиться в [области подготовки](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Определите, какие данные следует [сопоставлять между Azure AD и ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка ServiceNow для поддержки подготовки с помощью Azure AD

1. Найдите имя экземпляра ServiceNow. Это имя экземпляра можно взять из URL-адреса, который используется для доступа к ServiceNow. В следующем примере имя экземпляра имеет значение dev35214.

   ![Имя экземпляра ServiceNow](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Получите учетные данные для администратора в ServiceNow. Перейдите к профилю пользователя в ServiceNow и убедитесь, что у этого пользователя есть роль администратора. 

   ![Роль администратора ServiceNow](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление ServiceNow из коллекции приложений Azure AD

Добавьте ServiceNow из коллекции приложений Azure AD, чтобы начать управление подготовкой в ServiceNow. Если вы ранее настроили единый вход в ServiceNow, можно использовать это же приложение. Но мы рекомендуем создать отдельное приложение для исходной проверки интеграции. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили определить пользователей только на основе атрибутов пользователя или группы, примените фильтр области, как описано [здесь](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* При назначении пользователей и групп для ServiceNow необходимо выбрать роль, отличающуюся от роли **Доступ по умолчанию**. Пользователи с такой ролью исключаются из подготовки и будут помечены в журналах подготовки как не имеющие разрешений. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](../develop/howto-add-app-roles-in-azure-ad-apps.md), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Шаг 5. Настройка автоматической подготовки пользователей в ServiceNow 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в ServiceNow, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **ServiceNow**.

    ![Ссылка на ServiceNow в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Снимок экрана: раздел "Управление" с выделенным параметром "Подготовка".](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Снимок экрана: раскрывающийся список "Режим подготовки" с выделенным параметром "Автоматически".](common/provisioning-automatic.png)

5. В разделе **Учетные данные администратора** введите учетные данные администратора ServiceNow и имя пользователя. Щелкните **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к ServiceNow. Если установить подключение не удалось, убедитесь, что у учетной записи ServiceNow есть разрешения администратора, и повторите попытку.

    ![Снимок экрана: страница "Подготовка службы", где можно ввести учетные данные администратора.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с ServiceNow**.

9. В разделе **Сопоставления атрибутов** просмотрите пользовательские атрибуты, которые синхронизируются из Azure AD в ServiceNow. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в ServiceNow для операций обновления. Если вы решили изменить [целевой атрибут сопоставления](../app-provisioning/customize-application-attributes.md), сначала убедитесь, что API ServiceNow поддерживает фильтрацию пользователей по этому атрибуту. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

10. В разделе **Сопоставления** выберите **Синхронизировать группы Azure Active Directory с ServiceNow**.

11. В разделе **Сопоставления атрибутов** просмотрите атрибуты группы, которые синхронизируются из Azure AD в ServiceNow. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления групп в ServiceNow в операциях обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для ServiceNow, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей или группы для подготовки в ServiceNow, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](../reports-monitoring/concept-provisioning-logs.md), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
* **InvalidLookupReference:** при подготовке некоторых атрибутов, таких как Department и Location в ServiceNow, эти значения должны уже существовать в ссылочной таблице в ServiceNow. Предположим, что у вас есть два расположения (Seattle, Los Angeles) и три отдела (Sales, Finance, Marketing) в таблице **добавить_имя_таблицы** в ServiceNow. При попытке подготовить пользователя, для которого указаны отдел Sales и расположение Seattle, подготовка завершится успешно. Но для пользователя с отделом Sales и расположением LA подготовка не будет выполнена. Необходимо либо добавить значение LA в ссылочную таблицу в ServiceNow, либо изменить атрибут пользователя в Azure AD, чтобы он соответствовал формату ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** проверьте [сопоставления атрибутов](../app-provisioning/customize-application-attributes.md), чтобы найти соответствующий атрибут. Это значение должно присутствовать у пользователя или группы, которые вы пытаетесь подготавливать. 
* Изучите [API ServiceNow SOAP](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html), чтобы понять все требования и ограничения (например, если нужно указать для пользователя код страны).
* По умолчанию запросы на подготовку отправляются на адрес https://{имя_вашего_экземпляра}.service-now.com/{имя_таблицы}. Если вам нужно использовать собственный URL-адрес клиента, введите этот URL-адрес полностью в поле "Имя экземпляра".
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Эта ошибка указывает на проблему с обменом данными с экземпляром ServiceNow. 
   
   Если у вас возникают проблемы при тестировании подключения, попробуйте **отключить** следующие параметры в ServiceNow:
   
   1. Выберите **System Security** > **High security settings** > **Require basic authentication for incoming SCHEMA requests** (Безопасность системы > Параметры высокого уровня безопасности > Требовать базовую аутентификацию для входящих запросов SCHEMA).
   2. Выберите **System Properties** > **Web Services** > **Require basic authorization for incoming SOAP requests** (Свойства системы > Веб-службы > Требовать базовую авторизацию для входящих запросов SOAP).

   ![Авторизация запроса SOAP](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

   Если этот поможет решить проблему, обратитесь в службу поддержки ServiceNow и попросите включить отладку SOAP для устранения неполадок. 

* **Диапазоны IP-адресов** 

   Сейчас служба подготовки Azure AD работает в конкретных диапазонах IP-адресов. Поэтому при необходимости вы можете ограничить другие диапазоны IP-адресов и добавить эти конкретные диапазоны IP-адресов в список разрешений вашего приложения, чтобы разрешить поток трафика из службы подготовки Azure AD в ваше приложение (см. раздел [Диапазоны IP-адресов](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#ip-ranges)).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)