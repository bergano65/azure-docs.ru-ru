---
title: Руководство по настройке Workplace by Facebook для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603206"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Руководство по настройке Workplace by Facebook для автоматической подготовки пользователей

В этом уроке описаны шаги, которые необходимо выполнить как на рабочем месте, так и в Facebook и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически провизии и де-положения пользователей и групп на [рабочем месте facebook](https://work.workplace.com/) с помощью службы обеспечения Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Переход на новое рабочее место с помощью приложения Facebook
Если у вас есть существующая интеграция с Workplace на Facebook, пожалуйста, смотрите ниже раздел о предстоящих изменениях. Если вы настраиваете Workplace на Facebook в первый раз, вы можете пропустить этот раздел и перейти к поддерживаемым возможностям. 

#### <a name="whats-changing"></a>Изменения
* Изменения со стороны Azure AD: Метод авторизации для предоставления пользователям на рабочем месте исторически был давно секретным маркером. Вскоре вы увидите, что метод авторизации изменен на грант авторизации OAuth. 
* Изменения на рабочем месте стороны: Ранее приложение Azure AD было пользовательской интеграции на рабочем месте на Facebook. Теперь вы увидите Azure AD в каталоге интеграций workplace как стороннее приложение. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Что нужно сделать, чтобы перенести существующую пользовательскую интеграцию в новое приложение?
Если у вас есть существующая интеграция Workplace с действительным маркером, **никаких действий не требуется.** Каждую неделю мы автоматически мигрируем клиентов в новое приложение. Это делается полностью за кулисами. Если вы не можете ждать и хотите перейти к новому приложению вручную, вы можете добавить новый экземпляр Workplace из галереи и настроить подготовку снова. Все новые экземпляры Workplace будут автоматически использовать новую версию приложения. 

 
Если ваша интеграция workplace находится в карантине, вам нужно будет снова предоставить действительный токен, чтобы мы могли перенести вас. Раздел учетных данных админа будет серым, но вы можете прибужено следующее (**? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride)** на ваш URL,снова сохранитучет учетных данных. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Как усомниться в том, что мое приложение было перенесено? 
При переносе приложения баннер в разделе авторизации об изменениях upcomming будет удален, а секретное поле маркеров будет заменено синюю кнопку авторизации. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>Раздел учетных данных админа в моем приложении седой, и я не могу сохранить. Почему?
Мы заблокировали раздел учетных данных для существующих клиентов Workplace. Когда ваш арендатор был перенесен в новое приложение Workplace, вы сможете снова обновить раздел учетных данных админов. Если вы не можете ждать, вы можете использовать URL выше, чтобы отсеить приложение. 

 
#### <a name="when-will-these-changes-happen"></a>Когда эти изменения произойдут?
Все новые экземпляры Workplace уже будут использовать новый метод интеграции/авторизации. Существующие интеграции будут постепенно мигрировать к маю. Рабочая группа предоставила продление срока с 28 февраля по 1 мая. 

## <a name="capabilities-supported"></a>Возможности, поддерживаемые
> [!div class="checklist"]
> * Создание пользователей на рабочем месте на Facebook
> * Удалите пользователей на рабочем месте на Facebook, когда они не требуют доступа больше
> * Сохраняйте синхронизацию атрибутов пользователя между Azure AD и Workplace facebook
> * [Однократный вданный на](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) рабочее место на Facebook (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложений или глобальный администратор)
* подписка Workplace by Facebook с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания
1. Узнайте о [том, как работает служба обеспечения.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Определите, кто будет находиться в [сфере подготовки.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)
3. Определите, какие данные следует [сопоставить между Azure AD и Workplace по Facebook.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка Workplace на Facebook для поддержки подготовки с Azure AD

Перед настройкой и включением службы подготовки необходимо решить, какие пользователи или группы в Azure AD представляют пользователей, которым требуется доступ к приложению Workplace by Facebook. Приняв это решение, можно будет назначить этих пользователей для приложения Workplace by Facebook, выполнив следующие действия.

*   Рекомендуется назначить одного пользователя Azure AD в Workplace by Facebook для тестирования конфигурации подготовки. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя Workplace by Facebook необходимо выбрать допустимую роль пользователя. Роль "Доступ по умолчанию" не работает для подготовки.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Workplace на Facebook из галереи приложений Azure AD

Добавьте Workplace by Facebook из галереи приложений Azure AD, чтобы начать управление подготовкой к Workplace через Facebook. Если вы ранее настраивали Workplace на Facebook для SSO, вы можете использовать то же приложение. Однако рекомендуется создать отдельное приложение при тестировании интеграции на начальном этапе. Подробнее о добавлении приложения из галереи [можно](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)здесь.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определить, кто будет находиться в сфере подготовки 

Служба предоставления AD Azure AD позволяет использовать область охвата, которая будет подготовлена на основе назначения приложения и или на основе атрибутов пользователя/группы. Если вы выбираете область, которая будет подготовлена к вашему приложению на основе назначения, вы можете использовать следующие [шаги](../manage-apps/assign-user-or-group-access-portal.md) для назначения пользователей и групп приложения. Если вы выбираете область, которая будет подготовлена исключительно на основе атрибутов пользователя или группы, вы можете использовать фильтр проверки, как описано [здесь.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

* При назначении пользователей и групп на рабочее место facebook, вы должны выбрать роль, кроме **доступа по умолчанию.** Пользователи с ролью доступа по умолчанию исключены из подготовки и будут помечены как не имеющие эффективного права в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения,](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) чтобы добавить дополнительные роли. 

* Начните с малого. Тест с небольшим набором пользователей и групп, прежде чем выкатить для всех. Когда область подготовки устанавливается для назначенных пользователей и групп, вы можете управлять этим, назначив одному или двум пользователям или группам приложение. Когда область расположена для всех пользователей и групп, можно указать [фильтр сканирования на основе атрибутов.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения,** затем выберите **все приложения.**

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. Из списка приложений выберите **Workplace by Facebook**.

    ![Ссылка на Workplace by Facebook в списке "Приложения"](common/all-applications.png)

3. Выберите вкладку **«Подготовка».**

    ![Вкладка подготовки](common/provisioning.png)

4. Установите **режим обеспечения** **автоматического.**

    ![Вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **Полномочия админа** нажмите на **Authorize**. Вы будете перенаправлены на Рабочее место по странице авторизации Facebook. Ввейте рабочее место по имени пользователя Facebook и нажмите на кнопку **«Продолжить».** Нажмите **на тестовое соединение,** чтобы обеспечить подключение Azure AD к рабочему месту на Facebook. Если соединение не удается, убедитесь, что ваша учетная запись Workplace по Facebook имеет разрешения admin и повторите попытку.

    ![Подготовка](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![Авторизация](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. В поле **"Уведомление email"** введите адрес электронной почты человека или группы, которые должны получать уведомления об ошибке и выберите **уведомление отправить сообщение по электронной почте, когда происходит сбой.**

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **Картпинги** выберите **синхронизацию активных пользователей каталога Azure на рабочем месте на Facebook.**

9. Просмотрите атрибуты пользователя, синхронизированные с Azure AD на workplace, Facebook в разделе **«Отображение атрибутов».** Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Workplace by Facebook для операций обновления. Если вы решите изменить [соответствующий целевой атрибут,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)необходимо убедиться, что Workplace by Facebook API поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |Атрибут|Тип|
   |---|---|
   |userName|Строка|
   |displayName|Строка|
   |active|Логическое|
   |title|Логическое|
   |emails[type eq "work"].value|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |name.formatted|Строка|
   |адреса eq "работа"..formatted|Строка|
   |addresses[type eq "work"].streetAddress|Строка|
   |адреса eq "работа"..местность|Строка|
   |адреса eq "работа".регион|Строка|
   |адреса »тип eq «работа»».|Строка|
   |addresses[type eq "work"].postalCode|Строка|
   |адреса eq "другие"...formatted|Строка|
   |phoneNumbers[type eq "work"].value|Строка|
   |phoneNumbers[type eq "mobile"].value|Строка|
   |phoneNumbers[type eq "fax"].value|Строка|
   |externalId|Строка|
   |preferredLanguage|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Строка|
   |урна:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу предоставления услуг Azure AD для Workplace на Facebook, измените **статус подготовки** в разделе **«Настройки».** **On**

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и/или группы, которые вы хотели бы предоставить на рабочее место на Facebook, выбрав желаемые значения в **разделе Область** в разделе **Параметры.**

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **разделе Область** в разделе **Настройки.** Первоначальный цикл занимает больше времени, чем последующие циклы, которые происходят примерно каждые 40 минут при запуске службы обеспечения Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

1. Используйте [журналы подготовки,](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) чтобы определить, какие пользователи были подготовлены успешно или безуспешно
2. Проверьте [планку прогресса,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) чтобы увидеть состояние цикла подготовки и насколько он близок к завершению
3. Если конфигурация подготовки, как представляется, в неработоспособном состоянии, приложение будет идти в карантин. Узнайте больше о карантинных состояниях [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
*  Если вы видите пользователя неудачно созданным и есть событие журнала аудита с кодом "1789003", это означает, что пользователь из непроверенного домена.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход в каталог Azure Active?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
