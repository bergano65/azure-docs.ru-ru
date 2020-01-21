---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 1022a744564ed61a90973f7bba3eb32e9a632b46
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942814"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Настроить среду разработки.](../../../../quickstarts/setup-platform.md?tabs=vs&pivots=programmming-language-csharp)
> * [Создать пустой пример проекта.](../../../../quickstarts/create-project.md?pivots=programmming-language-csharp)
> * [Создать ресурс службы "Речь" Azure.](../../../../get-started.md)
> * [Отправить исходный файл в большой двоичный объект Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

## <a name="open-your-project-in-visual-studio"></a>Откройте проект в Visual Studio.

Сначала необходимо убедиться, что проект открыт в Visual Studio.

1. Запустите Visual Studio 2019.
2. Загрузите проект и откройте `Program.cs`.

## <a name="add-a-reference-to-newtonsoftjson"></a>Добавление ссылки на Newtonsoft.Json

1. В Обозревателе решений щелкните правой кнопкой мыши на проект **helloworld** и выберите **Управление пакетами NuGet**, чтобы отобразить Диспетчер пакетов NuGet.

1. В правом верхнем углу найдите раскрывающийся список **Источник пакета** и убедитесь, что выбран вариант **`nuget.org`** .

1. В левом верхнем углу нажмите кнопку **Просмотреть**.

1. В поле поиска введите *newtonsoft.json* и нажмите кнопку **ВВОД**.

1. В результатах поиска выберите пакет [**Newtonsoft.Json**](https://www.nuget.org/packages/Newtonsoft.Json), а затем нажмите кнопку **Установить** для установки последней стабильной версии.

1. Примите все соглашения и лицензии для запуска установки.

   После установки пакета на **Консоли диспетчера пакетов** появится подтверждение.

## <a name="start-with-some-boilerplate-code"></a>Добавление стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-43,138,277)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Программы-оболочки JSON

Так как REST API принимает запросы и возвращает результаты в формате JSON, с ними можно взаимодействовать только с помощью строк, но это не рекомендуется.
Чтобы упростить управление запросами и ответами, объявите несколько классов, которые будут использоваться для сериализации и десериализации JSON.

Укажите объявления для них после класса `TranscribeAsync`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=140-276)]

## <a name="create-and-configure-an-http-client"></a>Создание и настройка клиента HTTP
Первое, что нам потребуется, — это HTTP-клиент с правильным базовым URL-адресом и настроенной проверкой подлинности.
Вставьте этот код в `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=46-50)].

## <a name="generate-a-transcription-request"></a>Создание запроса на транскрибирование
Далее создайте запрос на транскрибирование. Добавьте этот код в `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=52-57)].

## <a name="send-the-request-and-check-its-status"></a>Отправка запроса и проверка его состояния
Теперь отправьте запрос в службу "Речь" и проверьте исходный код ответа. Этот код отклика указывает, получила ли служба запрос. Служба вернет в заголовках ответа URL-адрес, по которому будет храниться состояние транскрибирования.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=59-70)]

## <a name="wait-for-the-transcription-to-complete"></a>Ожидание завершения транскрибирования
Так как служба обрабатывает транскрибирование в асинхронном режиме, необходимо запрашивать ее состояние очень часто. Мы будем выполнять проверку каждые 5 секунд.

Для проверки состояния извлекайте содержимое по URL-адресу, полученному при отправке запроса. После получения содержимого обратно десериализуйте его в один из вспомогательных классов, чтобы упростить взаимодействие с ним.

Ниже приведен код опроса с отображением всех состояний, кроме состояний об успешном завершении.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=72-106,121-137)]

## <a name="display-the-transcription-results"></a>Отображение результатов транскрибирования
После того как служба успешно завершит транскрибирование, результаты будут храниться по другому URL-адресу, который можно получить из ответа о состоянии.

Создайте запрос на скачивание этих результатов во временный файл, прежде чем считывать и десериализовать их.
После загрузки результатов их можно вывести на консоль.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=107-120)]

## <a name="check-your-code"></a>Проверка кода
На этом этапе код должен выглядеть так: (Мы добавили несколько комментариев к этой версии.) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-277)]

## <a name="build-and-run-your-app"></a>Создание и запуск приложения

Теперь можно приступать к созданию приложения и проверке распознавания речи, используя службу "Речь".

1. **Скомпилируйте код**. В строке меню Visual Studio последовательно выберите **Сборка** > **Собрать решение**.
2. **Запустите приложение**. В строке меню выберите **Отладка** > **Начать отладку** или нажмите клавишу **F5**.
3. **Начните распознавание**. Вам будет предложено произнести фразу на английском языке. Речь, записанная в виде текста, отправляется в службу "Речь" и выводится в консоли.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [footer](./footer.md)]
