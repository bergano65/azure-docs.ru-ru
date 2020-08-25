---
title: Настройка функции Azure для обработки данных
titleSuffix: Azure Digital Twins
description: Узнайте, как создать функцию Azure, которая может получать доступ и вызываться цифровым двойников.
author: baanders
ms.author: baanders
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f371f52b0a110afeef6f3f72fb459b30842f77eb
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723713"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Подключение приложений функций Azure для обработки данных

Во время действия предварительной версии обновление цифровых двойников на основе данных осуществляется с помощью [**маршрутов событий**](concepts-route-events.md) через ресурсы вычислений, таких как [функции Azure](../azure-functions/functions-overview.md). Функция Azure может использоваться для обновления цифрового двойника в ответ на следующее:
* данные телеметрии устройства, поступающие из центра Интернета вещей
* изменение свойства или другие данные, поступающие из другого цифрового двойника в графе двойника

В этой статье описывается создание функции Azure для использования с Azure Digital двойников. 

Ниже приведен обзор шагов, которые он содержит.

1. Создание приложения "функции Azure" в Visual Studio
2. Запись функции Azure с помощью триггера службы " [Сетка событий](../event-grid/overview.md) "
3. Добавление кода проверки подлинности в функцию (для доступа к Azure Digital двойников)
4. Публикация приложения функции в Azure
5. Настройка доступа к [безопасности](concepts-security.md) . Чтобы функция Azure получала маркер доступа во время выполнения для доступа к службе, необходимо настроить Управляемое удостоверение службы для приложения функции.

Оставшаяся часть этой статьи посвящена этапам установки функций Azure по одной за раз.

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Создание приложения "функции Azure" в Visual Studio

В Visual Studio 2019 выберите *файл > новый проект*. Найдите шаблон " *функции Azure* ", выберите его и нажмите кнопку "Далее".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-new-project.png" alt-text="Visual Studio: диалоговое окно создания проекта":::

Укажите имя для приложения-функции и нажмите кнопку "создать".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-config.png" alt-text="Visual Studio: диалоговое окно настройки проекта":::

Выберите *триггер сетки событий* и нажмите кнопку "создать".

:::image type="content" source="media/how-to-create-azure-function/visual-studio-project-trigger.png" alt-text="Visual Studio: диалоговое окно триггера проекта функции Azure":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Запись функции Azure с помощью триггера службы "Сетка событий"

Следующий код создает краткую функцию Azure, которую можно использовать для записи событий в журнал: 

```csharp
// Default URL for triggering Event Grid function in the local environment
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;

namespace FunctionSample
{
    public static class FooFunction    {
        [FunctionName("Foo")]
        public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Это базовая функция Azure.

### <a name="run-and-debug-the-azure-function-app"></a>Запуск и отладка приложения функции Azure

Теперь можно скомпилировать и запустить функцию. Хотя функции Azure в конечном итоге предназначены для работы в облаке, вы также можете выполнять и отлаживать функции Azure локально.

Дополнительные сведения об этом см. в разделе [*Отладка триггера сетки событий в локальной*](../azure-functions/functions-debug-event-grid-trigger-local.md)среде.

### <a name="add-the-azure-digital-twins-sdk-to-your-azure-function-app"></a>Добавление пакета SDK для цифрового двойников Azure в приложение функции Azure

Приложение-функция взаимодействует с Azure Digital двойников, используя [клиентскую библиотеку Azure IOT Digital двойника для .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Чтобы использовать пакет SDK, необходимо включить в проект следующие пакеты:
* `Azure.DigitalTwins.Core` (версия `1.0.0-preview.2` )
* `Azure.Identity` (версия `1.1.1` )

Для правильной настройки конвейера пакета SDK Azure для функций Azure также потребуется:
* `Azure.Net.Http`
* `Azure.Core`

В зависимости от выбранных вами инструментов это можно сделать с помощью диспетчера пакетов Visual Studio или `dotnet` средства командной строки. 

Добавьте следующие операторы using в функцию Azure.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```

## <a name="add-authentication-code-to-the-azure-function"></a>Добавление кода проверки подлинности в функцию Azure

Затем добавьте код проверки подлинности, который позволит функции получить доступ к Azure Digital двойников.

Добавьте переменные в класс функции для следующих значений: 
* Идентификатор приложения Azure Digital двойников
* URL-адрес вашего экземпляра Digital двойников для Azure. Рекомендуется считать URL-адрес службы из переменной среды, а не жестко кодировать его в функции.
* Статическая переменная для хранения экземпляра HttpClient. HttpClient довольно дорого создавать, и мы хотим избежать этого при каждом вызове функции.

Кроме того, добавьте локальную переменную внутри функции, чтобы разместить ваш экземпляр клиента Digital двойников в проекте функции. *Не* делайте эту статическую переменную внутри класса.

Наконец, измените сигнатуру функции, чтобы она была асинхронной.

После этих изменений код функции должен выглядеть следующим образом:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Чтобы приложение-функция могло получить доступ к Azure Digital двойников, оно должно иметь управляемое системой удостоверение и иметь разрешения на доступ к вашему экземпляру Azure Digital двойников.

