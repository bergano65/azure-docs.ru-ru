---
title: Краткое руководство. Распознавание речи из файлов, находящихся в хранилище BLOB-объектов, в службе "Речь" с помощью Java
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
ms.openlocfilehash: d4a436540460240ebd97c39d3406b2886586a6df
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828816"
---
## <a name="prerequisites"></a>Предварительные требования

Перед началом работы нужно сделать следующее:

> [!div class="checklist"]
> * [Создать ресурс службы "Речь" Azure](../../../../get-started.md).
> * [Отправить исходный файл в большой двоичный объект Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).
> * [Настроить среду разработки](../../../../quickstarts/setup-platform.md?tabs=dotnet).
> * [Создать пустой пример проекта](../../../../quickstarts/create-project.md?tabs=dotnet).

## <a name="open-your-project-in-eclipse"></a>Открытие проекта с помощью Eclipse

Сначала необходимо убедиться, что проект открыт в Eclipse.

1. Запуск Eclipse
2. Загрузите проект и откройте `Main.java`.

## <a name="add-a-reference-to-gson"></a>Добавление ссылки на Gson
В этом кратком руководстве будет использоваться внешний сериализатор и десериализатор JSON. Для Java мы выбрали [Gson](https://github.com/google/gson).

Откройте файл pom.xml и добавьте следующую ссылку: [!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Использование стандартного кода

Добавим код, который выступает в качестве основы для нашего проекта.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]
(Замените значения `YourSubscriptionKey`, `YourServiceRegion` и `YourFileUrl` собственными.)

## <a name="json-wrappers"></a>Программы-оболочки JSON

Так как REST API принимает запросы и возвращает результаты в формате JSON, с ними можно взаимодействовать только с помощью строк, но это не рекомендуется.
Чтобы упростить управление запросами и ответами, объявите несколько классов, которые будут использоваться для сериализации и десериализации JSON.

Укажите объявления для них перед классом `Main`.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Создание и настройка клиента HTTP
Первое, что нам потребуется, — это HTTP-клиент с правильным базовым URL-адресом и настроенной проверкой подлинности.
Вставьте этот код в `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)].

## <a name="generate-a-transcription-request"></a>Создание запроса на транскрибирование
Далее создайте запрос на транскрибирование. Добавьте этот код в `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)].

## <a name="send-the-request-and-check-its-status"></a>Отправка запроса и проверка его состояния
Теперь отправьте запрос в службу "Речь" и проверьте исходный код ответа. Этот код отклика указывает, получила ли служба запрос. Служба вернет в заголовках ответа URL-адрес, по которому будет храниться состояние транскрибирования.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Ожидание завершения транскрибирования
Так как служба обрабатывает транскрибирование в асинхронном режиме, необходимо запрашивать ее состояние очень часто. Мы будем выполнять проверку каждые 5 секунд.

Для проверки состояния извлекайте содержимое по URL-адресу, полученному при отправке запроса. После получения содержимого обратно десериализуйте его в один из вспомогательных классов, чтобы упростить взаимодействие с ним.

Ниже приведен код опроса с отображением всех состояний, кроме состояний об успешном завершении.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Отображение результатов транскрибирования
После того как служба успешно завершит транскрибирование, результаты будут храниться по другому URL-адресу, который можно получить из ответа о состоянии.

Мы скачаем содержимое, расположенное по этому URL-адресу, десериализуем JSON и переберем результаты, выводимые в виде отображаемого текста в ходе выполнения.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Проверка кода
На этом этапе код должен выглядеть так: (Мы добавили в эту версию несколько комментариев.) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Создание и запуск приложения

Теперь можно приступать к созданию приложения и проверке распознавания речи, используя службу "Речь".

## <a name="next-steps"></a>Дополнительная информация

[!INCLUDE [footer](./footer.md)]
