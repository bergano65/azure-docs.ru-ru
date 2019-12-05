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
ms.openlocfilehash: dd9ac9654f916653af974e816485630423466ae5
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815426"
---
# <a name="swagger-documentation"></a>Документация по Swagger

Служба распознавания речи предоставляет спецификацию Swagger для взаимодействия с несколькими API-интерфейсами, которые используются для импорта данных, создания моделей, точности тестовых моделей, создания пользовательских конечных точек, добавления в очередь записей пакетов и управления подписками. Большинство операций, доступных на портале настраиваемого распознавания речи, можно выполнить программно с помощью этих API.

> [!NOTE]
> Для поддерживаемых операций преобразования речи в текст и преобразования текста в речь доступны REST API, которые описаны в спецификации Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Создание кода из спецификации Swagger

[Спецификация Swagger](https://cris.ai/swagger/ui/index) содержит возможности, которые позволяют быстро протестировать различные пути. Тем не менее иногда желательно сгенерировать код для всех путей, создав единую библиотеку вызовов, на основе которой потом можно будет создавать решения. Давайте рассмотрим процесс создания библиотеки Python.

Необходимо настроить Swagger в том же регионе, что и подписка на службу распознавания речи. Вы можете подтвердить свой регион в портал Azure под Вашим ресурсом службы речи. Полный список поддерживаемых регионов см. в разделе [Регионы](regions.md).

1. Перейдите на сайт https://editor.swagger.io.
2. В меню **File** (Файл) выберите **Import** (Импортировать).
3. Введите URL-адрес Swagger, включая регион для подписки на службу речи `https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Щелкните **Generate Client** (Создать клиент) и выберите Python.
5. Сохраните клиентскую библиотеку.

Вы можете использовать библиотеку Python, созданную с [примерами службы распознавания речи на GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Справочные документы

* [ОСТАВШАЯся (Swagger): пакетная запись и настройка](https://westus.cris.ai/swagger/ui/index)
* [REST API: преобразование речи в текст](rest-speech-to-text.md)
* [REST API: преобразование текста в речь](rest-text-to-speech.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Примеры речевых служб на GitHub](https://aka.ms/csspeech/samples).
* [Try the Speech service for free](get-started.md) (Бесплатное использование службы "Речь")
