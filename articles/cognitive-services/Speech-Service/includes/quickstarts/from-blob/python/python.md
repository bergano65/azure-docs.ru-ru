---
title: Краткое руководство. Распознавание речи из файлов, находящихся в хранилище BLOB-объектов, в службе "Речь" с помощью C#
titleSuffix: Azure Cognitive Services
description: ПОДЛЕЖИТ УТОЧНЕНИЮ
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 9223fa484d45b927059c73e23e0607526e70fae4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829155"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Отправить исходный файл в большой двоичный объект Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md).

## <a name="download-and-install-the-api-client-library"></a>Скачивание и установка клиентской библиотеки API

Чтобы выполнить пример, необходимо создать библиотеку Python для REST API, которая создается с помощью [Swagger](https://swagger.io).

Для установки сделайте следующее:

1. Перейдите на сайт https://editor.swagger.io.
1. В меню **File** (Файл) выберите **Import URL** (Импортировать с URL-адреса).
1. Введите URL-адрес Swagger, включая регион для вашей подписки служб "Речь": `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. Щелкните **Generate Client** (Создать клиент) и выберите **Python**.
1. Сохраните клиентскую библиотеку.
1. Извлеките скачанный файл python-client-generated.zip в любое расположение в своей файловой системе.
1. Установите извлеченный модуль python-client в среде Python с помощью pip: `pip install path/to/package/python-client`.
1. Имя установленного пакета — `swagger_client`. С помощью команды `python -c "import swagger_client"` можно проверить, работает ли установка.

> **Примечание.** Из-за [известной ошибки с автоматическим созданием Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541) при импорте пакета `swagger_client` могут возникнуть ошибки.
> Их можно исправить, удалив строку с содержимым
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> из файла `swagger_client/models/model.py` и строку с содержимым
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> из файла `swagger_client/models/inner_error.py` в установленном пакете. В сообщении об ошибке будет указано, где находятся файлы для установки.

## <a name="install-other-dependencies"></a>Установка других зависимостей

В примере используется библиотека `requests`. Ее можно установить с помощью следующей команды:

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Использование стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]
(Замените значения `YourSubscriptionKey`, `YourServiceRegion` и `YourFileUrl` собственными.)

## <a name="create-and-configure-an-http-client"></a>Создание и настройка клиента HTTP
Первое, что нам потребуется, — это HTTP-клиент с правильным базовым URL-адресом и настроенной проверкой подлинности.
Вставьте этот код в `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)].

## <a name="generate-a-transcription-request"></a>Создание запроса на транскрибирование
Далее создайте запрос на транскрибирование. Добавьте этот код в `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)].

## <a name="send-the-request-and-check-its-status"></a>Отправка запроса и проверка его состояния
Теперь отправьте запрос в службу "Речь" и проверьте исходный код ответа. Этот код отклика указывает, получила ли служба запрос. Служба вернет в заголовках ответа URL-адрес, по которому будет храниться состояние транскрибирования.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Ожидание завершения транскрибирования
Так как служба обрабатывает транскрибирование в асинхронном режиме, необходимо запрашивать ее состояние очень часто. Мы будем выполнять проверку каждые 5 секунд.

Мы перечислим все расшифровки, обрабатываемые этим ресурсом службы "Речь", и будем искать созданную вами расшифровку.

Ниже приведен код опроса с отображением всех состояний, кроме состояний об успешном завершении.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Отображение результатов транскрибирования
После того как служба успешно завершит транскрибирование, результаты будут храниться по другому URL-адресу, который можно получить из ответа о состоянии.

Здесь мы получаем JSON-файл результатов и отображаем его.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Проверка кода
На этом этапе код должен выглядеть так: (Мы добавили несколько комментариев к этой версии.) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Создание и запуск приложения

Теперь можно приступать к созданию приложения и проверке распознавания речи, используя службу "Речь".

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]
