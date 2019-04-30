---
title: Отправки и получения событий с помощью Go - концентраторов событий Azure | Документация Майкрософт
description: В статье описано, как создать приложение Go, которое отправляет события в Центры событий Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 823ebc985c77785f8b48d12d5919dbbd1b2b1459
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821691"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-go"></a>Отправлять события или получения событий из концентраторов событий с помощью Go
Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Служба "Центры событий" может обрабатывать и сохранять события и данные, в том числе телеметрические, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в режиме реального времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

Это руководство содержит инструкции для написания приложений Go для отправки событий или получать события из концентратора событий. 

> [!NOTE]
> Вы можете скачать это краткое руководство в качестве примера с сайта [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), заменить строки `EventHubConnectionString` и `EventHubName` значениями для своего концентратора событий и выполнить этот пример. Или следуйте инструкциям из этого руководства, чтобы создать собственное решение.

## <a name="prerequisites"></a>Технические условия

Для работы с данным руководством вам потребуется:

- Локально установленный Go. При необходимости выполните [следующие инструкции](https://golang.org/doc/install).
- Активная учетная запись Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][], прежде чем начинать работу.
- **Создать пространство имен концентраторов событий и концентратора событий**. Используйте [портала Azure](https://portal.azure.com) создать пространство имен типа концентраторов событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, выполните инструкции из [этой статьи](event-hubs-create.md).

## <a name="send-events"></a>Отправка событий
В этом разделе показано, как создать приложение Go для отправки событий в концентратор событий. 

### <a name="install-go-package"></a>Установка пакета Go

Получить пакет Go для Центров событий с помощью `go get` или `dep`. Пример.

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Импорт пакетов в файл кода

Для импорта пакетов Go используйте следующий пример кода:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу, следуя инструкциям в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Сохраните предоставленные учетные данные в своей среде со следующими именами. Пакеты Azure SDK для Go и Центров событий предварительно настроенные для поиска этих имен переменных:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Теперь создайте поставщик авторизации для клиента Центров событий, использующего эти учетные данные:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Создание клиента Центров событий

В следующем коде создается клиент Центров событий:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Написание кода для отправки сообщений

В следующем фрагменте кода используйте (1) для отправки сообщений из терминала в интерактивном режиме или (2) для отправки сообщений в рамках программы:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Дополнительно

Получите идентификаторы каждой секции в концентраторе событий:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Запустите приложение и отправьте события в концентратор событий. 

Поздравляем! Теперь вы можете отправлять сообщения в концентратор событий.

## <a name="receive-events"></a>Получение событий

### <a name="create-a-storage-account-and-container"></a>Создание учетной записи хранения и контейнера

Такие состояния, как аренда разделов и контрольные точки в потоке событий, совместно используются получателями через контейнер службы хранилища Azure. Вы можете создать учетную запись хранения и контейнер с помощью пакета SDK для Go, но их также можно создать, следуя инструкциям в статье [Об учетных записях хранения Azure](../storage/common/storage-create-storage-account.md).

Примеры для создания артефактов хранилища с помощью пакета SDK для Go доступны в [репозитории примеров Go](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) и в примере, прилагаемом к этому руководству.

### <a name="go-packages"></a>Выберите пакеты

Чтобы получать сообщения, получите пакеты Go для концентраторов событий с помощью `go get` или `dep`:

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Импорт пакетов в файл кода

Для импорта пакетов Go используйте следующий пример кода:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Создание субъекта-службы

Создайте субъект-службу, следуя инструкциям в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli). Сохраните предоставленные учетные данные в своей среде со следующими именами. Пакет Azure SDK для Go и пакет для службы "Центры событий" предварительно настроены для поиска этих имен переменных.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Затем создайте поставщика авторизации для вашего клиента концентраторов событий, который использует эти учетные данные:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Получение структуры метаданных

Получите структуру с метаданными о вашей среде Azure, используя пакет Azure SDK для Go. Последующие операции используют эту структуру для поиска нужных конечных точек.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Создание вспомогательного приложения для учетных данных 

Создайте вспомогательное приложение, которое использует предыдущие учетные данные Azure Active Directory (AAD) для создания подписанного URL-адреса (SAS) для службы хранилища. Последний параметр предписывает конструктору использовать такие же переменные среды, как и ранее:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Создайте указатель проверку и leaser 

Создание **leaser**, ответственные за предоставление секции для определенного получателя и **проверьте указатель**, отвечает за запись контрольных точек для потока сообщений, так что можно начать другим получателям чтение из правильного смещения.

В настоящее время доступно одно **StorageLeaserCheckpointer**. Оно использует один контейнер хранилища для управления арендой и контрольными точками. Помимо имен учетной записи и контейнера, средству **StorageLeaserCheckpointer** требуются учетные данные, созданные на предыдущем шаге, и структура среды Azure для правильного доступа к контейнеру.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Создание узла обработчика событий

Теперь у вас есть компоненты для создания узла обработчика событий следующим образом. Же **StorageLeaserCheckpointer** используется в качестве leaser и указатель на флажок, как описано ранее:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Создание обработчика 

Теперь создайте обработчик и зарегистрируйте его в узле обработчика событий. Когда узел запущен, он применяет его и любые другие указанные обработчики ко входящим сообщениям:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Написание кода для получения сообщений

Когда все будет настроено, можно запустить узел обработчика событий с `Start(context)`, чтобы он постоянно работал, или с `StartNonBlocking(context)` для работы только до тех пор, пока доступны сообщения.

В этом руководстве запуск и выполнение происходит следующим образом. (Ознакомьтесь с примером использования `StartNonBlocking` на GitHub).

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь со следующими статьями:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Возможностями и терминологией в концентраторах событий Azure](event-hubs-features.md)
- [Часто задаваемые вопросы о Центрах событий](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[создайте бесплатную учетную запись Azure]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
