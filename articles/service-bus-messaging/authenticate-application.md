---
title: Проверка подлинности приложения для доступа к сущностям служебной шины Azure
description: В этой статье содержатся сведения о проверке подлинности приложения с помощью Azure Active Directory для доступа к сущностям служебной шины Azure (очереди, разделы и т. д.).
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996989"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Проверка подлинности и авторизация приложения с помощью Azure Active Directory для доступа к сущностям служебной шины Azure
Служебная шина Azure поддерживает использование Azure Active Directory (Azure AD) для авторизации запросов к сущностям служебной шины (очередям, темам, подпискам или фильтрам). С помощью Azure AD можно использовать управление доступом на основе ролей (RBAC) для предоставления разрешений субъекту безопасности, который может быть пользователем, группой или субъектом-службой приложения. Дополнительные сведения о ролях и назначениях ролей см. в разделе [Основные сведения о различных ролях](../role-based-access-control/overview.md).

## <a name="overview"></a>Обзор
Когда участник безопасности (пользователь, группа или приложение) пытается получить доступ к сущности служебной шины, запрос должен быть полномочным. В Azure AD доступ к ресурсу состоит из двух этапов. 

 1. Во-первых, удостоверение субъекта безопасности проходит проверку подлинности и возвращается маркер OAuth 2,0. Имя ресурса для запроса маркера — `https://servicebus.azure.net`.
 1. Затем маркер передается как часть запроса службе служебной шины для авторизации доступа к указанному ресурсу.

Этап проверки подлинности требует, чтобы запрос приложения содержал маркер доступа OAuth 2,0 во время выполнения. Если приложение выполняется в сущности Azure, такой как виртуальная машина Azure, масштабируемый набор виртуальных машин или приложение-функция Azure, оно может использовать управляемое удостоверение для доступа к ресурсам. Сведения о проверке подлинности запросов, выполняемых управляемым удостоверением, в службе служебной шины см. в статье [Аутентификация доступа к ресурсам служебной шины Azure с помощью Azure Active Directory и управляемых удостоверений для ресурсов Azure](service-bus-managed-service-identity.md). 

