---
title: включить файл
description: включить файл
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: c044e9dc7b8ee622b157a93726e7700e62ba2dfa
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779164"
---
Начало работы со Службами коммуникации Azure с помощью клиентской библиотеки SMS Служб коммуникации Azure для C# для отправки SMS-сообщений.

Выполнение этого краткого руководства предполагает небольшую дополнительную плату в несколько центов США в учетной записи Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно. 
- Последняя версия [клиентской библиотеки NET Core](https://dotnet.microsoft.com/download/dotnet-core) для вашей операционной системы.
- Активный ресурс Служб коммуникации и строка подключения. [Создайте ресурс Служб коммуникации.](../../create-communication-resource.md)
- Номер телефона с поддержкой SMS-сообщений. [Получите номер телефона.](../get-phone-number.md)

### <a name="prerequisite-check"></a>Проверка предварительных условий

- В окне терминала или команды выполните команду `dotnet`, чтобы проверить, установлена ли клиентская библиотека .NET.
- Чтобы просмотреть номера телефонов, связанные с ресурсом Служб коммуникации, войдите на [портал Azure](https://portal.azure.com/), перейдите к ресурсу Служб коммуникации и откройте вкладку с **номерами телефонов** в области навигации слева.

## <a name="setting-up"></a>Настройка

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `SmsQuickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Измените каталог на только что созданную папку приложения и выполните команду `dotnet build`, чтобы скомпилировать приложение.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Установка пакета

Оставаясь в каталоге приложения, установите пакет клиентской библиотеки Служб коммуникации SMS для .NET с помощью команды `dotnet add package`.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.2
```

Добавьте директиву `using` в начало **Program.cs**, чтобы включить пространство имен `Azure.Communication`.

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы реализуют некоторые основные функции клиентской библиотеки Служб коммуникации SMS для C#.

| Имя                                       | Описание                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Этот класс требуется для реализации всех функций обмена текстовыми сообщениями. Его экземпляр можно создать на основе сведений о подписке и использовать для отправки SMS.                           |
| SendSmsOptions | Этот класс предоставляет параметры для настройки отчетов о доставке. Если enable_delivery_report имеет значение True, при успешной доставке будет создано событие. |

## <a name="authenticate-the-client"></a>Аутентификация клиента

 Откройте **Program.cs** в текстовом редакторе и замените текст метода `Main` кодом, чтобы инициализировать `SmsClient` с помощью строки подключения. Приведенный ниже код извлекает строку подключения для ресурса из переменной среды с именем `COMMUNICATION_SERVICES_CONNECTION_STRING`. См. сведения о том, как [управлять строкой подключения ресурса](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Отправка SMS-сообщения

Отправьте SMS-сообщение, вызвав метод Send. Добавьте следующий код в конец метода `Main` в **Program.cs**:

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

`<leased-phone-number>` необходимо заменить номером телефона с поддержкой SMS, связанным с ресурсом Служб коммуникации, а `<to-phone-number>` — номером телефона, на который вы хотите отправить сообщение.

Параметр `EnableDeliveryReport` является необязательным. Его можно использовать для настройки отчетов о доставке. Это полезно, если вы хотите, чтобы при доставке SMS-сообщений создавались события. Сведения о настройке отчетов о доставке SMS-сообщений см. в кратком руководстве [Обработка событий SMS-сообщений](../handle-sms-events.md).

## <a name="run-the-code"></a>Выполнение кода

Запустите приложение из каталога приложения с помощью команды `dotnet run`.

```console
dotnet run
```