С помощью следующей команды создайте управляемое системой удостоверение. Запишите значение поля *principalId* в выходных данных команды.

```azurecli
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Используйте значение *principalId* в следующей команде, чтобы назначить удостоверение приложения-функции для роли *владельца Azure Digital двойников (Предварительная версия)* для вашего экземпляра Digital двойников Azure. Это предоставит приложению функции в экземпляре разрешение на выполнение действий плоскости данных.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Дополнительные сведения об управляемом удостоверении см. [*в статье Использование управляемых удостоверений для службы приложений и функций Azure*](../app-service/overview-managed-identity.md).

Наконец, можно сделать так, чтобы URL-адрес вашего экземпляра Azure Digital двойников был доступен для вашей функции, задав переменную среды. Дополнительные сведения об этом см. в разделе [*переменные среды*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables).

> [!TIP]
> URL-адрес экземпляра Azure Digital двойников создается путем добавления *https://* в начало *имени узла*для цифрового двойникова Azure. Чтобы просмотреть имя узла, а также все свойства экземпляра, можно запустить `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-URL>"
```

## <a name="publish-the-azure-function-app"></a>Публикация приложения функции Azure

Чтобы опубликовать приложение-функцию в Azure, выберите проект функции (не решение) в обозреватель решений и нажмите кнопку *опубликовать ()*.

Появится следующая вкладка:

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-1.png" alt-text="Visual Studio: диалоговое окно функции публикации, страница 1":::

Выберите или создайте план службы приложений для использования с функциями Azure. Если не уверены, начните использовать план потребления по умолчанию.

> [!IMPORTANT] 
> Публикация функции Azure приведет к дополнительным затратам на вашу подписку, независимо от Azure Digital двойников.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-publish-2.png" alt-text="Visual Studio: диалоговое окно функции публикации, страница 2":::

На следующей странице введите нужное имя для нового приложения-функции, группы ресурсов и других сведений.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Настройка безопасного доступа для приложения функции Azure

В схеме функций Azure из предыдущих примеров необходимо передать маркер носителя для проверки подлинности в Azure Digital двойников. Чтобы убедиться, что этот токен носителя передан, необходимо настроить [управляемое удостоверение службы (MSI)](../active-directory/managed-identities-azure-resources/overview.md) для приложения-функции. Это необходимо сделать только один раз для каждого приложения функции.

Чтобы настроить это, перейдите к [портал Azure](https://portal.azure.com/) и перейдите к приложению функции.

На вкладке *функции платформы* выберите *удостоверение*.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-1.png" alt-text="Портал Azure: Выбор удостоверения для функции Azure":::

На странице удостоверение задайте для переключателя *состояние* значение *вкл*. 

:::image type="content" source="media/how-to-create-azure-function/visual-studio-msi-2.png" alt-text="Портал Azure: Включение состояния удостоверения":::

Также обратите внимание на **идентификатор объекта** , показанный на этой странице, так как он будет использоваться в следующем разделе.

### <a name="assign-access-roles"></a>Назначение ролей доступа

Так как Azure Digital двойников использует управление доступом на основе ролей для управления доступом (см [*. раздел Основные понятия: безопасность для решений для цифрового двойников Azure*](concepts-security.md) ), необходимо также добавить роль для каждого приложения-функции, которое будет иметь доступ к Azure Digital двойников.

Чтобы назначить роль, требуется **идентификатор ресурса** созданного экземпляра Azure Digital двойников. Если вы не зарегистрировали его ранее при создании экземпляра, его можно получить с помощью следующей команды:

```bash
az dt show --name <your-instance-name> -g <your-resource-group-name>
```
Идентификатор ресурса будет частью выходных данных в виде длинной строки с именем "ID", которая начинается с букв "/субскриптионс/...".

Используйте идентификатор ресурса вместе с ИДЕНТИФИКАТОРом объекта функции Azure из ранее в следующей команде:

```azurecli
az role assignment create --role "Azure Digital Twins Owner (Preview)" --assignee <object-ID> --scope <resource-ID>
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье описано, как настроить функцию Azure для использования с Azure Digital двойников. Затем вы можете подписать функцию Azure на службу "Сетка событий", чтобы прослушать конечную точку. Эта конечная точка может быть следующей:
* Конечная точка сетки событий, присоединенная к Azure Digital двойников для обработки сообщений, поступающих от самого себя в Azure Digital двойников (например, сообщений об изменении свойств, сообщений телеметрии, созданных [цифровым двойников](concepts-twins-graph.md) в графе двойника, или сообщений о жизненном цикле).
* Разделы системы IoT, используемые центром Интернета вещей для отправки данных телеметрии и других событий устройства
* Конечная точка сетки событий, получающая сообщения от других служб

Далее Узнайте, как создать базовую функцию Azure для приема данных центра Интернета вещей в Azure Digital двойников:
* [*Пошаговое руководство. прием данных телеметрии из центра Интернета вещей*](how-to-ingest-iot-hub-data.md)