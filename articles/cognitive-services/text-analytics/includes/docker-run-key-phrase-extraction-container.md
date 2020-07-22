---
title: Пример запуска контейнера команды запуска DOCKER
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Run для контейнера извлечение ключевых фраз
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 5929a34ea8fbcbb89f3738ac2a10b14353324b6c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108958"
---
Чтобы запустить контейнер *Извлечение ключевых фраз* , выполните следующую `docker run` команду. Замените заполнители ниже собственными значениями:

| Заместитель | Значение | Формат или пример |
|-------------|-------|---|
| **{API_KEY}** | Ключ для ресурса Анализ текста. Его можно найти на странице **ключа и конечной точки** ресурса на портал Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Конечная точка для доступа к API анализа текста. Его можно найти на странице **ключа и конечной точки** ресурса на портал Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер *Извлечение ключевых фраз* из образа контейнера.
* выделяет одно ядро ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.