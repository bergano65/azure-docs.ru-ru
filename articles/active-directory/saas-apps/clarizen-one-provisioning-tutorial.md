---
title: Руководство. Настройка Clarizen для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD, чтобы Clarizen их.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 133e12450f01e14f2204810f0d24cd28b5f948f8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795374"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Руководство. Настройка Clarizen для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить в Clarizen один и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп для [Clarizen одной](https://www.clarizen.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в Clarizen один
> * Удалить пользователей в Clarizen, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и Clarizen 1
> * Подготавливайте группы и членство в группах в Clarizen одной
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/clarizen-tutorial) в Clarizen (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) настраивать подготовку (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* Учетная запись пользователя в Clarizen одна с [разрешениями](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)для **интеграции** и **администраторами Lite** .

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и Clarizen одной](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка Clarizen для поддержки подготовки с помощью Azure AD
1. Выберите один из четырех следующих URL-адресов клиента в соответствии с Clarizen одной среды и центром обработки данных:
      * Центр данных для рабочих центров США: https://servicesapp2.clarizen.com/scim/v2
      * Рабочий центр данных ЕС: https://serviceseu1.clarizen.com/scim/v2
      * Центр обработки данных песочницы США: https://servicesapp.clarizentb.com/scim/v2
      * Центр обработки данных "песочницы ЕС": https://serviceseu.clarizentb.com/scim/v2

2. Создание [ключа API](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Это значение будет указано в поле **секретный токен** на вкладке Подготовка Clarizen одного приложения в портал Azure.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Clarizen из коллекции приложений Azure AD

Добавьте Clarizen один из коллекции приложений Azure AD, чтобы начать управление подготовкой для Clarizen одного из них. Если вы уже выполнили настройку Clarizen для единого входа, можно использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп, Clarizen их, необходимо выбрать роль, отличную от **доступа по умолчанию** . Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Шаг 5. Настройка автоматической подготовки пользователей для Clarizen 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Clarizen 1 в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения** , а затем **Все приложения** .

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Clarizen One** .

    ![Ссылка на Clarizen в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка** .

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Автоматическая вкладка подготовки](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите Clarizen URL-адрес одного клиента и секретный маркер. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Clarizen. В случае сбоя подключения убедитесь, что у вашей учетной записи Clarizen есть права администратора, и повторите попытку.

    ![Токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое** .

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить** .

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей, чтобы Clarizen их** .

9. Проверьте пользовательские атрибуты, которые синхронизированы из Azure AD, чтобы Clarizen их в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Clarizen одной для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что CLARIZEN один API поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить** , чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |displayName|Строка|
   |active|Логическое|
   |title|Строка|
   |emails[type eq "work"].value|Строка|
   |сообщения электронной почты [Type EQ "Home"]. значение|Строка|
   |сообщения электронной почты [Введите EQ "другое"]. значение|Строка|
   |preferredLanguage|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |name.formatted|Строка|
   |name.honorificPrefix|Строка|
   |имя. Хонорификсуффикс|Строка|
   |адреса [Введите EQ "Other"]. форматированный|Строка|
   |addresses[type eq "work"].formatted|Строка|
   |addresses[type eq "work"].country|Строка|
   |addresses[type eq "work"].region|Строка|
   |addresses[type eq "work"].locality|Строка|
   |addresses[type eq "work"].postalCode|Строка|
   |addresses[type eq "work"].streetAddress|Строка|
   |phoneNumbers[type eq "work"].value|Строка|
   |phoneNumbers[type eq "mobile"].value|Строка|
   |phoneNumbers[type eq "fax"].value|Строка|
   |Фоненумберс [Type EQ "Home"]. значение|Строка|
   |Фоненумберс [Введите EQ "другое"]. Value|Строка|
   |Фоненумберс [Type EQ "пейджер"]. Value|Строка|
   |externalId|Строка|
   |nickName|Строка|
   |локаль|Строка|
   |роли [основной EQ "true". Type]|Строка|
   |роли [первичный EQ "true". Value]|Строка|
   |timezone|Строка|
   |userType|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Справочник|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Строка|

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы, чтобы Clarizen их** .

11. Проверьте атрибуты группы, которые синхронизированы из Azure AD, чтобы Clarizen их в разделе **сопоставления атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в Clarizen одной для операций обновления. Нажмите кнопку **Сохранить** , чтобы зафиксировать все изменения.

      |attribute|Тип|
      |---|---|
      |displayName|Строка|
      |externalId|Строка|
      |members|Справочник|

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для Clarizen, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать для Clarizen, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить** .

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры** . Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Советы по устранению неполадок
При назначении пользователя для Clarizen одного приложения коллекции выберите только роль **пользователя** . Следующие роли показаны недопустимыми.

* Администратора (администратор)
* Пользователь отчетов по электронной почте
* Внешний пользователь
* Финансовый пользователь
* Пользователь социальных сетей
* Суперпользователь
* Время & расходов пользователя



## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
