---
title: Руководство по Настройка Hootsuite для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить автоматическую подготовку и отмену учетных записей пользователей из Azure AD в Hootsuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fbbee24e-272f-4fa6-819c-10c548bf0215
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2020
ms.author: Zhchia
ms.openlocfilehash: 1adbb0961ab610db936107f2fff210f4acecf2ed
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83882977"
---
# <a name="tutorial-configure-hootsuite-for-automatic-user-provisioning"></a>Руководство по Настройка Hootsuite для автоматической подготовки пользователей

Это руководство описывает действия, которые необходимо выполнить в Hootsuite и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически осуществляет и отменяет подготовку пользователей и групп для [Hootsuite](https://hootsuite.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в Hootsuite.
> * Удаление пользователей в Hootsuite, когда им больше не нужен доступ.
> * Синхронизация атрибутов пользователей между Azure AD и Hootsuite.
> * Подготовка групп и членства в группах в Hootsuite.
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial) в Hootsuite (рекомендуется).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* Учетная запись пользователя в [Hootsuite](http://www.hootsuite.com/) с разрешениями на **управление участниками** в организации.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные следует [сопоставлять между Azure AD и Hootsuite](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-hootsuite-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка Hootsuite для поддержки подготовки с помощью Azure AD

Чтобы получить долгосрочный токен секрета, который потребуется в последующих шагах, напишите на адрес электронной почты dev.support@hootsuite.com. 

## <a name="step-3-add-hootsuite-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Hootsuite из коллекции приложений Azure AD

Добавьте Hootsuite из коллекции приложений Azure AD, чтобы начать управление подготовкой в Hootsuite. Если вы ранее настроили Hootsuite для единого входа, можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей, которые будут находиться в области подготовки 

Служба подготовки Azure AD позволяет определить, кто будет подготовлен, в зависимости от назначения приложению и (или) на основе атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для Hootsuite необходимо выбрать роль, отличную от роли **Доступ по умолчанию**. Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Если единственной ролью, доступной в приложении, является роль "Доступ по умолчанию", можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в качестве области подготовки заданы назначенные пользователи и группы, это можно контролировать, назначив приложению одного или двух пользователей либо одну или две группы. Если в качестве области заданы все пользователи и группы, можно указать [фильтр задания области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-hootsuite"></a>Шаг 5. Настройка автоматической подготовки пользователей в Hootsuite 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-hootsuite-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в Hootsuite:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](./media/hootsuite-provisioning-tutorial/enterprise-applications.png)

    ![Колонка "Все приложения"](./media/hootsuite-provisioning-tutorial/all-applications.png)

2. В списке приложений выберите **Hootsuite**.

    ![Ссылка на Hootsuite в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**. Нажмите кнопку **Начало работы**.

    ![Вкладка "Подготовка"](common/provisioning.png)

    ![Колонка "Начало работы"](./media/hootsuite-provisioning-tutorial/get-started.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "Подготовка"](common/provisioning-automatic.png)

5. В разделе **Учетные данные администратора** введите значение `https://platform.hootsuite.com/scim/v2` в поле "URL-адрес клиента". Введите значение долгосрочного токена секрета, полученное ранее на **шаге 2**. Щелкните **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к Hootsuite. Если установить подключение не удалось, убедитесь, что у учетной записи Hootsuite есть разрешения администратора, и повторите попытку.

    ![Подготовка](./media/hootsuite-provisioning-tutorial/provisioning.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **Сопоставления** выберите **Подготовка пользователей Azure Active Directory**.

9. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в Hootsuite. Атрибуты, выбранные как свойства **Сопоставление**, используются для сопоставления учетных записей пользователей в Hootsuite для операций обновления. Если вы решили изменить [целевой атрибут сопоставления](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), потребуется убедиться, что API Hootsuite поддерживает фильтрацию пользователей по этому атрибуту. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |emails[type eq "work"].value|Строка|
   |active|Логическое|
   |displayName|Строка|
   |preferredLanguage|Строка|
   |timezone|Строка|
   |urn:ietf:params:scim:schemas:extension:Hootsuite:2.0:User:organizationIds|Строка|
   |urn:ietf:params:scim:schemas:extension:Hootsuite:2.0:User:teamIds|Строка|

10. Чтобы включить службу подготовки Azure AD для Hootsuite, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

11. Определите пользователей или группы для подготовки в Hootsuite, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Область действия подготовки](common/provisioning-scope.png)

12. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальный цикл синхронизации всех пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Если служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

* Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определять, какие пользователи были подготовлены успешно или неудачно.
* Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнавать состояние цикла подготовки и близость его завершения.
* Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о состояниях карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
