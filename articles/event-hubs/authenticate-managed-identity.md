---
title: Проверка управляемого удостоверения с помощью Active Directory Azure
description: В этой статье содержится информация о проверке подлинности управляемого итогового каталога Azure Active Для доступа к ресурсам Azure Event Hubs
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: dfc60fbc03021e72dccc0f60a7ac34d204ef6df9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025192"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Проверка управляемого итогового данных с помощью Active Directory Azure для доступа к ресурсам концентратов событий
Концентраторы событий Azure поддерживают активную проверку каталога Azure (Azure AD) с [управляемыми идентификаторами для ресурсов Azure.](../active-directory/managed-identities-azure-resources/overview.md) Управляемые идентификаторы для ресурсов Azure могут авторизовать доступ к ресурсам концентраторов событий, используя учетные данные Azure AD из приложений, работающих в виртуальных машинах Azure, функциональных приложениях, наборах виртуальной шкалы машин и других службах. Используя управляемые идентификаторы для ресурсов Azure вместе с аутентификацией Azure AD, можно избежать хранения учетных данных с помощью приложений, запускаемых в облаке.

В этой статье показано, как авторизовать доступ к концентратору событий с помощью управляемого итогового изображения из VM Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Включение управляемых удостоверений на виртуальной машине
Прежде чем использовать управляемые идентификаторы ресурсов Azure для авторизации ресурсов концентраторов событий из вашего VM, необходимо сначала включить управляемые идентификаторы для ресурсов Azure на VM. Сведения о включении управляемых удостоверений для ресурсов Azure см. в одной из следующих статей.

