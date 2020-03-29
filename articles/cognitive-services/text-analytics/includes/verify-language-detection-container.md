---
title: Проверка экземпляра контейнера language Detection
titleSuffix: Azure Cognitive Services
description: Узнайте, как проверить экземпляр контейнера Language Detection.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: f4e0770bc052044a408f2c4088f2bd5ead225aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968608"
---
### <a name="verify-the-language-detection-container-instance"></a>Проверка экземпляра контейнера language Detection

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

1. Установить **showStats** к `true`.

1. Выберите **Выполнение,** чтобы определить настроение текста.

    Модель, упакованная в контейнер, генерирует балл, который колеблется от 0 до 1, где 0 является негативным настроением и 1 является положительным настроением.

    Ответ JSON, который возвращается включает в себя настроения для обновленного ввода текста:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Теперь мы можем соотнести документы данных JSON полезной нагрузки ответа `id`с исходными документами полезной нагрузки запроса соответствующими. Каждый документ обрабатывается независимо, `characterCount` содержащий `transactionCount`различные статистические данные, такие как и . Кроме того, каждый `detectedLanguages` полученный `name`документ `iso6391Name`имеет `score` массив с , и для каждого языка обнаружены. При обнаружении нескольких `score` языков используется для определения наиболее вероятного языка.