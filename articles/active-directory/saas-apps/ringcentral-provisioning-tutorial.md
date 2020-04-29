---
title: Руководство. Настройка Рингцентрал для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в Рингцентрал.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 976ac071-fa7e-4f31-aed1-d174942860d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: 052223f69fc1c1d59ec5f1bcbeb3746ef7122c86
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79087037"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Учебник. Настройка Рингцентрал для автоматической подготовки пользователей

В этом руководстве описываются действия, которые необходимо выполнить в Рингцентрал и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. При настройке Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп для [рингцентрал](https://www.ringcentral.com/office/plansandpricing.html) с помощью службы подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в Рингцентрал
> * Удалить пользователей в Рингцентрал, когда им больше не нужен доступ
> * Синхронизация атрибутов пользователей между Azure AD и Рингцентрал
> * [Единый вход](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial) в рингцентрал (рекомендуется)

## <a name="prerequisites"></a>Предварительные условия

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD;](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) на настройку подготовки (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* [Клиент Рингцентрал](https://www.ringcentral.com/office/plansandpricing.html)
* Учетная запись пользователя в Рингцентрал с разрешениями администратора.

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания подготовки
1. Узнайте [, как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные должны [сопоставляться между Azure AD и рингцентрал](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-ringcentral-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка Рингцентрал для поддержки подготовки с помощью Azure AD

1. Войдите в [консоль администратора рингцентрал](https://login.ringcentral.com/sw.html). Перейдите к разделу **сервис > интеграция каталогов**.

    ![Консоль администрирования Рингцентрал](media/ringcentral-provisioning-tutorial/admin.png)

2.  В разделе **Выбор поставщика каталога**выберите **scim** . (В будущем будет доступен параметр с именем Azure Active Directory). Щелкните **включить службу scim**.

    ![Рингцентрал добавить SCIM](media/ringcentral-provisioning-tutorial/scim.png)

3.  Чтобы получить matthew.hunt@ringcentral.com **маркер проверки подлинности scim**, обратитесь в службу поддержки рингцентрал по адресу. Это значение будет указано в поле Секретный токен на вкладке Подготовка приложения Рингцентрал в портал Azure.

> [!NOTE]
> Чтобы назначить лицензии пользователям, см. ссылку на видео [здесь](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language).

## <a name="step-3-add-ringcentral-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление Рингцентрал из коллекции приложений Azure AD

Добавьте Рингцентрал из коллекции приложений Azure AD, чтобы начать управление подготовкой в Рингцентрал. Если вы ранее настроили Рингцентрал для единого входа, вы можете использовать то же приложение. Однако при первоначальном тестировании интеграции рекомендуется создать отдельное приложение. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей, которые будут находиться в области подготовки 

Служба подготовки Azure AD позволяет определить, кто будет подготовлен в соответствии с назначением приложения, или на основе атрибутов пользователя или группы. Если вы решили указать, кто будет подготовлен для приложения на основе назначения, можно выполнить следующие [действия](../manage-apps/assign-user-or-group-access-portal.md) , чтобы назначить пользователей и группы для приложения. Если выбрать область, для которой будет выполняться подготовка на основе только атрибутов пользователя или группы, можно использовать фильтр области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп для Рингцентрал необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с ролью доступа по умолчанию исключаются из подготовки и будут помечены как неэффективное в журналах подготовки. Если единственной ролью, доступной в приложении, является роль доступа по умолчанию, можно [обновить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , чтобы добавить дополнительные роли. 

* Запуск Small. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если для параметра область подготовки задано значение назначенные пользователи и группы, это можно контролировать, назначив приложению одного или двух пользователей или групп. Если для параметра scope задано значение все пользователи и группы, можно указать [Фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-ringcentral"></a>Шаг 5. Настройка автоматической подготовки пользователей в Рингцентрал 

В этом разделе описано, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в TestApp на основе назначений пользователей и групп в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей для Рингцентрал в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **корпоративные приложения**, а затем выберите **все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите **RingCentral**.

    ![Ссылка на RingCentral в списке приложений](common/all-applications.png)

3. Перейдите на вкладку **Подготовка** .

    ![Вкладка "подготовка"](common/provisioning.png)

4. Установите для **режима подготовки** значение **автоматически**.

    ![Вкладка "подготовка"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите `https://platform.ringcentral.com/scim/v2` **URL-адрес клиента**. Введите значение **маркера проверки подлинности scim** , полученное ранее в **маркере секрета**. Щелкните **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к рингцентрал. Если подключение не выполняется, убедитесь, что у учетной записи Рингцентрал есть разрешения администратора, и повторите попытку.

    ![Вкладка "подготовка"](./media/ringcentral-provisioning-tutorial/provisioning.png)

6. В поле **уведомление по электронной почте** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, и установите флажок **Отправить уведомление по электронной почте при возникновении сбоя** .

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Нажмите кнопку **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с рингцентрал**.

9. Проверьте атрибуты пользователя, которые синхронизированы из Azure AD в Рингцентрал в разделе " **сопоставление атрибутов** ". Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в рингцентрал для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что API рингцентрал поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|
   |---|---|
   |userName|Строка|
   |externalId|Строка|
   |active|Логическое значение|
   |displayName|Строка|
   |title|Строка|
   |emails[type eq "work"].value|Строка|
   |адреса [Type EQ "Рабочая"]. Country|Строка|
   |адреса [Type EQ "Рабочий"]. регион|Строка|
   |адреса [Введите EQ "Рабочий"]. локальность|Строка|
   |addresses[type eq "work"].postalCode|Строка|
   |addresses[type eq "work"].streetAddress|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |phoneNumbers[type eq "mobile"].value|Строка|
   |urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: Отдел|Строка|
   |urn: IETF: params: scim: схемы: расширение: Enterprise: 2.0: пользователь: менеджер|Справочник|

10. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Чтобы включить службу подготовки Azure AD для Рингцентрал, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

12. Определите пользователей и (или) группы, которые вы хотите подготавливать к Рингцентрал, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

13. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

Эта операция запускает начальный цикл синхронизации всех пользователей и групп, определенных в **области** в разделе **параметров** . Выполнение начального цикла занимает больше времени, чем последующие циклы, что происходит примерно каждые 40 минут, пока выполняется служба подготовки Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания:

1. Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , чтобы определить, какие пользователи были подготовлены успешно или неудачно
2. Просмотрите [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , чтобы просмотреть состояние цикла подготовки и его завершения
3. Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о состояниях карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие шаги

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)