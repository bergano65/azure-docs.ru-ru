---
title: Создание Центра Интернета вещей Azure с помощью REST API поставщика ресурсов | Документация Майкрософт
description: Узнайте, как использовать REST API поставщика C# ресурсов для создания центра Интернета вещей и управления им программным способом.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 7d5e38e2ecfa2406ff0f58f73d828aa45d84c512
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890484"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Создание Центра Интернета вещей с помощью REST API поставщика ресурсов (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Вы можете использовать [REST API поставщика ресурсов центра Интернета вещей](https://docs.microsoft.com/rest/api/iothub/iothubresource) , чтобы создавать центры Интернета вещей Azure и управлять ими программным способом. В этом учебнике показано, как использовать REST API поставщика ресурсов Центра Интернета вещей для создания Центра Интернета вещей из программы на C#.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Для работы с этим руководством требуется:

* приведенному.

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

* [Azure PowerShell 1,0](https://docs.microsoft.com/powershell/azure/install-Az-ps) или более поздней версии.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Подготовка проекта Visual Studio

1. В Visual Studio создайте проект классического приложения Windows на языке Visual C# с помощью шаблона проекта **консольного приложения (.NET Framework)** . Дайте проекту имя **CreateIoTHubREST**.

2. В обозревателе решений щелкните правой кнопкой мыши свой проект и выберите **Управление пакетами NuGet**.

3. В диспетчере пакетов NuGet выберите **Включить предварительные выпуски** и на странице **Обзор** найдите **Microsoft.Azure.Management.ResourceManager**. Выберите пакет, щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензий.

4. В диспетчере пакетов NuGet найдите **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Щелкните **Установить**, на странице **Просмотр изменений** нажмите кнопку **ОК** и выберите **Я принимаю**, чтобы принять условия лицензии.

5. Откройте файл Program.cs и замените существующие инструкции **using** следующим кодом:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. В Program.cs добавьте следующие статические переменные, заменив значения заполнителей. Ранее в этом учебнике вы записали **ApplicationId**, **SubscriptionId**, **TenantId** и **Password**. **Resource group name** — это имя группы ресурсов, используемой при создании Центра Интернета вещей. Вы можете использовать существующую группу ресурсов или новую. **IoT Hub name** — это имя создаваемого Центра Интернета вещей, например **MyIoTHub**. Имя Центра Интернета вещей должно быть глобально уникальным. **Deployment name** — это имя развертывания, например **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>Использование REST API поставщика ресурсов для создания Центра Интернета вещей

Используйте [REST API поставщика ресурсов центра Интернета вещей](https://docs.microsoft.com/rest/api/iothub/iothubresource) , чтобы создать центр Интернета вещей в группе ресурсов. REST API поставщика ресурсов также можно использовать для изменения имеющегося Центра Интернета вещей.

1. Добавьте в класс Program.cs следующий метод:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Добавьте в метод **CreateIoTHub** следующий код. Этот код создает объект **HttpClient** с маркером аутентификации в заголовках:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Добавьте в метод **CreateIoTHub** следующий код. Этот код описывает создаваемый Центр Интернета вещей и создает представление JSON. Текущий список расположений, которые поддерживают центр Интернета вещей, см. в статье [состояние Azure](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Добавьте в метод **CreateIoTHub** следующий код. Этот код отправляет запрос REST в Azure. Затем код проверяет ответ и получает URL-адрес, который можно использовать для контроля состояния задачи развертывания:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Добавьте в конец метода **CreateIoTHub** следующий код. Этот код ожидает завершения развертывания с помощью адреса **asyncStatusUri**, полученного на предыдущем шаге:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Добавьте в конец метода **CreateIoTHub** следующий код. Этот код получает созданные ключи Центра Интернета вещей и выводит их на консоль:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>Завершение и запуск приложения

Теперь можно завершить приложение, вызвав метод **CreateIoTHub** , а затем собрать и запустить приложение.

1. Добавьте в конец метода **Main** следующий код:

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Щелкните **Построить** и **Построить решение**. Исправьте все ошибки.

3. Щелкните **Отладка** и **Начать отладку** для запуска приложения. Для запуска развертывания может потребоваться несколько минут.

4. Чтобы убедиться, что приложение добавило новый центр Интернета вещей, посетите [портал Azure](https://portal.azure.com/) и просмотрите список ресурсов. Кроме того, можно использовать командлет PowerShell **Get-азресаурце** .

> [!NOTE]
> В этом примере приложения добавляется стандартный Центр Интернета вещей S1, который подлежит оплате. По завершении можно удалить центр Интернета вещей с помощью [портал Azure](https://portal.azure.com/) или с помощью командлета PowerShell **Remove-азресаурце** .

## <a name="next-steps"></a>Дополнительная информация

После развертывания Центра Интернета вещей с использованием REST API вам могут понадобиться дополнительные сведения:

* Узнайте о возможностях [поставщика ресурсов центра Интернета вещей REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource).

* Дополнительные сведения о возможностях Azure Resource Manager см. в статье [Azure Resource Manager обзор](../azure-resource-manager/resource-group-overview.md) .

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md)

* [Пакеты SDK для Azure IoT](iot-hub-devguide-sdks.md)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)