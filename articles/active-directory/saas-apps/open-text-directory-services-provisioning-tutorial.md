---
title: Руководство. Настройка служб каталогов OpenText для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в службы каталогов OpenText.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: c0858c2e5b1ada866d252e45113f0f90b73caaf8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428966"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Учебник. Настройка служб каталогов OpenText для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить в OpenText Directory Services и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп для OpenText служб каталогов с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в службах каталогов OpenText
> * Удалять пользователей в службах каталогов OpenText, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и службами каталогов OpenText
> * Подготавливайте группы и членство в группах в OpenText Directory Services
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial) в OpenText Directory Services (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) настраивать подготовку (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* Установка ОТДС, доступная в Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между службами каталогов Azure AD и OpenText](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка служб каталогов OpenText для поддержки подготовки с помощью Azure AD

> [!NOTE]
> Приведенные ниже действия применимы к установке служб каталогов OpenText. Они не применяются к клиентам OpenText Корешаре или OpenText OT2.

1. Создайте выделенный конфиденциальный **клиент OAuth**.
2. Задайте длительное **время существования маркера доступа**.

      ![Жизненный цикл доступа к маркеру](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. Не указывайте URL-адреса перенаправления. Они не являются обязательными. 
4. ОТДС создаст и отобразит **секрет клиента**. Сохраните **идентификатор клиента** и **секрет клиента** в безопасном месте.

      ![Секрет клиента](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Создайте секцию для пользователей и групп, которые будут синхронизированы из Azure AD.

      ![Страница секционирования](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Предоставьте административные права клиенту OAuth, созданному в разделе, который будет использоваться для синхронизации пользователей и групп Azure AD.    
      * Действия Partition->-> изменение администраторов

      ![Страница администратора](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. Маркер секрета должен быть извлечен и настроен в Azure AD. Для этого можно использовать любое клиентское приложение HTTP. Ниже приведены инструкции по получению с помощью приложения API Swagger, входящего в ОТДС.
      * В веб-браузере перейдите по адресу {ОТДС URL}/otdsws/OAuth2
      * Перейдите по адресу/Token и щелкните значок замка в правом верхнем углу. Введите идентификатор и секрет клиента OAuth, полученные ранее, в качестве имени пользователя и пароля соответственно. Нажмите кнопку "Авторизовать".

      ![Кнопка авторизации](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Выберите **client_credentials** для grant_type и нажмите кнопку **выполнить**.

      ![Выполнить настраиваемой](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. Маркер доступа в ответе следует использовать в поле **секретного токена** в Azure AD.

      ![Маркер доступа](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление служб каталогов OpenText из коллекции приложений Azure AD

Добавьте службы каталогов OpenText из коллекции приложений Azure AD, чтобы начать управление подготовкой к работе со службами каталогов OpenText. Если вы ранее настроили службы каталогов OpenText для единого входа, можно использовать одно и то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп службам каталогов OpenText необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Шаг 5. Настройка автоматической подготовки пользователей в OpenText Directory Services 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей для служб каталогов OpenText в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **OpenText Directory Services**.

    ![Ссылка на службы каталогов OpenText в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Автоматическая вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите URL-адрес клиента службы каталогов OpenText.
   * URL-адрес клиента, не являющегося спеЦифк: {URL-адрес ОТДС}/СЦим/{партитионнаме}
   * Указанный URL-адрес клиента: {ОТДС URL}/Отдстенант/{тенантид}/сЦим/{партитионнаме}

6. Введите маркер секрета, полученный на шаге 2. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к службам каталогов OpenText. В случае сбоя подключения убедитесь, что учетная запись служб каталогов OpenText имеет разрешения администратора, и повторите попытку.

      ![Токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с OpenText Directory Services**.

9. Изучите пользовательские атрибуты, синхронизированные из Azure AD, с OpenText Directory Services в разделе **сопоставления атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в OpenText Directory Services для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что API службы каталогов OpenText поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |active|Логическое|
   |displayName|Строка|
   |title|Строка|
   |emails[type eq "work"].value|Строка|
   |preferredLanguage|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |name.formatted|Строка|
   |addresses[type eq "work"].formatted|Строка|
   |addresses[type eq "work"].streetAddress|Строка|
   |addresses[type eq "work"].locality|Строка|
   |addresses[type eq "work"].region|Строка|
   |addresses[type eq "work"].postalCode|Строка|
   |addresses[type eq "work"].country|Строка|
   |phoneNumbers[type eq "work"].value|Строка|
   |phoneNumbers[type eq "mobile"].value|Строка|
   |phoneNumbers[type eq "fax"].value|Строка|
   |externalId|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Справочник| 

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы со службами каталогов OpenText**.

11. Изучите атрибуты группы, синхронизированные из Azure AD, с OpenText Directory Services в разделе **сопоставления атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в OpenText Directory Services для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

      |attribute|Тип|
      |---|---|
      |displayName|Строка|
      |externalId|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для служб каталогов OpenText, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать к службам каталогов OpenText, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
