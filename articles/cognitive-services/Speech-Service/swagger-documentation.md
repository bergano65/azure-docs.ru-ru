---
title: Документация по Swagger — служба речи
titleSuffix: Azure Cognitive Services
description: Документацию по Swagger можно использовать для автоматического создания пакетов SDK для различных языков программирования. Все операции в нашей службе поддерживаются Swagger.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 232435a424d2461bce4598356a986473cb1d3644
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552577"
---
# <a name="swagger-documentation"></a>Документация по Swagger

Службы распознавания речи предлагают спецификацию Swagger для взаимодействия с рядом интерфейсов REST API, используемых для импорта данных, создания моделей, проверки точности моделей, создания пользовательских конечных точек, постановки пакетных операция транскрибирования в очередь и управления подписками. Большинство операций, доступных на портале настраиваемого распознавания речи, можно выполнить программно с помощью этих API.

> [!NOTE]
> Для поддерживаемых операций преобразования речи в текст и преобразования текста в речь доступны REST API, которые описаны в спецификации Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Создание кода из спецификации Swagger

[Спецификация Swagger](https://cris.ai/swagger/ui/index) содержит возможности, которые позволяют быстро протестировать различные пути. Тем не менее иногда желательно сгенерировать код для всех путей, создав единую библиотеку вызовов, на основе которой потом можно будет создавать решения. Давайте рассмотрим процесс создания библиотеки Python.

Необходимо настроить для Swagger тот же регион, что у вашей подписки службы распознавания речи. Узнать, какой регион используется, можно в ресурсе службы распознавания речи на портале Azure. Полный список поддерживаемых регионов см. в разделе [Регионы](regions.md).

1. Перейдите на сайт https://editor.swagger.io.
2. В меню **File** (Файл) выберите **Import** (Импортировать).
3. Введите URL-адрес Swagger, включая регион для вашей подписки служб распознавания речи `https://<your-region>.cris.ai/docs/v2.0/swagger`.
4. Щелкните **Generate Client** (Создать клиент) и выберите Python.
5. Сохраните клиентскую библиотеку.

Созданную библиотеку Python можно использовать с [примерами для служб распознавания речи на портале GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Справочная документация

* [REST (Swagger): Пакетное транскрибирование и настройка](https://westus.cris.ai/swagger/ui/index)
* [REST API: Преобразование речи в текст](rest-speech-to-text.md)
* [REST API: Преобразование текста в речь](rest-text-to-speech.md)

## <a name="next-steps"></a>Следующие шаги

* [Примеры для служб распознавания речи на портале GitHub](https://aka.ms/csspeech/samples)
* [Получите ключ подписки для Служб речи бесплатно](get-started.md)
