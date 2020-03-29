---
title: Проверка экземпляра контейнера анализа настроений
titleSuffix: Azure Cognitive Services
description: Узнайте, как проверить экземпляр контейнера анализа настроений.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 2e201b4ec0d1364ea99b376171efabad65af0a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968610"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Проверка экземпляра контейнера анализа настроений

1. Выберите вкладку **«Обзор»** и скопируйте IP-адрес.
1. Откройте новую вкладку браузера и введите IP-адрес. Например, `http://<IP-address>:5000 (http://55.55.55.55:5000`введите ). Отображается главная страница контейнера, которая позволяет узнать, что контейнер работает.

    ![Просмотр домашней страницы контейнера, чтобы убедиться, что она работает](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Выберите ссылку **на описание API службы,** чтобы перейти на страницу Swagger контейнера.

1. Выберите любой из **AIS POST** и выберите **Попробуйте его.** Параметры отображаются, что включает в себя этот пример ввода:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Замените входные материалы следующим содержанием JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Установить **showStats** к `true`.

1. Выберите **Выполнение,** чтобы определить настроение текста.

    Модель, упакованная в контейнер, генерирует балл, который колеблется от 0 до 1, где 0 является негативным настроением и 1 является положительным настроением.

    Ответ JSON, который возвращается включает в себя настроения для обновленного ввода текста:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Теперь мы можем `id` соотнести документ данных JSON полезной нагрузки `id`ответа с исходным документом полезной нагрузки запроса. Оценка более чем `0.98` указывает на очень позитивные настроения.