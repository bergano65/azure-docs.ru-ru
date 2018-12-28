---
title: Отправка событий в Центры событий Azure с помощью Go | Документация Майкрософт
description: В статье описано, как создать приложение Go, которое отправляет события в Центры событий Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 44cbea8cc40f83ed5fdd2863da0cb0a01e8dc2b8
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091066"
---
# <a name="send-events-to-event-hubs-using-go"></a>Отправка событий в Центры событий с помощью Go

Центры событий Azure — это платформа потоковой передачи больших данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. Центры событий могут обрабатывать и сохранять события, данные и телеметрию, созданные распределенным программным обеспечением и устройствами. Данные, отправляемые в концентратор событий, можно преобразовывать и сохранять с помощью любого поставщика аналитики в реальном времени, а также с помощью адаптеров пакетной обработки или хранения. Подробный обзор Центров событий см. в статьях [Что такое Центры событий Azure?](event-hubs-about.md) и [Обзор функций Центров событий](event-hubs-features.md).

В этом руководстве представлены инструкции по отправке событий в концентратор событий из приложения, написанного на Go. 

> [!NOTE]
> Вы можете скачать это краткое руководство в качестве примера с сайта [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs), заменить строки `EventHubConnectionString` и `EventHubName` значениями для своего концентратора событий и выполнить этот пример. Или следуйте инструкциям из этого руководства, чтобы создать собственное решение.

## <a name="prerequisites"></a>Предварительные требования

Для работы с данным руководством вам потребуется:

* Локально установленный Go. При необходимости выполните [следующие инструкции](https://golang.org/doc/install).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Создание пространства имен Центров событий и концентратора событий
Первым шагом является использование [портала Azure](https://portal.azure.com) для создания пространства имен типа Центров событий и получение учетных данных управления, необходимых приложению для взаимодействия с концентратором событий. Чтобы создать пространство имен и концентратор событий, [выполните эти инструкции](event-hubs-create.md).

Теперь приступите к следующим действиям в этом руководстве:

## <a name="install-go-package"></a>Установка пакета Go

Получить пакет Go для Центров событий с помощью `go get` или `dep`. Например: 

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>Импорт пакетов в файл кода

Для импорта пакетов Go используйте следующий пример кода:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>Создание субъекта-службы

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

## <a name="create-event-hubs-client"></a>Создание клиента Центров событий

В следующем коде создается клиент Центров событий:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>Отправка сообщений

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
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>Дополнительно

Получите идентификаторы каждой секции в концентраторе событий:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

Запустите приложение и отправьте события в концентратор событий. 

Поздравляем! Теперь вы можете отправлять сообщения в концентратор событий.

## <a name="next-steps"></a>Дополнительная информация
В рамках работы с этим кратким руководством вы отправляли сообщения в концентратор событий с помощью Go. Чтобы узнать, как получать события из концентратора событий с помощью Go, см. статью [Получение событий из концентраторов событий с помощью Go](event-hubs-go-get-started-receive-eph.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
