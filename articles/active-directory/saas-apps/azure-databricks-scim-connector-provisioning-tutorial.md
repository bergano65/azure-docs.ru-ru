---
title: 'Учебник: Наконкажирование Azure Databricks SCIM Connector для автоматического предоставления пользовательского интерфейса с помощью Active Directory Документы Майкрософт'
description: Узнайте, как автоматически предоставлять и депредоставлять учетные записи пользователей из Azure AD в Azure Databricks SCIM Connector.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b16eaf27-4bd1-4509-be2c-9a4f66b6badc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2020
ms.author: Zhchia
ms.openlocfilehash: fe1260982edc877c049716bd74f1bb3e90d33b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370524"
---
# <a name="tutorial-configure-azure-databricks-scim-connector-for-automatic-user-provisioning"></a>Учебник: Наконкажирование Azure Databricks SCIM Connector для автоматического подготовки пользователей

В этом уроке описаны шаги, которые необходимо выполнить как в Azure Databricks SCIM Connector, так и в Active Directory Azure (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически провизии и де-положения пользователей и групп в [Azure Databricks SCIM Connector](https://databricks.com/) с помощью службы обеспечения AD Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Возможности, поддерживаемые
> [!div class="checklist"]
> * Создание пользователей в Разъеме SCIM Databricks Azure Databricks
> * Удалите пользователей в разъединении SCIM Datam Azure, если им больше не требуется доступ
> * Сохраняйте синхронизацию атрибутов пользователя между Azure AD и разъемом SCIM Databricks Azure
> * Группы предоставления и членство в группах в Azure Databricks SCIM Connector

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложений или глобальный администратор). 
* Учетная запись Azure Databricks с разрешениями для админов.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания
1. Узнайте о [том, как работает служба обеспечения.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Определите, кто будет находиться в [сфере подготовки.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Определите, какие данные следует [сопоставить между Azure AD и Azure Databricks SCIM Connector.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-azure-databricks-scim-connector-to-support-provisioning-with-azure-ad"></a>Шаг 2. Нанастройка Azure Databricks SCIM Connector для поддержки подготовки с Azure AD

1. Чтобы настроить подготовку Azure Databricks SCIM, добавьте его в качестве ресурса в арендаторAz Active Directory и настройте его с помощью приведенных ниже насепоясных настроек.

    ![Настройка Azure Databricks](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/setup.png)

2. Для создания токена личного доступа в Azure Databricks отсылайте [это](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#token-management)под бортом .

3. Копирование **токена**. Это значение будет введено в поле Secret Token в вкладке «Подготовка» приложения Для подключения данных Databricks на портале Azure.

## <a name="step-3-add-azure-databricks-scim-connector-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавьте разъем SCIM-разъем Azure Databricks из галереи приложений Azure AD

Добавьте разъем SCIM-соединения Azure Databricks из галереи приложений Azure AD, чтобы начать управление подготовкой к разъединению SciM Databricks Az. Если ранее вы настраивали Azure Databricks SCIM Connector для SSO, вы можете использовать то же приложение. Однако рекомендуется создать отдельное приложение при тестировании интеграции на начальном этапе. Подробнее о добавлении приложения из галереи [можно](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)здесь. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определить, кто будет находиться в сфере подготовки 

Служба предоставления AD Azure AD позволяет использовать область охвата, которая будет подготовлена на основе назначения приложения и или на основе атрибутов пользователя/группы. Если вы выбираете область, которая будет подготовлена к вашему приложению на основе назначения, вы можете использовать следующие [шаги](../manage-apps/assign-user-or-group-access-portal.md) для назначения пользователей и групп приложения. Если вы выбираете область, которая будет подготовлена исключительно на основе атрибутов пользователя или группы, вы можете использовать фильтр проверки, как описано [здесь.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* При назначении пользователей и групп в Azure Databricks SCIM Connector необходимо выбрать роль, не связанную с **доступом к по умолчанию.** Пользователи с ролью доступа по умолчанию исключены из подготовки и будут помечены как не имеющие эффективного права в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) чтобы добавить дополнительные роли. 

* Начните с малого. Тест с небольшим набором пользователей и групп, прежде чем выкатить для всех. Когда область подготовки устанавливается для назначенных пользователей и групп, вы можете управлять этим, назначив одному или двум пользователям или группам приложение. Когда область расположена для всех пользователей и групп, можно указать [фильтр сканирования на основе атрибутов.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 


## <a name="step-5-configure-automatic-user-provisioning-to-azure-databricks-scim-connector"></a>Шаг 5. Настройка автоматической подготовки пользователя к разъединению SCIM Databricks Azure Databricks 

В этом разделе вы проведет шаги по настройке службы предоставления AD Azure AD для создания, обновления и отработки пользователей и/или групп в TestApp на основе пользовательских и/или групповых заданий в Azure AD.

> [!NOTE]
> Чтобы узнать больше о прыге SCIM SCIM из Azure Databricks, [обратитесь](https://docs.databricks.com/dev-tools/api/latest/scim.html
)к этому .

### <a name="to-configure-automatic-user-provisioning-for-azure-databricks-scim-connector-in-azure-ad"></a>Для настройки автоматического подключения пользователей к разъединению SCIM Datam В Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Azure Databricks SCIM Connector.**

    ![Ссылка на соединение соединения SCIM Databricks в списке приложений](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Admin Credentials** введомните значение конечных точек SCIM в **URL-адрес клиента.** URL-адрес клиента должен `https://<region>.azuredatabricks.net/api/2.0/preview/scim` быть в формате, в котором **регион** можно найти в URL-адресе домашней страницы Databricks Azure Databricks. Например, конечная точка SCIM для `https://westus.azuredatabricks.net/api/2.0/preview/scim` **западного** региона будет . Ввейте значение токенов, извлеченное ранее в **Secret Token.** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к разъединению SCIM Databricks Azure. Если соединение выходит из строя, убедитесь, что учетная запись SCIM Connector Azure Databricks имеет разрешения Admin и повторите попытку.

    ![Подготовка](./media/azure-databricks-scim-provisioning-connector-provisioning-tutorial/provisioning.png)

6. В поле **"Уведомление email"** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке и выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure в Azure Databricks SCIM Connector.**

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD с разъемом SCIM Databricks в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления учетных записей пользователей в Azure Databricks SCIM Connector для операций обновления. Если вы решите изменить [соответствующий целевой атрибут,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)необходимо убедиться, что API-разъем Для Databricks DATAbricks поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |Атрибут|Тип|
   |---|---|
   |userName|Строка|
   |displayName|Строка|
   |active|Логическое|

10. В разделе **Картпинги** выберите **синхронизацию групп активных каталогов Azure в Azure Databricks SCIM Connector.**

11. Просмотрите атрибуты группы, синхронизированные с Azure AD с разъемом SCIM Databricks в разделе **Атрибут-Картирование.** Атрибуты, выбранные в качестве свойств **соответствия,** используются для сопоставления групп в разъеме SciM Datam Azure Databricks для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

     |Атрибут|Тип|
     |---|---|
     |displayName|Строка|
     |members|Справочник|

11. В разделе **Картпинги** выберите **синхронизацию групп активных каталогов Azure в Azure Databricks SCIM Connector.**

12. Чтобы включить службу предоставления Azure AD для разъема SCIM Databricks Azure Databricks, измените **статус подготовки** **в** разделе **«Настройки».**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

13. Определите пользователей и/или группы, которые вы хотели бы предоставить в Разъем SCIM Databricks Azure, выбрав желаемые значения в **разделе Область** в разделе **«Настройки».**

    ![Область действия подготовки](common/provisioning-scope.png)

14. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **разделе Область** в разделе **Настройки.** Первоначальный цикл занимает больше времени, чем последующие циклы, которые происходят примерно каждые 40 минут при запуске службы обеспечения Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

* Используйте [журналы подготовки,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) чтобы определить, какие пользователи были подготовлены успешно или безуспешно
* Проверьте [планку прогресса,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) чтобы увидеть состояние цикла подготовки и насколько он близок к завершению
* Если конфигурация подготовки, как представляется, в неработоспособном состоянии, приложение будет идти в карантин. Узнайте больше о карантинных состояниях [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
* Databricks всегда преобразует свои значения имени пользователя в более низкий случай при сохранении в каталоге, независимо от капитализации, которая мы отправляем им через SCIM.
* В настоящее время запросы GET в отношении SCIM API Azure USER@contoso.com Databricks для пользователей чувствительны к user@contoso.comслучаям, поэтому, если мы запросим на него, придумаем 0 результатов при хранении.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
