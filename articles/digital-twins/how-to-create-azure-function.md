---
title: Настройка функции Azure для обработки данных
titleSuffix: Azure Digital Twins
description: Узнайте, как создать функцию Azure, которая может получать доступ и вызываться цифровым двойников.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0f5a921ad9eba82f27b3a8945643737b6d76030
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614076"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Подключение приложений функций Azure для обработки данных

Обновление цифровых двойников на основе данных осуществляется с помощью [**маршрутов событий**](concepts-route-events.md) через ресурсы вычислений, таких как [функции Azure](../azure-functions/functions-overview.md). Функцию Azure можно использовать для обновления цифрового двойника в ответ на следующее:
* данные телеметрии устройства, поступающие из центра Интернета вещей
* изменение свойства или другие данные, поступающие из другого цифрового двойника в графе двойника

В этой статье описывается создание функции Azure для использования с Azure Digital двойников. 

Ниже приведен обзор шагов, которые он содержит.

1. Создание приложения "функции Azure" в Visual Studio
2. Запись функции Azure с помощью триггера службы " [Сетка событий](../event-grid/overview.md) "
3. Добавление кода проверки подлинности в функцию (для доступа к Azure Digital двойников)
4. Публикация приложения функции в Azure
5. Настройка [безопасного](concepts-security.md) доступа для приложения функции Azure

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Создание приложения "функции Azure" в Visual Studio

В Visual Studio 2019 выберите _файл > создать > проект_ и найдите шаблон _функции Azure_ , нажмите кнопку _Далее_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: диалоговое окно создания проекта":::

Укажите имя для приложения-функции и нажмите кнопку _создать_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: Настройка нового проекта":::

Выберите тип *триггера сетки событий* приложения функции и нажмите кнопку _создать_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: диалоговое окно триггера проекта функции Azure":::

После создания приложения-функции Visual Studio будет иметь автоматически заполняемый пример кода в файле **Function.CS** в папке проекта. Эта короткая функция Azure используется для регистрации событий.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: окно проекта с образцом кода":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Запись функции Azure с помощью триггера службы "Сетка событий"

