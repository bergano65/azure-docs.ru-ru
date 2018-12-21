---
title: Краткое руководство. REST API Службы Azure SignalR
description: Краткое руководство по использованию REST API службы Azure SignalR.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: fdbdbe77c6541d62acef0d23d599d9687f5301b1
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251867"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Краткое руководство. Широковещательные сообщения в режиме реального времени из консольного приложения

Служба Azure SignalR предоставляет [REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) для поддержки сценариев связи сервера с клиентом, таких как широковещательная передача. Вы можете выбрать любой язык программирования, который может сделать вызов REST API. Вы можете отправлять сообщения всем подключенным клиентам, конкретному клиенту по имени или группе клиентов.

В этом кратком руководстве вы узнаете, как отправлять сообщения из приложения командной строки в подключенные клиентские приложения на языке C#.

## <a name="prerequisites"></a>Предварительные требования

Это краткое руководство предназначено для macOS, Windows или Linux.

* [Базовый пакет SDK для .NET](https://www.microsoft.com/net/download/core)
* Текстовый редактор или редактор кода по вашему выбору.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу <https://portal.azure.com/> с помощью своей учетной записи Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Пока служба развертывается, давайте перейдем к подготовке кода. Клонируйте [пример приложения из GitHub](https://github.com/aspnet/AzureSignalR-samples.git), задайте строку подключения службы SignalR и запустите приложение в локальной среде.

1. Откройте окно терминала Git. Перейдите в папку, в которую вы хотите клонировать пример проекта.

1. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Сборка и запуск примера

Этот пример представляет собой консольное приложение, показывающее использование службы Azure SignalR. Он предоставляет два режима.

- Режим сервера — используйте простые команды для вызова REST API службы Azure SignalR.
- Режим клиента — подключитесь к службе Azure SignalR и получайте сообщения с сервера.

Также вы можете узнать, как создать маркер доступа для проверки подлинности с помощью службы Azure SignalR.

### <a name="build-the-executable-file"></a>Сборка исполняемого файла

В качестве примера мы используем macOS osx.10.13-x64. Можно найти [рекомендации](https://docs.microsoft.com/dotnet/core/rid-catalog) по сборке на других платформах.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Запуск клиента

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Запуск сервера

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Запуск примера без публикации

Можно также выполнить следующую команду, чтобы запустить сервер или клиент

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Использование функции user-secrets для указания строки подключения

Вы можете выполнить `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` в корневом каталоге примера. После этого вам больше не нужен параметр `-c "<ConnectionString>"`.

## <a name="usage"></a>Использование

После запуска сервера используйте такую команду для отправки сообщения.

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Можно запустить несколько клиентов с разными именами.

## <a name="usage"> </a> Интеграция со сторонними службами

Служба Azure SignalR позволяет интегрировать сторонние службы с системой.

### <a name="definition-of-technical-specifications"></a>Определение технических спецификаций

В следующей таблице показаны все версии поддерживаемых REST API. Также можно найти файл с определением для каждой версии.

Version (версия) | Состояние API | Порт | Стандартный
--- | --- | --- | ---
`1.0-preview` | Доступна | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Доступна | Стандартная | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

В следующе таблице показан список доступных API для каждой версии.

API | `1.0-preview` | `1.0`
--- | --- | ---
[Отправка всем](#broadcast) | :heavy_check_mark: | :heavy_check_mark:
[Отправка группе](#broadcast-group) | :heavy_check_mark: | :heavy_check_mark:
Широковещательная передача для некоторых групп | :heavy_check_mark: (не рекомендуется) | `N / A`
[Отправка определенным пользователям](#send-user) | :heavy_check_mark: | :heavy_check_mark:
Отправка некоторым пользователям | :heavy_check_mark: (не рекомендуется) | `N / A`
[Добавление пользователя в группу](#add-user-to-group) | `N / A` | :heavy_check_mark:
[Удаление пользователя из группы](#remove-user-from-group) | `N / A` | :heavy_check_mark:

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Широковещательная передача для всех

Version (версия) | Метод HTTP для API | Request URL (URL-адрес запроса) | Тело запроса
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | То же, что и выше

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Широковещательная передача для группы

Version (версия) | Метод HTTP для API | Request URL (URL-адрес запроса) | Тело запроса
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | То же, что и выше

<a name="send-user"> </a>
### <a name="sending-to-specific-users"></a>Отправка для определенных пользователей

Version (версия) | Метод HTTP для API | Request URL (URL-адрес запроса) | Тело запроса
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | То же, что и выше

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Добавление пользователя в группу

Version (версия) | Метод HTTP для API | Request URL (URL-адрес запроса)
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Удаление пользователя из группы

Version (версия) | Метод HTTP для API | Request URL (URL-адрес запроса)
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