- [Портал Azure](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Шаблон Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Клиентские библиотеки менеджера ресурсов Azure](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Разрешение на получение управляемой идентификации в Azure AD
Чтобы авторизовать запрос службы концентраторов событий из управляемого итога в приложении, сначала настройте настройки управления доступом на основе ролей (RBAC) для этого управляемого удостоверения. Концентраторы событий Azure определяют роли RBAC, которые охватывают разрешения на отправку и чтение из концентраторов событий. При присвоении роли RBAC управляемому иждивению управляемому иждивению предоставляется доступ к данным Event Hubs в соответствующем объеме.

Для получения дополнительной информации о [Authenticate with Azure Active Directory for access to Event Hubs resources](authorize-access-azure-active-directory.md)назначении ролей RBAC см.

## <a name="use-event-hubs-with-managed-identities"></a>Использование Центров событий с управляемыми удостоверениями для ресурсов Azure
Чтобы использовать концентраторы событий с управляемыми идентификаторами, необходимо назначить идентификатор роли и соответствующую область. Процедура в этом разделе использует простое приложение, которое работает под управляемым идентификатором и получает доступ к ресурсам Event Hubs.

Здесь мы используем пример веб-приложения, размещенного в [Azure App Service](https://azure.microsoft.com/services/app-service/). Для пошаговых инструкций по созданию веб-приложения смотрите [создание веб-приложения ASP.NET Core в Azure](../app-service/app-service-web-get-started-dotnet.md)

Как только приложение создано, выполните следующие действия: 

1. Перейдите к **настройкам** и выберите **Identity.** 
1. Выберите **статус,** который будет **на**. 
1. Нажмите кнопку **Сохранить**, чтобы сохранить параметры. 

    ![Управляемая идентификация для веб-приложения](./media/authenticate-managed-identity/identity-web-app.png)

После включения этой настройки в каталоге Active Directory (Azure AD) создается новая идентификаторная система службы и настраивается в хост службы поддержки приложений.

Теперь назначьте эту идентификацию службы роли в требуемой области в ресурсах концентраторов событий.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Назначать роли RBAC с помощью портала Azure
Чтобы назначить роль ресурсам концентров событий, перейдите на этот ресурс на портале Azure. Отобразите настройки управления доступом (IAM) для ресурса и следуйте этим инструкциям для управления назначениями ролей:

> [!NOTE]
> Следующие шаги присваивает роли идентификации службы пространствам имен event Hubs. Вы можете следовать тем же шагам, чтобы назначить роль, область действия для любого ресурса концентраторов событий. 

1. На портале Azure перейдите в пространство имен событий Концентраторов и **отобразите обзор** пространства имен. 
1. Выберите **элемент управления доступом (IAM)** в левом меню для отображения параметров управления доступом для концентратора событий.
1.  Выберите вкладку **Назначения ролей**, чтобы просмотреть список назначений ролей.
3.  Выберите **Добавить,** чтобы добавить новую роль.
4.  На странице **назначения ролей Добавить** выберите роли концентраторов событий, которые вы хотите назначить. Затем поиск, чтобы найти службу идентификации вы зарегистрировали назначить роль.
    
    ![Добавление страницы назначения ролей](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Щелкните **Сохранить**. Удостоверение, которому назначена роль RBAC, будет отображаться в списке под этой ролью. Например, на следующем изображении показано, что иная личность службы имеет владельца данных event Hubs.
    
    ![Идентификация, присвоенная роли](./media/authenticate-managed-identity/role-assigned.png)

После присвоения роли веб-приложение получит доступ к ресурсам Event Hubs в определенной области. 

### <a name="test-the-web-application"></a>Тестирование веб-приложения
1. Создайте пространство имен Центров событий и концентратор событий. 
2. Развертывание веб-приложения в Azure. Смотрите следующий раздел вкладок для ссылок на веб-приложение на GitHub. 
3. Убедитесь, что SendReceive.aspx установлен в качестве документа по умолчанию для веб-приложения. 
3. Включить **идентификацию** для веб-приложения. 
4. Назначаем эту идентификацию роли **владельца данных event Hubs** на уровне пространства имен или уровня концентратора событий. 
5. Запустите веб-приложение, введите имя и имя концентратора событий, сообщение и выберите **Отправить.** Чтобы получить событие, выберите **Receive**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (последний)](#tab/latest)
Теперь вы можете запустить веб-приложение и указать браузер на страницу aspx образца. Вы можете найти пример веб-приложения, которое отправляет и получает данные с ресурсов Event Hubs в [репо GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

Установите последний пакет от [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)и начните отправлять события в концентраторы событий с помощью **EventHubProducerClient** и получать события с помощью **EventHubConsumerClient.** 

> [!NOTE]
> В примере Java, который использует управляемый игломерек для публикации событий в концентратор событий, [см. Публикация событий с образцом идентификации Azure на GitHub.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (устаревшая версия)](#tab/old)
Теперь вы можете запустить веб-приложение и указать браузер на страницу aspx образца. Вы можете найти пример веб-приложения, которое отправляет и получает данные с ресурсов Event Hubs в [репо GitHub.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)

Установите последний пакет от [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)и начните отправлять и получать данные из концентраторов событий с помощью EventHubClient, как показано в следующем коде: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Центры событий для Kafka
Приложения Apache Kafka можно использовать для отправки сообщений и получения сообщений из концентраторов Azure Event с помощью управляемого идентификатора OAuth. Смотрите следующий пример на GitHub: [Концентраторы событий для Kafka - отправлять и получать сообщения с помощью управляемого удостоверения OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Примеры
- **Образцы образцов Azure.Messaging.EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Образцы microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    В этих образцах используется старая библиотека **Microsoft.Azure.EventHubs,** но вы можете легко обновить ее до самой последней библиотеки **Azure.Messaging.EventHubs.** Для перемещения образца из старой библиотеки в новую можно [см.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
    Этот пример был обновлен для использования последней библиотеки **Azure.Messaging.EventHubs.**
- [Концентраторы событий для Kafka - отправлять и получать сообщения с помощью управляемой идентификации OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Дальнейшие действия
- Ознакомьтесь со следующей статьей, чтобы узнать об управляемых идентификаторах для ресурсов Azure: [Что такое управляемые идентификаторы для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Смотрите следующие статьи:
    - [Authenticate requests to Azure Event Hubs из приложения с использованием active-каталога Azure](authenticate-application.md)
    - [Проверка подлинности концентрах событий Azure с использованием общих подписей доступа](authenticate-shared-access-signature.md)
    - [Разрешить доступ к ресурсам концентраторов событий с помощью активного каталога Azure](authorize-access-azure-active-directory.md)
    - [Разрешить доступ к ресурсам концентраторов событий с помощью общих подписей доступа](authorize-access-shared-access-signature.md)