Вы можете написать функцию Azure, добавив пакет SDK в приложение функции. Приложение-функция взаимодействует с Azure Digital двойников, используя [клиентскую библиотеку Azure IOT Digital двойника для .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 

Чтобы использовать пакет SDK, необходимо включить в проект следующие пакеты. Можно либо установить пакеты с помощью диспетчера пакетов NuGet Visual Studio, либо добавить пакеты с помощью `dotnet` программы командной строки. Выберите один из следующих методов. 

**Вариант 1. Добавление пакетов с помощью диспетчера пакетов Visual Studio:**
    
Это можно сделать, щелкнув проект правой кнопкой мыши и выбрав пункт _Управление пакетами NuGet_ в списке. Затем в открывшемся окне выберите вкладку _Обзор_ и найдите следующие пакеты. Выберите _установить_ и _принять_ лицензионное соглашение для установки пакетов.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Для правильной настройки конвейера пакета SDK Azure для функций Azure необходимы также следующие пакеты. Повторите тот же процесс, что и выше, чтобы установить все пакеты.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**Вариант 2. Добавление пакетов с помощью `dotnet` программы командной строки:**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.2.2
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
Затем в Visual Studio обозреватель решений откройте файл _Function.CS_ , где имеется образец кода, и добавьте следующие инструкции _using_ в функцию Azure. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-azure-function"></a>Добавление кода проверки подлинности в функцию Azure

Теперь вы можете объявить переменные уровня класса и добавить код проверки подлинности, который позволит функции получить доступ к Azure Digital двойников. Вы добавите следующий код в функцию Azure в файле {имя функции}. cs.

* Чтение URL-адреса службы ADT в качестве переменной среды. Рекомендуется считать URL-адрес службы из переменной среды, а не жестко кодировать его в функции.
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* Статическая переменная для хранения экземпляра HttpClient. HttpClient довольно дорого создавать, и мы хотим избежать этого при каждом вызове функции.
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* Вы можете использовать учетные данные управляемого удостоверения в функции Azure.
```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
```
* Добавьте локальную переменную _дигиталтвинсклиент_ внутри функции, чтобы разместить свой экземпляр клиента Azure Digital двойников в проекте функции. *Не* делайте эту переменную статической внутри класса.
```csharp
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
```
* Добавьте проверку значения NULL для _адтинстанцеурл_ и заключите логику функции в блок try catch, чтобы перехватить все исключения.

После этих изменений код функции будет выглядеть следующим образом:

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## <a name="publish-the-function-app-to-azure"></a>Публикация приложения функции в Azure

Чтобы опубликовать приложение-функцию в Azure, выберите проект функции (не решение) в обозреватель решений и нажмите кнопку **опубликовать**.

> [!IMPORTANT] 
> Публикация функции Azure приведет к дополнительным затратам на вашу подписку, независимо от Azure Digital двойников.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: публикация функции Azure ":::

Выберите **Azure** в качестве целевого объекта публикации и нажмите кнопку **Далее**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: диалоговое окно публикации функции Azure, выбор Azure ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: диалоговое окно функции публикации выберите Azure приложение-функция (Windows) или (Linux) на основе компьютера":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: диалоговое окно публикации функции, создание новой функции Azure":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: диалоговое окно функции публикации, заполните поля и выберите Создать.":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: диалоговое окно функции публикации, выберите в списке приложение функции и завершите его работу.":::

На следующей странице введите нужное имя для нового приложения-функции, группы ресурсов и других сведений.
Чтобы приложение-функция могло получить доступ к Azure Digital двойников, оно должно иметь управляемое системой удостоверение и иметь разрешения на доступ к вашему экземпляру Azure Digital двойников.

Затем можно настроить безопасный доступ для функции с помощью интерфейса командной строки или портал Azure. Выберите один из следующих методов.

## <a name="set-up-security-access-for-the-azure-function-app"></a>Настройка безопасного доступа для приложения функции Azure
Вы можете настроить доступ к безопасности для приложения функции Azure, используя один из следующих вариантов:

### <a name="option-1-set-up-security-access-for-the-azure-function-app-using-cli"></a>Вариант 1. Настройка безопасного доступа для приложения функции Azure с помощью интерфейса командной строки

В схеме функций Azure из предыдущих примеров требуется передать в него токен носителя, чтобы иметь возможность проходить проверку подлинности в Azure Digital двойников. Чтобы убедиться, что этот токен носителя передан, необходимо настроить [управляемое удостоверение службы (MSI)](../active-directory/managed-identities-azure-resources/overview.md) для приложения-функции. Это необходимо сделать только один раз для каждого приложения функции.

Вы можете создать управляемое системой удостоверение и назначить удостоверение приложения-функции для роли _владельца Azure Digital двойников (Предварительная версия)_ для своего экземпляра Azure Digital двойников. Это предоставит приложению функции в экземпляре разрешение на выполнение действий плоскости данных. Затем сделайте URL-адрес экземпляра Azure Digital двойников доступным для вашей функции, задав переменную среды.

 Используйте [Azure Cloud Shell](https://shell.azure.com) для выполнения команд.

С помощью следующей команды создайте управляемое системой удостоверение. Запишите значение поля _principalId_ в выходных данных команды.

```azurecli 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Используйте значение _principalId_ в следующей команде, чтобы назначить удостоверение приложения-функции для роли _владельца Azure Digital двойников (Предварительная версия)_ для вашего экземпляра Digital двойников Azure.

```azurecli 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```
Наконец, можно сделать так, чтобы URL-адрес вашего экземпляра Azure Digital двойников был доступен для вашей функции, задав переменную среды. Дополнительные сведения о настройке переменных среды см. в разделе [*переменные среды*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> URL-адрес экземпляра Azure Digital двойников создается путем добавления *https://* в начало *имени узла*для цифрового двойникова Azure. Чтобы просмотреть имя узла, а также все свойства экземпляра, можно запустить `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-azure-function-app-using-azure-portal"></a>Вариант 2. Настройка безопасного доступа для приложения функции Azure с помощью портал Azure

Управляемое удостоверение, назначенное системой, позволяет ресурсам Azure проходить проверку подлинности в облачных службах (например, Azure Key Vault) без сохранения учетных данных в коде. После включения все необходимые разрешения можно предоставить через Управление доступом на основе ролей в Azure. Жизненный цикл этого типа управляемого удостоверения связан с жизненным циклом этого ресурса. Кроме того, каждый ресурс (например, виртуальная машина) может иметь только одно назначенное системой управляемое удостоверение.

В [портал Azure](https://portal.azure.com/)найдите _приложение функции_ в строке поиска с именем приложения функции, созданным ранее. Выберите *приложение-функция* из списка. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Портал Azure: Поиск приложения функции":::

В окне приложение функции выберите _удостоверение_ на панели навигации слева, чтобы включить управляемое удостоверение.
На вкладке _назначенная система_ переключите _состояние_ на вкл. и _Сохраните_ его. Вы увидите всплывающее окно для _включения управляемого удостоверения, назначенного системой_.
Нажмите кнопку _Да_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Портал Azure: Включить управляемое системой удостоверение":::

Вы можете проверить в уведомлениях о том, что функция успешно зарегистрирована в Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Портал Azure: уведомления":::

Также обратите внимание на **идентификатор объекта** , показанный на странице _удостоверений_ , как он будет использоваться в следующем разделе.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Скопируйте идентификатор объекта для использования в будущем":::

### <a name="assign-access-roles-using-azure-portal"></a>Назначение ролей доступа с помощью портал Azure

Нажмите кнопку _назначения ролей Azure_ , чтобы открыть страницу назначения ролей Azure. Затем выберите _+ добавить назначение ролей (Предварительная версия)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Портал Azure: Добавление назначения ролей":::

В открывшемся окне _Добавление назначения ролей (Предварительная версия)_ выберите

* _Область_ — группа ресурсов.
* _Подписка_: выберите подписку Azure.
* _Группа ресурсов_. Выберите группу ресурсов из раскрывающегося списка.
* _Роль_: выберите _Azure Digital двойников Owner (Предварительная версия)_ в раскрывающемся списке.

Сохраните сведения, нажав кнопку _сохранить_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Портал Azure: Добавление назначения ролей (Предварительная версия) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Настройка параметров приложения с помощью портал Azure

Чтобы сделать URL-адрес своего экземпляра Digital двойников доступным для вашей функции, можно задать переменную среды. Дополнительные сведения об этом см. в разделе [*переменные среды*](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables). Параметры приложения предоставляются как переменные среды для доступа к экземпляру Digital двойников. 

Для создания параметра приложения потребуются ADT_INSTANCE_URL.

ADT_INSTANCE_URL можно получить, добавив **_https://_** в имя узла экземпляра. В портал Azure можно найти имя узла Digital двойников, выполнив поиск своего экземпляра на панели поиска. Затем на левой панели навигации выберите _Обзор_ , чтобы просмотреть _имя узла_. Скопируйте это значение, чтобы создать параметр приложения.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Портал Azure: Overview — > Копировать имя узла для использования в поле _Value_.":::

Теперь можно создать параметр приложения, выполнив следующие действия:

* Поиск функции Azure с помощью имени функции в строке поиска и выбор функции из списка
* Выберите пункт _Конфигурация_ на панели навигации слева, чтобы создать новый параметр приложения.
* На вкладке _Параметры приложения_ выберите _+ создать параметр приложения_ .

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Портал Azure: Поиск существующей функции Azure":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Портал Azure: Настройка параметров приложения":::

В открывшемся окне используйте значение, скопированное из выше, чтобы создать параметр приложения. \
_Имя_  : ADT_SERVICE_URL \
_Значение_ : https://{ваш-Azure-Digital-двойников-hostname}

Нажмите кнопку _ОК_ , чтобы создать параметр приложения.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Портал Azure: Добавление параметров приложения.":::

Параметры приложения можно просмотреть с помощью имени приложения в поле _имя_ . Затем сохраните параметры приложения, нажав кнопку _сохранить_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Портал Azure: Просмотр созданного приложения и перезапуск приложения":::

Все изменения параметров приложения требуют перезапуска приложения. Нажмите кнопку _продолжить_ , чтобы перезапустить приложение.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Портал Azure: сохранение параметров приложения":::

Вы можете просмотреть параметры приложения, щелкнув значок _уведомления_ . Если параметр приложения не создан, можно повторно добавить параметр приложения, выполнив описанный выше процесс.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Портал Azure: уведомления об обновлении параметров приложения":::

## <a name="next-steps"></a>Дальнейшие действия

В этой статье описано, как настроить функцию Azure для использования с Azure Digital двойников. Затем вы можете подписать функцию Azure на службу "Сетка событий", чтобы прослушать конечную точку. Эта конечная точка может быть следующей:
* Конечная точка сетки событий, присоединенная к Azure Digital двойников для обработки сообщений, поступающих от самого себя в Azure Digital двойников (например, сообщений об изменении свойств, сообщений телеметрии, созданных [цифровым двойников](concepts-twins-graph.md) в графе двойника, или сообщений о жизненном цикле).
* Разделы системы IoT, используемые центром Интернета вещей для отправки данных телеметрии и других событий устройства
* Конечная точка сетки событий, получающая сообщения от других служб

Далее Узнайте, как создать базовую функцию Azure для приема данных центра Интернета вещей в Azure Digital двойников:
* [*Пошаговое руководство. прием данных телеметрии из центра Интернета вещей*](how-to-ingest-iot-hub-data.md)