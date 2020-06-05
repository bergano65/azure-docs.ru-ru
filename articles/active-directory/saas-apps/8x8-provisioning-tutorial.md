---
title: Руководство по Настройка 8x8 для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить автоматическую отмену и подготовку учетных записей пользователей из Azure AD в 8x8.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81b4cde7-4938-4a1a-8495-003c06239b27
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: 6fc14bd41faf6a86953b82f8a7ea7bd75b746cf9
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83801517"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Руководство по Настройка 8x8 для автоматической подготовки пользователей

Это руководство описывает действия, которые необходимо выполнить в 8x8 Configuration Manager и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически осуществляет и отменяет подготовку пользователей и групп для [8x8](https://www.8x8.com) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в 8x8
> * Удаление пользователей в 8x8, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и 8x8
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) в 8x8 (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор).
* Подписка 8x8 X Series любого уровня.
* Учетная запись пользователя 8x8 с разрешением администратора в [Configuration Manager](https://vo-cm.8x8.com).
* [Единый вход с помощью Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) уже настроен.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные следует [сопоставлять между Azure AD и 8x8](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка 8x8 для поддержки подготовки с помощью Azure AD

В этом разделе описывается процедура настройки 8x8 для поддержки подготовки в Azure AD.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>Чтобы настроить маркер доступа для подготовки пользователей в 8x8 Configuration Manager, сделайте следующее:

1. Войдите в [Configuration Manager](https://vo-cm.8x8.com). Выберите **Управления удостоверениями**.

   ![Управление удостоверениями](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Щелкните ссылку **Show user provisioning information** (Показать сведения о подготовке пользователя), чтобы создать маркер.

   ![Отображение подготовки пользователей](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Скопируйте **URL-адрес 8x8** и **токен API 8x8**. Эти значения будут указаны в полях **URL-адрес клиента** и **Секретный маркер** соответственно на вкладке "Подготовка" приложения 8x8 на портале Azure.

   ![Копирование URL-адреса и маркера](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление 8x8 из коллекции приложений Azure AD

Добавьте 8x8 из коллекции приложений Azure AD, чтобы начать управление подготовкой в 8x8. Если вы ранее настроили 8x8 для единого входа, можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей, которые будут находиться в области подготовки

Служба подготовки Azure AD позволяет определить, кто будет подготовлен, в зависимости от назначения приложению и (или) на основе атрибутов пользователя или группы. Если вы решили указать, кто именно будет подготовлен к работе в приложении, на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Это более простой вариант, который используется большинством пользователей.

Если вы решили указать, кто именно будет подготовлен, на основе одних только атрибутов пользователя или группы, можете использовать фильтр задания области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для 8x8 необходимо выбрать роль, отличную от роли **Доступ по умолчанию**. Пользователи с ролью "Доступ по умолчанию" исключаются из подготовки и будут помечены в журналах подготовки как не назначенные явно. Если единственной ролью, доступной в приложении, является роль "Доступ по умолчанию", можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в качестве области подготовки заданы назначенные пользователи и группы, это можно контролировать, назначив приложению одного или двух пользователей либо одну или две группы. Если в качестве области заданы все пользователи и группы, можно указать [фильтр задания области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Шаг 5. Настройка автоматической подготовки пользователей в 8x8 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в 8x8 на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в 8x8, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Колонка "Все приложения"](./media/8x8-provisioning-tutorial/all-applications.png)

2. Из списка приложений выберите **8x8**.

    ![Ссылка на 8x8 в списке приложений](common/all-applications.png)

3. Выберите вкладку **Подготовка**. Нажмите кнопку **Начало работы**.

    ![Вкладка "Подготовка"](common/provisioning.png)

   ![Колонка "Начало работы"](./media/8x8-provisioning-tutorial/get-started.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "Подготовка"](common/provisioning-automatic.png)

5. В разделе **Учетные данные администратора** скопируйте **URL-адрес 8x8** из Configuration Manager в поле **URL-адрес клиента**. Скопируйте **маркер API 8x8** из Configuration Manager в поле **Секретный маркер**. Щелкните **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к 8x8. Если установить подключение не удалось, убедитесь, что у учетной записи 8x8 есть разрешения администратора, и повторите попытку.

    ![Подготовка](./media/8x8-provisioning-tutorial/provisioning.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с 8x8**.

9. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы из Azure AD в 8x8. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в 8x8 для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), потребуется убедиться, что API 8x8 поддерживает фильтрацию пользователей по этому атрибуту. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|Примечания|
   |---|---|---|
   |userName|Строка|Задает имя пользователя и идентификатор федерации|
   |externalId|Строка||
   |active|Логическое||
   |title|Строка||
   |emails[type eq "work"].value|Строка||
   |name.givenName|Строка||
   |name.familyName|Строка||
   |phoneNumbers[type eq "mobile"].value|Строка|Номер личного контакта|
   |phoneNumbers[type eq "work"].value|Строка|Номер личного контакта|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка||
   |urn:ietf:params:scim:schemas:extension:8x8:1.1:User:site|Строка|Невозможно обновить после создания пользователя|
   |локаль|Строка|Не сопоставлено по умолчанию|
   |timezone|Строка|Не сопоставлено по умолчанию|

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для 8x8, в разделе **Параметры** измените значение параметра **Состояние подготовки** на **Включено**.

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей или группы для подготовки в 8x8, выбрав нужные значения в поле **Область** в разделе **Параметры**.

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется начальный цикл синхронизации всех пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Если служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
2. Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и близость его завершения.
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о состояниях карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
