---
title: Руководство. Настройка пути Juno для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в Juno путешествие.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 79813b19-c96e-4459-a4e5-636a6b6f5041
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2020
ms.author: Zhchia
ms.openlocfilehash: 08de07a52d1e43dea91e6684d33027d8bcad61fc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641853"
---
# <a name="tutorial-configure-juno-journey-for-automatic-user-provisioning"></a>Учебник. Настройка пути Juno для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить как в Junoм пути, так и в Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп для [Junoного пути](https://www.junojourney.com/) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в Junoном пути
> * Удалить пользователей в Junoм пути, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и Junoным путешествием
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial) в Junoное путешествие (рекомендуется)

## <a name="prerequisites"></a>Предварительные условия

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
*  [Клиент Junoного пути](https://www.junojourney.com/getstartedwithjuno).
*  Учетная запись пользователя в Juno путешествие с разрешениями администратора.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания подготовки
1. Узнайте [, как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и Juno путешествием](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-juno-journey-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка пути Juno для поддержки подготовки с помощью Azure AD

1. Для получения **секретного маркера** и **URL-адреса клиента** обратитесь support@the-juno.comк группе поддержки Juno. Это значение будет указано в полях **секретный токен** и **URL-адрес клиента** , соответственно на вкладке Подготовка приложения Junoного пути в портал Azure. 

## <a name="step-3-add-juno-journey-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Juno из коллекции приложений Azure AD

Добавьте Juno из коллекции приложений Azure AD, чтобы начать управление подготовкой для Junoного пути. Если вы ранее настроили Juno для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей, которые будут находиться в области подготовки 

Служба подготовки Azure AD позволяет определить, кто будет подготовлен в соответствии с назначением приложения, или на основе атрибутов пользователя или группы. Если вы решили указать, кто будет подготовлен для приложения на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md) , чтобы назначить пользователей и группы для приложения. Если выбрать область, для которой будет выполняться подготовка на основе только атрибутов пользователя или группы, можно использовать фильтр области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для Junoного пути необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью доступа по умолчанию исключаются из подготовки и будут помечены как неэффективное в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , чтобы добавить дополнительные роли. 

* Запуск Small. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если для параметра область подготовки задано значение назначенные пользователи и группы, это можно контролировать, назначив приложению одного или двух пользователей или групп. Если для параметра scope задано значение все пользователи и группы, можно указать [Фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-juno-journey"></a>Шаг 5. Настройка автоматической подготовки пользователей в Junoм пути 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в TestApp на основе назначений пользователей и групп в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-juno-journey-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей для Junoного пути в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **Juno Journey**.

    ![Ссылка на Junoное путешествие в списке "приложения"](common/all-applications.png)

3. Перейдите на вкладку **Подготовка** .

    ![Вкладка "подготовка"](common/provisioning.png)

4. Установите для **режима подготовки** значение **автоматически**.

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите значение URL-адреса клиента, полученное ранее в поле **URL-адрес клиента**. Введите значение секретного токена, полученное ранее в **маркере секрета**. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к Juno пути. В случае сбоя подключения убедитесь, что учетная запись Juno путешествие имеет разрешения администратора, и повторите попытку.

    ![Подготовка](./media/juno-journey-provisioning-tutorial/provisioning.png)

6. В поле **уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, и установите флажок **Отправить уведомление по электронной почте при возникновении сбоя** .

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с Junoным путешествием**.

9. Изучите пользовательские атрибуты, которые синхронизированы из Azure AD, чтобы Juno в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в Juno пути для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что API Junoного пути поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |Переменная|Type|
   |---|---|
   |userName|Строка|
   |externalId|Строка|
   |displayName|Строка|
   |title|Строка|
   |active|Логическое значение|
   |preferredLanguage|Строка|
   |emails[type eq "work"].value|Строка|
   |адреса [Type EQ "Рабочая"]. Country|Строка|
   |адреса [Type EQ "Рабочий"]. регион|Строка|
   |адреса [Введите EQ "Рабочий"]. локальность|Строка|
   |addresses[type eq "work"].postalCode|Строка|
   |адреса [Type EQ "Рабочая"]. форматированный|Строка|
   |addresses[type eq "work"].streetAddress|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |имя. middleName|Строка|
   |Name. форматируются|Строка|
   |phoneNumbers[type eq "fax"].value|Строка|
   |phoneNumbers[type eq "mobile"].value|Строка|
   |phoneNumbers[type eq "work"].value|Строка|
   |urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: Отдел|Строка|
   |urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: Емплойинумбер|Строка|
   |urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: costCenter|Строка|
   urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: деление|Строка|
   urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: менеджер|Строка|
   urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: Организация|Строка|


10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для Junoного пути, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и (или) группы, которые вы хотите подготавливать для Junoного пути, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **области** в разделе **параметров** . Выполнение начального цикла занимает больше времени, чем последующие циклы, что происходит примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

* Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , чтобы определить, какие пользователи были подготовлены успешно или неудачно
* Просмотрите [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , чтобы просмотреть состояние цикла подготовки и его завершения
* Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о состояниях карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
