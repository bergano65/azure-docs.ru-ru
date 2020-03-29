---
title: Документация Swagger - Служба речи
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
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77430811"
---
# <a name="swagger-documentation"></a>Документация по Swagger

Служба речевого сообщения предлагает спецификацию Swagger для взаимодействия с горсткой REST AIS, используемых для импорта данных, создания моделей, точности тестирования модели, создания пользовательских конечных точек, очереди пакетных транскрипций и управления подписками. Большинство операций, доступных на портале настраиваемого распознавания речи, можно выполнить программно с помощью этих API.

> [!NOTE]
> Для поддерживаемых операций преобразования речи в текст и преобразования текста в речь доступны REST API, которые описаны в спецификации Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Создание кода из спецификации Swagger

[Спецификация Swagger](https://cris.ai/swagger/ui/index) содержит возможности, которые позволяют быстро протестировать различные пути. Тем не менее иногда желательно сгенерировать код для всех путей, создав единую библиотеку вызовов, на основе которой потом можно будет создавать решения. Давайте рассмотрим процесс создания библиотеки Python.

Вам нужно будет установить Swagger в том же регионе, что и подписка на услугу speech. Подтвердить свой регион можно на портале Azure на ресурсе службы speech. Полный список поддерживаемых регионов можно узнать в [регионах.](regions.md)

1. Перейдите на сайт https://editor.swagger.io.
2. В меню **File** (Файл) выберите **Import** (Импортировать).
3. Введите url-адрес swagger, включая регион для подписки на услуги speech`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Щелкните **Generate Client** (Создать клиент) и выберите Python.
5. Сохраните клиентскую библиотеку.

Вы можете использовать библиотеку Python, которую вы создали с [образцами службы речи на GitHub.](https://aka.ms/csspeech/samples)

## <a name="reference-docs"></a>Справочная документация

* [REST (Swagger): Транскрипция и настройка пакета](https://westus.cris.ai/swagger/ui/index)
* [REST API: От речи к тексту](rest-speech-to-text.md)
* [REST API: Текст к речи](rest-text-to-speech.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Образцы речевой службы на GitHub](https://aka.ms/csspeech/samples).
* [Try the Speech service for free](get-started.md) (Бесплатное использование службы "Речь")
