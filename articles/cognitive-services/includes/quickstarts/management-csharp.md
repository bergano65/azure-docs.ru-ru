---
title: Краткое руководство. Клиентская библиотека управления Azure для .NET
description: В этом кратком руководстве описано, как приступить к работе с клиентской библиотекой управления Azure для .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 93ea0ffc94b78e014b30ab1d45d589eba50fe524
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607629"
---
[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [Примеры](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="prerequisites"></a>Предварительные требования

* Действующая подписка Azure ([создайте бесплатную учетную запись](https://azure.microsoft.com/free/)).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="create-an-azure-service-principal"></a>Создание субъекта-службы Azure

Чтобы приложение взаимодействовало с учетной записью Azure, необходима субъект-служба Azure для управления разрешениями. Выполните инструкции по [созданию субъекта-службы Azure](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

При создании субъекта-службы вы увидите, что у него есть значение секрета, идентификатор и идентификатор приложения. Сохраните идентификатор приложения и секрет во временное расположение для последующих шагов.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Перед созданием ресурса Cognitive Services в вашей учетной записи должна быть группа ресурсов Azure, в которой будет содержаться ресурс. Если у вас еще нет группы ресурсов, создайте ее на [портале Azure](https://ms.portal.azure.com/), прежде чем продолжить работу.

## <a name="create-a-new-c-application"></a>Создание нового приложения C#

Создайте приложение .NET Core. В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `azure-management-quickstart`. Эта команда создает простой проект "Hello World" на языке C# с одним файлом исходного кода: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Измените каталог на созданную папку приложения. Чтобы создать приложение, выполните следующую команду:

```console
dotnet build
```

Выходные данные сборки не должны содержать предупреждений или ошибок. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В каталоге приложения установите клиентскую библиотеку управления Azure для .NET, используя следующую команду:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде загружаемого пакета NuGet.

### <a name="import-libraries"></a>Импорт библиотек

Откройте файл *program.cs* и добавьте следующие операторы `using` в начало файла:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Аутентификация клиента

Добавьте следующие поля в корень файла *program.cs* и заполните их значения с помощью созданного субъекта-службы и информации учетной записи Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Затем в методе **Main** используйте эти значения для создания объекта **CognitiveServicesManagementClient**. Этот объект необходим для всех операций управления Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Вызов методов управления

Добавьте следующий код в метод **Main**, чтобы получить список доступных ресурсов, создать пример ресурса, вывести список собственных ресурсов, а затем удалить пример ресурса. Эти методы будут определены позже.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>Создание ресурса Cognitive Services

### <a name="choose-a-service-and-pricing-tier"></a>Выбор службы и ценовой категории

При создании ресурса необходимо знать, какой вид службы вы хотите использовать, а также выбрать [ценовую категорию](https://azure.microsoft.com/pricing/details/cognitive-services/) (или номер SKU). Вы будете использовать эту и другую информацию в качестве параметров при создании ресурса. Чтобы получить список доступных видов служб Cognitive Service, вызовите в скрипте следующий метод:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

Список номеров SKU и сведения о ценах см. ниже. 

#### <a name="multi-service"></a>Несколько служб

| Служба                    | Вид                      |
|----------------------------|---------------------------|
| Несколько служб. См. дополнительные сведения на [странице с расценками](https://azure.microsoft.com/pricing/details/cognitive-services/).            | `CognitiveServices`     |


#### <a name="vision"></a>Компьютерное зрение

| Служба                    | Вид                      |
|----------------------------|---------------------------|
| Компьютерное зрение            | `ComputerVision`          |
| Ресурс прогнозирования службы "Пользовательское визуальное распознавание" | `CustomVision.Prediction` |
| Ресурс обучения службы "Пользовательское визуальное распознавание"   | `CustomVision.Training`   |
| Распознавание лиц                       | `Face`                    |
| Распознаватель документов            | `FormRecognizer`          |
| Распознаватель рукописного текста             | `InkRecognizer`           |

#### <a name="search"></a>Поиск

| Служба            | Вид                  |
|--------------------|-----------------------|
| Автозаполнение Bing   | `Bing.Autosuggest.v7` |
| Пользовательский поиск Bing | `Bing.CustomSearch`   |
| API Поиска сущностей Bing | `Bing.EntitySearch`   |
| Поиск Bing        | `Bing.Search.v7`      |
| API Проверки орфографии Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Речь

| Служба            | Вид                 |
|--------------------|----------------------|
| Службы "Речь"    | `SpeechServices`     |
| Распознавание речи | `SpeakerRecognition` |

#### <a name="language"></a>Язык

| Служба            | Вид                |
|--------------------|---------------------|
| Анализ форм | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Анализ текста     | `TextAnalytics`     |
| Преобразование текста   | `TextTranslation`   |

#### <a name="decision"></a>Решение

| Служба           | Вид               |
|-------------------|--------------------|
| Детектор аномалий  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Персонализатор      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Ценовые категории и выставление счетов

Ценовые категории (и сумма в выставленных счетах) основаны на количестве отправленных вами транзакций с использованием данных аутентификации. Каждая ценовая категория определяет:
* максимальное количество разрешенных транзакций, обрабатываемых в секунду (TPS);
* функции службы, включенные в ценовой категории;
* стоимость предопределенного количества транзакций. При превышении этого количества будет взиматься дополнительная плата, как указано в [сведениях о ценах](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) для вашей службы.

> [!NOTE]
> Многие из служб Cognitive Services имеют бесплатный уровень, который можно использовать для пробного использования службы. Чтобы использовать бесплатный уровень, выберите `F0` в качестве номера SKU для вашего ресурса.

## <a name="create-a-cognitive-services-resource"></a>Создание ресурса Cognitive Services

Чтобы создать ресурс Cognitive Services и подписаться на него, используйте метод **Create**. Этот метод добавляет новый оплачиваемый ресурс в передаваемую группу ресурсов. При создании ресурса необходимо знать, какой вид службы вы хотите использовать, а также выбрать ценовую категорию (или номер SKU) и расположение Azure. Следующий метод использует все эти аргументы и создает ресурс.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

## <a name="view-your-resources"></a>Просмотр ресурсов

Чтобы просмотреть все ресурсы в учетной записи Azure (для всех групп ресурсов), используйте следующий метод:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Удаление ресурса

Следующий метод удаляет указанные ресурсы из заданной группы ресурсов.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение из каталога приложения с помощью команды `dotnet run`.

```dotnet
dotnet run
```

## <a name="see-also"></a>См. также

* [Справочная документация по пакету SDK для управления Azure](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)
* [Общие сведения об Azure Cognitive Services](../../Welcome.md)
* [Проверка подлинности запросов к Azure Cognitive Services](../../authentication.md)
* [Создание ресурса с помощью портала Azure](../../cognitive-services-apis-create-account.md)