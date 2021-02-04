---
title: Руководство по настройке "abstract" для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как автоматически подготавливать и отзывать учетные записи пользователей из Azure AD в-abstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 50be022f04a5c9fde9758d533adce657763db269
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566858"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Учебник. Настройка независимых параметров для автоматической подготовки пользователей

В этом учебнике описываются действия, которые необходимо выполнить в выпуске и Azure Active Directory (Azure AD) для настройки автоматической подготовки пользователей. После настройки Azure AD автоматически подготавливает и отменяет подготовку пользователей и групп [с помощью службы](https://www.getabstract.com) подготовки Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Поддерживаемые возможности
> [!div class="checklist"]
> * Создание пользователей в выдержки.
> * Удалите пользователей в случае, если им больше не нужен доступ.
> * Обеспечьте синхронизацию атрибутов пользователей между Azure AD и abstract.
> * Подготавливайте группы и членство в группах в выдержки.
> * [Единый вход](getabstract-tutorial.md) в-abstract (рекомендуется)

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

* [Клиент Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Учетная запись пользователя в Azure AD с [разрешением](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) настраивать подготовку (например, администратор приложений, администратор облачных приложений, владелец приложения или глобальный администратор). 
* Неабстрактный клиент (неабстрактная Корпоративная лицензия).
* Единый вход включен в клиенте Azure AD и в виде абстрактного клиента.
* Утверждение и SCIM включение для функции-абстракции (отправка сообщения электронной почты b2b.itsupport@getabstract.com ).

## <a name="step-1-plan-your-provisioning-deployment"></a>Шаг 1. Планирование развертывания для подготовки
1. Узнайте, [как работает служба подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Определите, кто будет находиться в [области подготовки](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Определите, какие данные следует [сопоставлять между Azure AD и abstract](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Шаг 2. Настройка "abstract" для поддержки подготовки с помощью Azure AD
1. Вход в неабстрактный
2. Щелкните значок параметров, расположенный в правом верхнем углу, и выберите параметр **Мой центр администрирования** .
 
    ![очень абстрактный центр администрирования](media/getabstract-provisioning-tutorial/my-account.png)

3. Перейдите к параметру **администратора scim** и щелкните его.
 
    ![Абстрактный администратор SCIM](media/getabstract-provisioning-tutorial/scim-admin.png) 

4. Нажмите кнопку " **Переход** " 

    ![SCIM идентификатор клиента](media/getabstract-provisioning-tutorial/scim-client-go.png)

5. Щелкните " **создать новый токен** ".

    ![SCIM токен 1](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

6. Если вы уверены, что нажмите кнопку **создать новый токен** . В противном случае нажмите кнопку **"Отмена** "

    ![SCIM токен 2](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

7. Наконец, можно либо щелкнуть значок Копировать в буфер обмена, либо выбрать весь маркер и скопировать его. Кроме того, обратите внимание, что URL-адрес клиента или базовый `https://www.getabstract.com/api/scim/v2` . Эти значения будут указаны в поле **секретный токен** * и **URL-адрес клиента** * на вкладке Подготовка приложения "Абстракция" в портал Azure.

    ![Абстрактный токен SCIM 3](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)


## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Шаг 3. Добавление выдержки из коллекции приложений Azure AD

Добавьте параметр "abstract" из коллекции приложений Azure AD, чтобы начать управление подготовкой к выдержки. Если вы ранее настроили функцию abstract для единого входа, вы можете использовать то же приложение. Но мы рекомендуем создать отдельное приложение для исходной проверки интеграции. Дополнительные сведения о добавлении приложения из коллекции см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Шаг 4. Определение пользователей для включения в область подготовки 

Служба подготовки Azure AD позволяет определить пользователей, которые будут подготовлены, на основе назначения приложению и (или) атрибутов пользователя или группы. Если вы решили определить пользователей на основе назначения, выполните следующие [действия](../manage-apps/assign-user-or-group-access-portal.md), чтобы назначить пользователей и группы приложению. Если вы решили определить пользователей только на основе атрибутов пользователя или группы, примените фильтр области, как описано [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* При назначении пользователей и групп в параметре abstract необходимо выбрать роль, отличную от **доступа по умолчанию**. Пользователи с такой ролью исключаются из подготовки и будут помечены в журналах подготовки как не имеющие разрешений. Кроме того, если эта роль является единственной, доступной в приложении, можно [изменить манифест приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), чтобы добавить дополнительные роли. 

* Начните с малого. Протестируйте небольшой набор пользователей и групп, прежде чем выполнять развертывание для всех. Если в область подготовки включены назначенные пользователи и группы, проверьте этот механизм, назначив приложению одного или двух пользователей либо одну или две группы. Если в область включены все пользователи и группы, можно указать [фильтр области на основе атрибутов](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Шаг 5. Настройка автоматической подготовки пользователей в функцию abstract 

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и (или) групп в TestApp на основе их назначений в Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Чтобы настроить автоматическую подготовку учетных записей пользователей в Azure AD, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com). Выберите **Корпоративные приложения**, а затем **Все приложения**.

    ![Колонка "Корпоративные приложения"](common/enterprise-applications.png)

2. В списке приложений выберите параметр " **неабстрактный**".

    ![Ссылка на неабстрактный элемент в списке "приложения"](common/all-applications.png)

3. Выберите вкладку **Подготовка**.

    ![Вкладка "Подготовка"](common/provisioning.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Вкладка "Подготовка" с выделенным значением "Авто"](common/provisioning-automatic.png)

5. В разделе **учетные данные администратора** введите URL-адрес клиента и секретный токен. Нажмите кнопку **проверить подключение** , чтобы убедиться, что Azure AD может подключиться к-abstract. Если подключение не выполняется, убедитесь, что у учетной записи.

    ![Токен](common/provisioning-testconnection-tenanturltoken.png)

6. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Отправить уведомление по электронной почте при сбое**.

    ![Почтовое уведомление](common/provisioning-notification-email.png)

7. Щелкните **Сохранить**.

8. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory пользователей с** помощью параметра abstract.

9. Проверьте пользовательские атрибуты, которые были синхронизированы из Azure AD, в параметре abstract в разделе **сопоставления атрибутов** . Атрибуты, выбранные как свойства **Matching** , используются для сопоставления учетных записей пользователей в параметре abstract для операций обновления. Если вы решили изменить [соответствующий целевой атрибут](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), необходимо убедиться, что API-интерфейс-абстрактная поддерживает фильтрацию пользователей на основе этого атрибута. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

   |attribute|Тип|Поддерживается для фильтрации|
   |---|---|---|
   |userName|Строка|&check;|
   |active|Логическое|
   |emails[type eq "work"].value|Строка|
   |name.givenName|Строка|
   |name.familyName|Строка|
   |externalId|Строка|
   |preferredLanguage|Строка|

10. В разделе **сопоставления** выберите **синхронизировать Azure Active Directory группы в** параметре abstract.

11. В разделе **сопоставление атрибутов** Проверьте атрибуты группы, которые синхронизированы из Azure AD в параметре abstract. Атрибуты, выбранные как свойства **Matching** , используются для сопоставления групп в параметре abstract для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    |attribute|Тип|Поддерживается для фильтрации|
    |---|---|---|
    |displayName|Строка|&check;|
    |externalId|Строка|
    |members|Справочник|
12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для выполнения приабстракции, измените значение параметра **состояние подготовки** на **включено** в разделе **Параметры** .

    ![Состояние подготовки "Включено"](common/provisioning-toggle-on.png)

14. Определите пользователей и (или) группы, которые вы хотите подготавливать к выдержки, выбрав нужные значения в **области** в разделе **Параметры** .

    ![Область действия подготовки](common/provisioning-scope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Сохранение конфигурации подготовки](common/provisioning-configuration-save.png)

После этого начнется цикл начальной синхронизации всех пользователей и групп, определенных в поле **Область** в разделе **Параметры**. Начальный цикл занимает больше времени, чем последующие циклы. Пока служба подготовки Azure AD запущена, они выполняются примерно каждые 40 минут. 

## <a name="step-6-monitor-your-deployment"></a>Шаг 6. Мониторинг развертывания
После настройки подготовки используйте следующие ресурсы для мониторинга развертывания.

* Используйте [журналы подготовки](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), чтобы определить, какие пользователи были подготовлены успешно или неудачно.
* Используйте [индикатор выполнения](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), чтобы узнать состояние цикла подготовки и приблизительное время до его завершения.
* Если конфигурация подготовки, вероятно, находится в неработоспособном состоянии, приложение перейдет в карантин. Дополнительные сведения о режимах карантина см. [здесь](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)
