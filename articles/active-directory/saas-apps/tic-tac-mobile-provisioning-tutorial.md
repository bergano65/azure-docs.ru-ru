---
title: Руководство. Настройка Tic-Tac Mobile для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отменять предоставление учетных записей пользователей из Azure AD на Tic-Tac Mobile.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 99fe2f8356b53b5d8e459dbf62534ddb4f0019b2
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357272"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Руководство. Настройка Tic-Tac Mobile для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить в Tic-Tac Mobile и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп к [крестики Mobile](https://www.tictacmobile.com/) с помощью службы подготовки Azure AD. Сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. [в статье Автоматизация подготовки пользователей и ее отмены в Azure AD](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Поддерживаемые возможности

> [!div class="checklist"]
> * Создание пользователей в Tic-Tac Mobile.
> * Удалите пользователей в Tic-Tac Mobile, когда им больше не нужен доступ.
> * Обеспечьте синхронизацию атрибутов пользователей между Azure AD и Tic-Tac Mobile.

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки. Примерами могут быть администраторы приложений, администратор облачных приложений, владелец приложения или глобальный администратор.
* [Мобильная](https://www.tictacmobile.com/) учетная запись крестики с ролью суперпользователя.


## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки

1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Определите, какие данные должны [сопоставляться между Azure AD и Tic-Tac Mobile](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка Tic-Tac Mobile для поддержки подготовки с помощью Azure AD

Обратитесь support@tictacmobile.com к, чтобы получить **URL-адрес клиента** и **секретный маркер**. Для получения маркера необходимо иметь роль суперпользователя в Tic-Tac Mobile. Маркер будет добавлен в поле **секретный токен** на вкладке **Подготовка** Tic-Tac мобильного приложения в портал Azure.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Tic-Tac Mobile из коллекции приложений Azure AD

Добавьте Tic-Tac Mobile из коллекции приложений Azure AD, чтобы начать управление подготовкой для Tic-Tac мобильных устройств. Если вы ранее настроили Tic-Tac Mobile для единого входа, можно использовать то же приложение. При первоначальном тестировании интеграции создайте отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. в разделе [Подготовка приложений на основе атрибутов с помощью фильтров области](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки

С помощью службы подготовки Azure AD можно определить, кто будет подготовлен в соответствии с назначением приложения, или на основе атрибутов пользователя или группы. Если вы решили указать, кто будет подготовлен для приложения на основе назначения, выполните действия, описанные в разделе [Управление назначением пользователей для приложения в Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) , чтобы назначить пользователей и группы для приложения. Если вы решили указать, кто будет подготовлен на основе только атрибутов пользователя или группы, используйте фильтр области, как описано в разделе [Подготовка приложений на основе атрибутов с помощью фильтров области](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* При назначении пользователей и групп для Tic-Tac Mobile необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью доступа по умолчанию исключаются из подготовки и будут помечены как неэффективное в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , чтобы добавить дополнительные роли.
* Начните с малого. Протестируйте небольшое количество пользователей и групп, прежде чем выполнять развертывание для всех. Если для параметра область подготовки задано значение назначенные пользователи и группы, вы можете поддерживать управление, назначив приложению одного или двух пользователей или групп. Если для параметра scope задано значение все пользователи и группы, можно указать [Фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Шаг 5. Настройка автоматической подготовки пользователей для Tic-Tac мобильных устройств

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей или групп в TestApp на основе назначений пользователей или групп в Azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Настройка автоматической подготовки пользователей для Tic-Tac Mobile в Azure AD

1. Войдите на [портал Azure](https://portal.azure.com). Щелкните **Корпоративные приложения** > **Все приложения**.

    ![Снимок экрана, на котором показана область "корпоративные приложения".](common/enterprise-applications.png)

1. В списке приложений выберите **крестики-список_компонентов Mobile**.

    ![Снимок экрана, на котором показана ссылка Tic-Tac Mobile в списке "приложения".](common/all-applications.png)

1. Выберите вкладку **Подготовка**.

    ![Снимок экрана, на котором показана вкладка "подготовка".](common/provisioning.png)

1. Для параметра **Режим подготовки к работе** выберите значение **Автоматически**.

    ![Снимок экрана, на котором отображается вкладка "подготовка" для автоматического выбора.](common/provisioning-automatic.png)

1. В разделе **учетные данные администратора** введите **URL-адрес мобильного клиента** Tic-Tac и **маркер секрета**. Выберите **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Tic-Tac Mobile. В случае сбоя подключения убедитесь, что у учетной записи Tic-Tac Mobile есть разрешения администратора, и повторите попытку.

    ![Снимок экрана, на котором показано поле секретного токена.](common/provisioning-testconnection-tenanturltoken.png)

1. В поле e-mail **Notification (уведомление** ) введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки. Установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Снимок экрана, на котором отображается поле уведомления по электронной почте.](common/provisioning-notification-email.png)

1. Нажмите кнопку **Сохранить**.

1. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Tic-Tac Mobile**.

1. Проверьте пользовательские атрибуты, которые синхронизированы из Azure AD, чтобы Tic-Tac Mobile в разделе **сопоставления атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Tic-Tac Mobile для операций обновления. При изменении [соответствующего целевого атрибута](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)необходимо убедиться, что Tic-Tac Mobile API поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить** , чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |externalId|Строка|
   |title|Строка|
   |сообщения электронной почты [Type EQ "Рабочая"]. Value|Строка|
   |preferredLanguage|Строка|
   |externalId|Строка|
   |userType|Строка|
   |локаль|Строка|
   |timezone|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|Строка|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Строка|

1. Инструкции по настройке фильтров области см. в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Чтобы включить службу подготовки Azure AD для Tic-Tac Mobile, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Снимок экрана, на котором показано состояние подготовки.](common/provisioning-toggle-on.png)

1. Определите пользователей или группы, которые вы хотите подготавливать для Tic-Tac Mobile, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Снимок экрана, на котором показана область подготовки.](common/provisioning-scope.png)

1. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Снимок экрана, на котором показано сохранение конфигурации подготовки.](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут.

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания

После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
1. Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и близость его завершения.
1. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о состояниях карантина см. в статье [Подготовка приложений в карантинном состоянии](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
