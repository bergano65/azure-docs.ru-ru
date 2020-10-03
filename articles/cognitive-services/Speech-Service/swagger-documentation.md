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
ms.openlocfilehash: 6bb50e427fa85a170c5ad23a63d67c01e898a17d
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665690"
---
# <a name="swagger-documentation"></a>Документация по Swagger

Служба распознавания речи предоставляет спецификацию Swagger для взаимодействия с несколькими API-интерфейсами, которые используются для импорта данных, создания моделей, точности тестовых моделей, создания пользовательских конечных точек, добавления в очередь записей пакетов и управления подписками. Большинство операций, доступных на портале настраиваемого распознавания речи, можно выполнить программно с помощью этих API.

> [!NOTE]
> Для поддерживаемых операций преобразования речи в текст и преобразования текста в речь доступны REST API, которые описаны в спецификации Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Создание кода из спецификации Swagger

[Спецификация Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) содержит возможности, которые позволяют быстро протестировать различные пути. Тем не менее иногда желательно сгенерировать код для всех путей, создав единую библиотеку вызовов, на основе которой потом можно будет создавать решения. Давайте рассмотрим процесс создания библиотеки Python.

Необходимо настроить Swagger в том же регионе, что и подписка на службу распознавания речи. Вы можете подтвердить свой регион в портал Azure под Вашим ресурсом службы речи. Полный список поддерживаемых регионов см. в разделе [регионы](regions.md).

1. В браузере перейдите к спецификации Swagger для вашего региона:  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. На этой странице щелкните **Определение API**и щелкните **Swagger**. Скопируйте URL-адрес страницы, которая отображается.
1. В новом браузере перейдите по адресу https://editor.swagger.io
1. Щелкните **файл**, щелкните **Импортировать URL-адрес**, вставьте URL-адрес и нажмите кнопку **ОК**.
1. Щелкните **создать клиент** и выберите **Python**. Клиентская библиотека загружается на компьютер в `.zip` файле.
1. Извлеките все содержимое из скачиваемого файла. Вы можете использовать `tar -xf` для извлечения всех элементов.
1. Установите извлеченный модуль в среду Python:  
       `pip install path/to/package/python-client`
1. Установленный пакет называется `swagger_client` . Убедитесь, что установка работала:  
       `python -c "import swagger_client"`

Вы можете использовать библиотеку Python, созданную с [примерами службы распознавания речи на GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Справочная документация

* [ОСТАВШАЯся (Swagger): пакетная запись и настройка](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API: Преобразование речи в текст](rest-speech-to-text.md)
* [REST API: Преобразование текста в речь](rest-text-to-speech.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Примеры речевых служб на GitHub](https://aka.ms/csspeech/samples).
* [Try the Speech service for free](overview.md#try-the-speech-service-for-free) (Бесплатное использование службы "Речь")
