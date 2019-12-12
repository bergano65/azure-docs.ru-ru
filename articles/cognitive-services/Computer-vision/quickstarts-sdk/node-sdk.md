---
title: Краткое руководство. Клиентская библиотека компьютерного зрения для Node.js | Документация Майкрософт
description: Начало работы с клиентской библиотекой службы "Компьютерное зрение" для Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 199ead07e499575de21c6878cd348e648f604171
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972665"
---
# <a name="quickstart-computer-vision-client-library-for-nodejs"></a>Краткое руководство. Клиентская библиотека службы "Компьютерное зрение" для Node.js

Начало работы с клиентской библиотекой службы "Компьютерное зрение" для Node.js. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. 

С помощью клиентской библиотеки службы "Компьютерное зрение" для Node.js можно выполнять следующие операции:

* [Анализ изображения](#analyze-an-image)
* [Чтение печатного и рукописного текста](#read-printed-and-handwritten-text).

[Справочная документация](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Пакет (npm)](https://www.npmjs.com/package/azure-cognitiveservices-computervision) | [Примеры](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Настройка

### <a name="create-a-computer-vision-azure-resource"></a>Создание ресурса Компьютерного зрения в Azure

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Компьютерного зрения с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [интерфейса командной строки Azure (CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* Получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки [задайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа и URL-адреса конечной точки с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и `COMPUTER_VISION_ENDPOINT` соответственно.
 
### <a name="create-a-new-nodejs-application"></a>создание приложения Node.js;

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. 

```console
mkdir myapp && cd myapp
```

Выполните команду `npm init`, чтобы создать приложение узла с помощью файла `package.json`. 

```console
npm init
```

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Установите пакеты NPM: `ms-rest-azure` и `azure-cognitiveservices-computervision`.

```console
npm install azure-cognitiveservices-computervision ms-rest-azure
```

Файл `package.json` этого приложения будет дополнен зависимостями.

### <a name="prepare-the-nodejs-script"></a>Подготовка скрипта Node.js

Создайте новый файл, *index. js* и откройте его в текстовом редакторе. Добавьте в файл следующие операторы импорта.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Затем определите функцию `computerVision` и объявите асинхронный ряд с первичной функцией и функцией обратного вызова. Вы добавите код краткого руководства в основную функцию и вызовите `computerVision` в нижней части скрипта.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы обрабатывают некоторые основные функции пакета SDK Node.js для службы "Компьютерное зрение".

|ИМЯ|ОПИСАНИЕ|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Этот класс требуется для всех функций Компьютерного зрения. Вы создаете его экземпляр с информацией о подписке и используете его для выполнения большинства операций с образами.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Это перечисление определяет различные типы анализа изображений, которые можно выполнить в стандартной операции анализа. Набор значений **VisualFeatureTypes** указывается в зависимости от потребностей. |

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с помощью клиентской библиотеки службы "Компьютерное зрение" для Node.js:

* [аутентификация клиента](#authenticate-the-client);
* [Анализ изображения](#analyze-an-image)
* [Чтение печатного и рукописного текста](#read-printed-and-handwritten-text).

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте переменные для конечной точки Azure и ключа ресурса. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Создайте экземпляр клиента с конечной точкой и ключом. Создайте объект [ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) с помощью своего ключа и конечной точкой для создания объекта [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Анализ изображения

Код в этом разделе анализирует удаленные образы для извлечения различных визуальных компонентов. Эти операции можно выполнить как часть метода **analyzeImage** клиентского объекта, или же их можно вызвать с помощью отдельных методов. Дополнительные сведения см. в [справочной документации](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest).

> [!NOTE]
> Можно также проанализировать локальный образ. См. пример кода на [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) для сценариев, включающих локальные образы.

### <a name="get-image-description"></a>Получение описания изображения

Следующий код получает список созданных заголовков для изображения. Дополнительные сведения см. в статье [Описание изображений на понятном для пользователя языке](../concept-describing-images.md).

Сначала определите URL-адрес изображения для анализа:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Затем добавьте следующий код, чтобы получить описание образа и вывести его на консоль.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Получение категории изображения

Следующий код получает обнаруженную категорию изображения. Дополнительные сведения см. в статье [Categorize images by subject matter](../concept-categorizing-images.md) (Классификация изображений по темам).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Определите вспомогательную функцию `formatCategories`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Получение тегов изображения

Следующий код получает набор обнаруженных тегов изображения. Дополнительные сведения см. в статье о [тегах содержимого](../concept-tagging-images.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Определите вспомогательную функцию `formatTags`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Обнаружение объектов

Следующий код обнаруживает общие объекты в образе и выводит их на консоль. Дополнительные сведения см. в статье [Обнаружение объектов](../concept-object-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Определите вспомогательную функцию `formatRectObjects`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Обнаружение торговых марок

Следующий код обнаруживает фирменные торговые марки и логотипы в образе и выводит их на консоль. Дополнительные сведения см. в статье [Обнаружение торговых марок](../concept-brand-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Распознавание лиц

Следующий код возвращает обнаруженные лица на изображении с их координатами прямоугольника и атрибутами выбора лиц. Дополнительные сведения см. в статье [Определение лиц с помощью компьютерного зрения](../concept-detecting-faces.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Определите вспомогательную функцию `formatRectFaces`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Обнаружение содержимого для взрослых, непристойного или жуткого содержимого

Следующий код выводит сведения об обнаружении содержимого для взрослых на изображении. Дополнительные сведения см. в статье [Detect adult content](../concept-detecting-adult-content.md) (Обнаружение содержимого для взрослых).

Укажите URL-адрес для изображения, которое необходимо использовать:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)] 

Затем добавьте следующий код для обнаружения содержимого для взрослых и вывода результатов на консоль.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)] 

### <a name="get-image-color-scheme"></a>Получение цветовой схемы изображения

Следующий код выводит обнаруженные атрибуты цвета на изображении, например доминирующие и акцентные цвета. Дополнительные сведения см. в статье [Обнаружение цветовых схем на изображениях](../concept-detecting-color-schemes.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Определите вспомогательную функцию `printColorScheme` для вывода сведений о цветовой схеме на консоль.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Получение содержимого, связанного с определенной предметной областью

Компьютерное зрение может использовать специализированную модель для дальнейшего анализа изображений. Дополнительные сведения см. в статье [Обнаружение содержимого, связанного с определенными предметными областями](../concept-detecting-domain-content.md). 

Сначала определите URL-адрес изображения для анализа:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Следующий код анализирует данные об обнаруженных достопримечательностях на изображении.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Определите вспомогательную функцию `formatRectDomain`, чтобы проанализировать данные расположения обнаруженных ориентиров.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Получение сведений о типе изображения

Следующий код выводит информацию о типе изображения&mdash;, независимо от того, является ли это картинкой или рисунком.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Определите вспомогательную функцию `describeType`:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Чтение печатного и рукописного текста

Компьютерное зрение может считывать видимый текст в образе и преобразовывать его в поток символов.

> [!NOTE]
> Кроме того, вы можете прочитать текст из локального образа. См. пример кода на [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) для сценариев, включающих локальные образы.

### <a name="set-up-test-images"></a>Настройка тестовых изображений

Сохраните ссылку на URL-адрес изображений, из которых вы хотите извлечь текст.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Вызов API распознавания

Добавьте приведенный ниже код, который вызывает функцию `recognizeText` для заданных изображений.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Определите функцию `recognizeText`. При этом для клиентского объекта вызывается метод **recognizeText**, который возвращает идентификатор операции и запускает асинхронный процесс чтения содержимого образа. Потом он использует идентификатор операции, чтобы проверить операцию с интервалами в одну секунду, пока не будут возвращены результаты. Затем он возвращает извлеченные результаты.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

После определите вспомогательную функцию `printRecText`, которая выводит результаты операции распознавания на консоль.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `node` для файла quickstart.

```console
node index.js
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
>[Справочник по API службы "Компьютерное зрение" (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Что собой представляет Компьютерное зрение](../Home.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