Для шага авторизации необходимо, чтобы один или несколько ролей RBAC были назначены субъекту безопасности. Служебная шина Azure предоставляет роли RBAC, охватывающие наборы разрешений для ресурсов служебной шины. Роли, назначенные субъекту безопасности, определяют разрешения, которые будут иметь субъекта. Дополнительные сведения о назначении ролей RBAC в служебной шине Azure см. в статье [встроенные роли RBAC для служебной шины Azure](#built-in-rbac-roles-for-azure-service-bus). 

Собственные приложения и веб-приложения, которые делают запросы к служебной шине, также могут авторизоваться в Azure AD. В этой статье показано, как запросить маркер доступа и использовать его для авторизации запросов к ресурсам служебной шины. 


## <a name="assigning-rbac-roles-for-access-rights"></a>Назначение ролей RBAC для прав доступа
Azure Active Directory (Azure AD) разрешает права доступа к защищенным ресурсам с помощью [управления доступом на основе ролей (RBAC)](../role-based-access-control/overview.md). Служебная шина Azure определяет набор встроенных ролей RBAC, охватывающих общие наборы разрешений, используемых для доступа к сущностям служебной шины. Кроме того, можно определить пользовательские роли для доступа к данным.

Когда роль RBAC назначается субъекту безопасности Azure AD, Azure предоставляет доступ к этим ресурсам для этого субъекта безопасности. Доступ может быть ограничен уровнем подписки, группой ресурсов или пространством имен служебной шины. Субъект безопасности Azure AD может быть пользователем, группой, субъектом-службой приложения или [управляемым удостоверением для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Встроенные роли RBAC для служебной шины Azure
Для служебной шины Azure управление пространствами имен и всеми связанными ресурсами через портал Azure и API управления ресурсами Azure уже защищено с помощью модели *управления доступом на основе ролей* (RBAC). Azure предоставляет следующие встроенные роли RBAC для авторизации доступа к пространству имен служебной шины:

- [Владелец данных служебной шины Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Включает доступ к данным для пространства имен служебной шины и его сущностей (очередей, разделов, подписок и фильтров).
- [Отправитель данных служебной шины Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): Используйте эту роль, чтобы предоставить отправку доступа к пространству имен служебной шины и ее сущностям.
- [Приемник данных служебной шины Azure](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): Используйте эту роль, чтобы предоставить доступ к пространству имен служебной шины и ее сущностям. 

## <a name="resource-scope"></a>Область действия ресурса 
Прежде чем назначить роль RBAC субъекту безопасности, определите область доступа, которую должен иметь субъект безопасности. Рекомендации определяют, что всегда рекомендуется предоставлять только самые узкие области.

В следующем списке описаны уровни, на которых можно ограничить доступ к ресурсам служебной шины, начиная с самой короткой области:

- **Очередь**, **раздел**или **Подписка**: Назначение ролей применяется к определенной сущности служебной шины. В настоящее время портал Azure не поддерживает назначение пользователей/групп/управляемых удостоверений для ролей RBAC служебной шины на уровне подписки. 
- **Пространство имен служебной шины**: Назначение ролей охватывает всю топологию служебной шины в пространстве имен и связанную с ней группу потребителей.
- **Группа ресурсов.** Назначение ролей применяется ко всем ресурсам служебной шины в группе ресурсов.
- **Подписка**: Назначение ролей применяется ко всем ресурсам служебной шины во всех группах ресурсов в подписке.

> [!NOTE]
> Помните, что для распространения назначений ролей RBAC может потребоваться до пяти минут. 

Дополнительные сведения о том, как определяются встроенные роли, см. в разделе [Знакомство с определениями ролей](../role-based-access-control/role-definitions.md#management-and-data-operations). Сведения о создании настраиваемых ролей RBAC см. в статье [Создание настраиваемых ролей для управления доступом на основе ролей в Azure](../role-based-access-control/custom-roles.md).


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Назначение ролей RBAC с помощью портал Azure  
Дополнительные сведения об управлении доступом к ресурсам Azure с помощью RBAC и портал Azure см. в [этой статье](..//role-based-access-control/role-assignments-portal.md). 

Определив соответствующую область для назначения ролей, перейдите к этому ресурсу в портал Azure. Отобразить параметры управления доступом (IAM) для ресурса и выполнить следующие инструкции для управления назначениями ролей:

> [!NOTE]
> Описанные ниже действия назначают роль пространству имен служебной шины. Вы можете выполнить те же действия, чтобы назначить роль другим поддерживаемым областям (группе ресурсов, подписке и т. д.).

1. В [портал Azure](https://portal.azure.com/)перейдите к пространству имен служебной шины. Выберите **Управление доступом (IAM)** в меню слева, чтобы отобразить параметры контроля доступа для пространства имен. Если необходимо создать пространство имен служебной шины, следуйте инструкциям в этой статье: [Создайте пространство имен обмена сообщениями служебной шины](service-bus-create-namespace-portal.md).

    ![В меню слева выберите пункт Управление доступом.](./media/authenticate-application/select-access-control-menu.png)
1. Выберите вкладку **Назначения ролей**, чтобы просмотреть список назначений ролей. Нажмите кнопку **Добавить** на панели инструментов, а затем выберите **добавить назначение ролей**. 

    ![Кнопка "Добавить" на панели инструментов](./media/authenticate-application/role-assignments-add-button.png)
1. На странице **Добавление назначения ролей** выполните следующие действия.
    1. Выберите **роль служебной шины** , которую требуется назначить. 
    1. Найдите **участника безопасности** (пользователя, группы, субъекта-службы), которому нужно назначить роль.
    1. Нажмите кнопку **сохранить** , чтобы сохранить назначение ролей. 

        ![Назначение роли пользователю](./media/authenticate-application/assign-role-to-user.png)
    4. Удостоверение, которому назначена роль RBAC, будет отображаться в списке под этой ролью. Например, на следующем рисунке показано, что Azure-Users находится в роли владельца данных служебной шины Azure. 
        
        ![Пользователь в списке](./media/authenticate-application/user-in-list.png)

Вы можете выполнить аналогичные действия, чтобы назначить роль, ограниченную группой ресурсов, или подпиской. Определив роль и ее область, вы можете проверить это поведение с помощью [примеров на сайте GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Аутентификация из приложения
Ключевым преимуществом использования Azure AD с служебной шиной является то, что ваши учетные данные больше не нужно хранить в коде. Вместо этого можно запросить маркер доступа OAuth 2,0 на платформе Microsoft Identity. Azure AD выполняет проверку подлинности субъекта безопасности (пользователя, группы или субъекта-службы), на котором выполняется приложение. Если проверка подлинности прошла удачно, Azure AD возвращает маркер доступа приложению, а приложение может использовать маркер доступа для авторизации запросов к служебной шине Azure.

В следующих разделах показано, как настроить собственное приложение или веб-приложение для проверки подлинности с помощью Microsoft Identity Platform 2,0. Дополнительные сведения о платформе Microsoft Identity Platform 2,0 см. в статье [Обзор платформы Microsoft Identity Platform (v 2.0)](../active-directory/develop/v2-overview.md).

Общие сведения о процессе предоставления кода OAuth 2.0 представлены в разделе [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Регистрация приложения в клиенте Azure AD
Первым шагом в использовании Azure AD для авторизации сущностей служебной шины является регистрация клиентского приложения в клиенте Azure AD из [портал Azure](https://portal.azure.com/). При регистрации клиентского приложения вы предоставляете сведения о приложении в AD. Затем Azure AD предоставляет идентификатор клиента (также называемый ИДЕНТИФИКАТОРом приложения), с помощью которого можно связать приложение со средой выполнения Azure AD. Дополнительные сведения об идентификаторе клиента см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

На следующих изображениях показаны шаги для регистрации веб-приложения.

![Регистрация приложения](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> При регистрации приложения в качестве собственного приложения можно указать любой допустимый универсальный код ресурса (URI) для URI перенаправления. Для собственных приложений это значение не обязательно должно быть действительным URL-адресом. Для веб-приложений URI перенаправления должен быть допустимым URI, так как он указывает URL-адрес, на который предоставляются токены.

После регистрации приложения в разделе **Параметры**отобразится **идентификатор приложения (клиента)** .

![Идентификатор приложения зарегистрированного приложения](./media/authenticate-application/application-id.png)

Дополнительные сведения о регистрации приложения в Azure AD см. в разделе [Интеграция приложений с Azure Active Directory](../active-directory/develop/quickstart-v2-register-an-app.md).

> [!IMPORTANT]
> Запишите значение **TenantId** и **applicationId**. Эти значения понадобятся для запуска приложения.

### <a name="create-a-client-secret"></a>Создание секрета клиента   
Приложению требуется секрет клиента для подтверждения его личности при запросе маркера. Чтобы добавить секрет клиента, выполните следующие действия.

1. Перейдите к регистрации приложения в портал Azure, если вы еще не сделали это на странице.
1. В меню слева выберите **сертификаты & секреты** .
1. В разделе **секреты клиента**выберите **новый секрет клиента** , чтобы создать новый секрет.

    ![Новый секрет клиента — кнопка](./media/authenticate-application/new-client-secret-button.png)
1. Введите описание секрета и выберите желаемый интервал срока действия, а затем нажмите кнопку **Добавить**.

    ![Страница добавления секрета клиента](./media/authenticate-application/add-client-secret-page.png)
1. Немедленно скопируйте значение нового секрета в безопасное расположение. Значение заполнения отображается только один раз.

    ![Секрет клиента](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Разрешения для API служебной шины
Если приложение является консольным приложением, необходимо зарегистрировать собственное приложение и добавить разрешения API для **Microsoft. servicebus** в набор **необходимых разрешений** . Для собственных приложений также требуется **URL-адрес перенаправления** в Azure AD, который служит идентификатором. URI не обязательно должен быть сетевым назначением. В этом примере используйте `https://servicebus.microsoft.com`, так как в образце кода уже используется этот URI.

### <a name="client-libraries-for-token-acquisition"></a>Клиентские библиотеки для получения маркера  
После регистрации приложения и предоставления ему разрешений на отправку и получение данных в служебной шине Azure можно добавить код в приложение для проверки подлинности субъекта безопасности и получения маркера OAuth 2,0. Для проверки подлинности и получения маркера можно использовать одну из [библиотек проверки подлинности платформы идентификации Microsoft Identity](../active-directory/develop/reference-v2-libraries.md) или другую библиотеку с открытым исходным кодом, которая поддерживает OpenID Connect или Connect 1,0. Приложение может использовать маркер доступа для авторизации запроса к служебной шине Azure.

Список сценариев, для которых поддерживается получение маркеров, см. в разделе [сценарии](https://aka.ms/msal-net-scenarios) [библиотеки проверки подлинности Майкрософт (MSAL) для](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) репозитория GitHub .NET.

## <a name="sample-on-github"></a>Пример на GitHub
См. Следующий пример на GitHub: [Ролевое управление доступом для служебной шины](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Используйте параметр " **секретный код клиента** ", а не **Интерактивный вход пользователя** . При использовании параметра секрет клиента всплывающее окно не отображается. Приложение использует идентификатор клиента и идентификатор приложения для проверки подлинности. 

### <a name="run-the-sample"></a>Запуск примера

Перед запуском примера измените файл **app. config** и в зависимости от сценария задайте следующие значения:

- `tenantId`. задайте значение **TenantId**.
- `clientId`. задайте значение **ApplicationId**.
- `clientSecret`. если необходимо войти в систему с помощью секрета клиента, создайте его в Azure AD. Кроме того, используйте веб-приложение или API вместо собственного приложения. Добавьте приложение на страницу **Управление доступом (IAM)** в созданном ранее пространстве имен.
- `serviceBusNamespaceFQDN`. Укажите полное DNS-имя созданного пространства имен Служебной шины (например, `example.servicebus.windows.net`).
- `queueName`. Укажите имя созданной очереди.
- URI перенаправления, указанный в приложении на предыдущих шагах.

При запуске консольного приложения вам будет предложено выбрать сценарий. Выберите **имя входа интерактивного пользователя** , введя его номер и нажав клавишу ВВОД. В приложении отображается окно входа в систему, оно запрашивает ваше согласие на доступ к служебной шине, а затем использует службу для запуска сценария отправки или получения с помощью удостоверения для входа.


## <a name="next-steps"></a>Следующие шаги
- Дополнительные сведения о RBAC см. в статье [что такое управление доступом на основе ролей (RBAC)](../role-based-access-control/overview.md)?
- Чтобы узнать, как назначать роли RBAC и управлять назначениями ролей с помощью Azure PowerShell, Azure CLI или REST API, см. сведения в статьях ниже:
    - [Управление доступом на основе ролей с помощью Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Управление доступом на основе ролей с помощью интерфейса командной строки Azure](../role-based-access-control/role-assignments-cli.md)
    - [Управление доступом на основе ролей с помощью REST API](../role-based-access-control/role-assignments-rest.md)
    - [Управление доступом на основе ролей (RBAC) с помощью шаблонов Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Дополнительную информацию об обмене сообщениями через служебную шину см. в следующих разделах.

- [Примеры RBAC служебной шины](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
- [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
- [Как использовать разделы и подписки служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)
