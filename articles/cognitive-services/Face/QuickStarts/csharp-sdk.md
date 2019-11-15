---
title: Краткое руководство. Клиентская библиотека Распознавания лиц для .NET | Документация Майкрософт
description: Начало работы с клиентской библиотекой Распознавания лиц для .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/20/2019
ms.author: pafarley
ms.openlocfilehash: 1848f7230ed189f139a223020f08db150295132d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647487"
---
# <a name="quickstart-face-client-library-for-net"></a>Краткое руководство. Клиентская библиотека Распознавания лиц для .NET

Начало работы с клиентской библиотекой Распознавания лиц для .NET. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба API Распознавания лиц предоставляет доступ к передовым алгоритмам обнаружения и распознавания лиц на изображениях.

Клиентская библиотека Распознавания лиц для .NET позволяет выполнять такие задачи:

* [аутентификация клиента](#authenticate-the-client);
* [Определение лиц на изображении](#detect-faces-in-an-image)
* [поиск похожих лиц](#find-similar-faces);
* [создание и обучение на основе изображения группы людей](#create-and-train-a-person-group);
* [опознание лица](#identify-a-face);
* [создание моментального снимка для переноса данных](#take-a-snapshot-for-data-migration).

[Справочная документация](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [Пакет (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [Примеры](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Настройка

### <a name="create-a-face-azure-resource"></a>Создание ресурса API Распознавания лиц

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс API Распознавания лиц с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* Получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки [задайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа и URL-адреса конечной точки с именами `FACE_SUBSCRIPTION_KEY` и `FACE_ENDPOINT` соответственно.

### <a name="create-a-new-c-application"></a>Создание нового приложения C#

Создайте консольное приложение .NET Core на C# в предпочитаемой интегрированной среде разработки или редакторе. 

В окне консоли (cmd, PowerShell или Bash) выполните команду `dotnet new`, чтобы создать консольное приложение с именем `face-quickstart`. Эта команда создает простой проект Hello World на языке C# с одним файлом исходного кода: *Program.cs*. 

```console
dotnet new console -n face-quickstart
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

В каталоге проекта откройте файл *Program.cs* в предпочитаемом редакторе или интегрированной среде разработки. Затем добавьте следующие `using` директивы:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

В методе приложения `Main` создайте переменные для конечной точки и ключа Azure вашего ресурса.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

В каталоге приложения установите клиентскую библиотеку Распознавания лиц для .NET с помощью следующей команды:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Если вы используете интегрированную среду разработки Visual Studio, клиентская библиотека доступна в виде загружаемого пакета NuGet.

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы обрабатывают некоторые основные функции пакета SDK Распознавания лиц для .NET.

|ИМЯ|ОПИСАНИЕ|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Этот класс реализует авторизацию для использования Распознавания лиц и требуется для реализации всех ее функций. Вы создаете его экземпляр с информацией о подписке и используете его для создания экземпляров других классов. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Этот класс обрабатывает основные задачи по обнаружению и распознаванию лиц. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Этот класс представляет все данные об отдельном лице, обнаруженном на изображении. Его можно использовать для получения подробных сведений о лице.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Этот класс управляет хранимыми в облаке конструкциями **FaceList**, которые включают систематизированную коллекцию лиц. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Этот класс управляет хранимыми в облаке конструкциями **Person**, в которых хранится коллекция лиц одного человека.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Этот класс управляет хранимыми в облаке конструкциями **PersonGroup**, в которых хранится систематизированная коллекция объектов **Person**. |
|[SnapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Этот класс управляет функциональностью моментального снимка. Его можно использовать для временного сохранения всех хранимых в облаке данных о лицах и переноса этих данных в новую подписку Azure. |

## <a name="code-examples"></a>Примеры кода

В приведенных ниже фрагментах кода показано, как выполнять следующие задачи с клиентской библиотекой Распознавания лиц для .NET:

* [аутентификация клиента](#authenticate-the-client);
* [Определение лиц на изображении](#detect-faces-in-an-image)
* [поиск похожих лиц](#find-similar-faces);
* [создание и обучение на основе изображения группы людей](#create-and-train-a-person-group);
* [опознание лица](#identify-a-face);
* [создание моментального снимка для переноса данных](#take-a-snapshot-for-data-migration).


## <a name="authenticate-the-client"></a>Аутентификация клиента

> [!NOTE]
> В этом кратком руководстве предполагается, что вы уже [создали переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа API и конечной точки Распознавания лиц с именами `FACE_SUBSCRIPTION_KEY` и `FACE_ENDPOINT`.

В новом методе создайте экземпляр клиента с использованием конечной точки и ключа. Создайте объект [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) с ключом и используйте его с конечной точкой, чтобы создать объект [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Скорее всего, вы будете вызывать этот метод из метода `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Определение лиц на изображении

В корне класса определите следующую строку URL-адреса. Этот URL-адрес указывает на набор с примерами изображений.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

По желанию вы можете выбрать модель искусственного интеллекта, которая будет использоваться для извлечения данных из обнаруженных лиц. Сведения о доступных вариантах см. в разделе [Указание модели распознавания](../Face-API-How-to-Topics/specify-recognition-model.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

Последняя операция обнаружения принимает объект [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet), URL-адрес изображения и модель распознавания.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Получение обнаруженных объектов лиц

В следующем блоке кода метод `DetectFaceExtract` обнаруживает лица на трех изображениях, размещенных по указанному URL-адресу, и создает список объектов [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) в памяти программы. Список значений [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) указывает, какие компоненты следует извлечь. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Отображение обнаруженных данных о лицах

Оставшаяся часть метода `DetectFaceExtract` анализирует и выводит данные атрибутов для каждого обнаруженного лица. Каждый атрибут должен быть указан отдельно в исходном вызове API обнаружения лиц (в списке [FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)). Следующий код обрабатывает каждый атрибут, но вам, скорее всего, потребуются лишь один-два из них.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Поиск похожих лиц

Следующий код принимает одно обнаруженное лицо (источник) и выполняет поиск совпадений в коллекции других лиц (цель). При обнаружении совпадения в консоль выводится идентификатор лица, с которым найдено совпадение.

### <a name="detect-faces-for-comparison"></a>Обнаружение лиц для сравнения

Во-первых, определите второй метод для обнаружения лиц. Прежде чем сравнивать лица, их нужно обнаружить на изображениях, и этот метод обнаружения оптимизирован для операций сравнения. Он не извлекает подробные атрибуты лица, как в предыдущем разделе, и использует другую модель распознавания.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Поиск совпадений

Следующий метод обнаруживает лица в наборе целевых изображений и в одном исходном изображении. Затем он сравнивает их и находит все целевые изображения, аналогичные исходному изображению.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Вывод совпадений

Следующий код выводит в консоль сведения о совпадениях.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Создание и обучение на основе изображения группы людей

Следующий код создает группу **PersonGroup** с шестью разными объектами **Person**. Он сопоставляет каждый объект **Person** с набором тестовых изображений, а затем обучается распознавать каждого человека по характеристикам их лиц. Объекты **Person** и **PersonGroup** используются в операциях Verify, Identify и Group.

В корне класса определите следующую строку URL-адреса, если вы этого еще не сделали. Она указывает на набор тестовых изображений.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

В коде, который приведен далее в этом разделе, определена модель распознавания для извлечения данных из лиц, а следующий фрагмент кода создает ссылки на доступные модели. Сведения о моделях распознавания см. [здесь](../Face-API-How-to-Topics/specify-recognition-model.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Создание PersonGroup

Объявите в корне класса строковую переменную, которая будет представлять идентификатор создаваемой группы **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

Добавьте указанный ниже код в новый метод. Этот код связывает имена людей с примерами их изображений.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Теперь добавьте следующий код, чтобы создать в словаре объект **Person** для каждого человека и добавить данные о лицах из соответствующих изображений. Каждый объект **Person** связывается с одним объектом **PersonGroup** через уникальную строку идентификатора. Не забудьте передать в этот метод переменные `client`, `url` и `RECOGNITION_MODEL1`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Обучение PersonGroup

После извлечения данных о лицах и сортировки их по разным объектам **Person** вам нужно обучить объект **PersonGroup** для выявления визуальных характеристик, связанных с каждым из объектов **Person** в этой группе. Следующий код вызывает асинхронный метод **train** и опрашивает результаты, а затем выводит сведения о состоянии в консоль.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Эта группа **Person** и связанные с ней объекты **Person** теперь готовы к использованию в операциях проверки, определения или группирования.

## <a name="identify-a-face"></a>опознание лиц;

Операция идентификации (Identify) принимает изображение человека или нескольких людей и пытается опознать каждое лицо на этом изображении. Она сравнивает каждое обнаруженное лицо с **PersonGroup**, которая является базой данных объектов **Person** с известными характеристиками лиц.

> [!IMPORTANT]
> Чтобы выполнить этот пример, сначала необходимо выполнить код из раздела [Создание и обучение на основе изображения группы людей](#create-and-train-a-person-group). Также здесь должны быть доступны переменные, которые используются в этом разделе&mdash;`client`, `url` и `RECOGNITION_MODEL1`&mdash;.

### <a name="get-a-test-image"></a>Получение тестового изображения

Обратите внимание, что [код создания и обучения группы объектов Person](#create-and-train-a-person-group) определяет переменную `sourceImageFileName`. Эта переменная соответствует исходному изображению, которое содержит пользователей для распознавания.

### <a name="identify-faces"></a>Идентификация лиц

Следующий код принимает исходное изображение и создает список всех лиц, обнаруженных в этом изображении. Именно эти лица будут сопоставляться с объектами в группе **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

Следующий фрагмент кода вызывает операцию Identify и выводит результаты на консоль. Служба здесь пытается сопоставить каждое лицо с исходного изображения с определенным объектом **Person** в указанной группе **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>создание моментального снимка для переноса данных.

Функция моментальных снимков позволяет перемещать сохраненные данные о лицах, например обученный объект **PersonGroup**, в другую подписку Распознавания лиц Azure Cognitive Services. Эту функцию можно использовать, если вы, например, создали объект **PersonGroup** в бесплатной пробной подписке и теперь хотите перенести его в платную подписку. Обзор функции создания моментальных снимков см. в руководстве по [переносу данных о лицах](../Face-API-How-to-Topics/how-to-migrate-face-data.md).

В этом примере выполняется перенос объекта **PersonGroup**, созданного при выполнении инструкций из раздела [Создание и обучение на основе изображения группы людей](#create-and-train-a-person-group). Вы можете выполнить эти инструкции или создать для миграции собственные конструкции API Распознавания лиц.

### <a name="set-up-target-subscription"></a>Создание целевой подписки

Во-первых, у вас должна быть вторая подписка Azure с ресурсом API Распознавания лиц. Для этого выполните инструкции из раздела [Настройка](#setting-up). 

Затем определите следующие переменные в методе `Main` вашей программы. Вам потребуется создать новые переменные среды для идентификатора подписки учетной записи Azure, а также ключ, конечную точку и идентификатор подписки для новой (целевой) учетной записи. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

В этом примере объявите переменную для идентификатора целевого объекта **PersonGroup**, который принадлежит новой подписке, в которую будут копироваться данные.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Проверка подлинности целевого клиента

Затем добавьте код для проверки подлинности вторичной подписки Распознавания лиц.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Использование моментального снимка

Остальные операции с моментальным снимком должны выполняться в асинхронном методе. 

1. Первым шагом является **создание** моментального снимка, который сохраняет данные о лицах из исходной подписки во временном облачном расположении. Этот метод возвращает идентификатор, который используется для запроса состояния операции.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Затем запрашивайте идентификатор, пока операция не завершится.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Затем примените операцию **apply** для записи данных о лицах в целевую подписку. Этот метод также возвращает значение идентификатора.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Повторно запрашивайте идентификатор, пока операция не завершится.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Наконец, завершите блок try/catch и завершите метод.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

На этом этапе новый объект **PersonGroup** должен содержать те же данные, что и исходный, и должен быть доступен из новой (целевой) подписки Распознавания лиц Azure.

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение из каталога приложения с помощью команды `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Если при изучении этого краткого руководства вы создали объект **PersonGroup** и хотите его удалить, выполните в программе следующий код:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Определите метод удаления со следующим кодом:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Кроме тоге, если при изучении этого краткого руководства вы перенесли данные с помощью функции создания моментальных снимков, вам также нужно удалить объект **PersonGroup**, сохраненный в целевой подписке.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководство вы узнали, как с помощью клиентской библиотеки API Распознавания лиц для .NET выполнять базовые задачи. Далее ознакомьтесь со справочной документацией, чтобы узнать больше о библиотеке.

> [!div class="nextstepaction"]
> [Справочник по API распознавания лиц (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [What is the Face API?](../overview.md) (Общие сведения об API Распознавания лиц)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs).