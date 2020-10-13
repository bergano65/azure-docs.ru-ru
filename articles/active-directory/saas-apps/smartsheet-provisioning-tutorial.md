---
title: Руководство. Настройка Smartsheet для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической инициализации и отзыва учетных записей пользователей в Smartsheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 623ec6999add175e85f117e547fba61734d2b892
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91286018"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Учебник. Настройка Smartsheet для автоматической подготовки пользователей

Цель этого руководства — продемонстрировать шаги, которые необходимо выполнить в Smartsheet и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической инициализации и отзыва пользователей и групп в [Smartsheet](https://www.smartsheet.com/pricing). Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в Smartsheet
> * Удалить пользователей в Smartsheet, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и Smartsheet
> * Единый вход в Smartsheet (рекомендуется)

> [!NOTE]
> Сейчас этот соединитель предоставляется в общедоступной предварительной версии. Дополнительные сведения об общих условиях использования продуктов в предварительной версии см. в документе [Дополнительные условия использования Предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор).
* [Клиент Smartsheet](https://www.smartsheet.com/pricing).
* Учетная запись пользователя на корпоративном плане Smartsheet Enterprise или Enterprise с разрешениями системного администратора.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и Smartsheet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка Smartsheet для поддержки подготовки с помощью Azure AD

Перед настройкой Smartsheet для автоматической подготовки пользователей с помощью Azure AD необходимо включить подготовку SCIM для Smartsheet.

1. Войдите в систему как **sysadmin** на **[портале Smartsheet](https://app.smartsheet.com/b/home)** и перейдите к **администратору учетной записи**.

    ![Администратор учетной записи Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Перейдите в раздел средства **управления безопасностью > автоматической подготовки пользователей > изменить**.

    ![Средства управления безопасностью Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Добавьте и проверьте домены электронной почты для пользователей, которые планируется подготавливать из Azure AD в Smartsheet. Выберите **не включено** , чтобы убедиться, что все действия по подготовке созданы только из Azure AD, а также убедитесь, что список пользователей Smartsheet синхронизирован с назначениями Azure AD.

    ![Подготовка пользователей Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. После завершения проверки потребуется активировать домен. 

    ![Smartsheet активировать домен](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Создайте **маркер секрета** , необходимый для настройки автоматической подготовки пользователей с помощью Azure AD, перейдя к **приложениям и интеграции**.

    ![Снимок экрана со страницей администрирования Smartsheet с именем аватара пользователя и параметром интеграции приложений &.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Выберите **доступ через API**. Щелкните **создать новый маркер доступа**.

    ![Снимок экрана с диалоговым окном "личные настройки" с доступом к API и созданием новых параметров маркера доступа, вызываемых.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Определите имя маркера доступа API. Нажмите кнопку **ОК**.

    ![Снимок экрана шага 1 из 2: создание маркера доступа API с параметром "ОК", вызываемым "out".](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Скопируйте маркер доступа API и сохраните его, так как это будет единственный момент, когда вы сможете его просмотреть. Это необходимо в поле **секретного токена** в Azure AD.

    ![Токен Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Smartsheet из коллекции приложений Azure AD

Добавьте Smartsheet из коллекции приложений Azure AD, чтобы начать управление подготовкой в Smartsheet. Если вы ранее настроили Smartsheet для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для Smartsheet необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Чтобы обеспечить четность в назначениях ролей пользователей между Smartsheet и Azure AD, рекомендуется использовать те же назначения ролей, заполненные в списке пользователей Full Smartsheet. Чтобы получить этот список пользователей из Smartsheet, перейдите к **администратору учетной записи > Управление пользователями > другие действия > скачать список пользователей (CSV)**.

* Для доступа к определенным функциям в приложении Smartsheet требуется, чтобы пользователь имел несколько ролей. Чтобы узнать больше о типах пользователей и разрешениях в Smartsheet, перейдите к разделу [типы и разрешения пользователей](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Если у пользователя есть несколько ролей, назначенных в Smartsheet, **необходимо** убедиться, что эти назначения ролей реплицируются в Azure AD, чтобы избежать ситуации, когда пользователи могут навсегда потерять доступ к объектам Smartsheet. Каждая уникальная роль в Smartsheet **должна** быть назначена другой группе в Azure AD. После этого пользователь **должен** быть добавлен в каждую из групп, соответствующих нужным ролям. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Шаг 5. Настройка автоматической подготовки пользователей в Smartsheet 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в Smartsheet на основе назначений пользователей и групп в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Smartsheet в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Smartsheet**.

    ![Ссылка на Smartsheet в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Снимок экрана параметров управления с вызываемым параметром подготовки.](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Снимок экрана: раскрывающийся список режима подготовки с вызываемым автоматическим параметром.](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите **базовый URL-адрес scim 2,0 и значения маркера доступа** , полученные ранее из Smartsheet в **URL-адресе клиента** и **секретном токене** соответственно. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Smartsheet. Если подключение не выполняется, убедитесь, что у учетной записи Smartsheet есть разрешения SysAdmin, и повторите попытку.

    ![Токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Выберите команду **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Smartsheet**.

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Smartsheet в разделе **сопоставление атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Smartsheet для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |active|Логическое|
   |title|Строка|
   |userName|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |phoneNumbers[type eq "work"].value|Строка|
   |phoneNumbers[type eq "mobile"].value|Строка|
   |phoneNumbers[type eq "fax"].value|Строка|
   |externalId|Строка|
   |роли [основной EQ "true"]. Отображение|Строковый тип|
   |роли [основной EQ "true"]. Type|Строка|
   |roles[primary eq "True"].value|Строка|
   |Роли|Строка|
   urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Строка|


10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для Smartsheet, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и (или) группы, которые вы хотите подготавливать к Smartsheet, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Ограничения соединителя

* Smartsheet не поддерживает обратимое удаление. Если атрибуту **активного** пользователя присвоено значение false, Smartsheet удаляет пользователя навсегда.

## <a name="change-log"></a>Журнал изменений

* 06/16/2020 — добавлена поддержка атрибутов расширения предприятия "центр затрат", "деление", "руководитель" и "Отдел" для пользователей.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../app-provisioning/check-status-user-account-provisioning.md)
