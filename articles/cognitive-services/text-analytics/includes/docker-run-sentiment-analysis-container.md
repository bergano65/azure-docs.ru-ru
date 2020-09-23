---
title: Пример запуска контейнера команды запуска DOCKER
titleSuffix: Azure Cognitive Services
description: Команда DOCKER Run для контейнера анализ тональности
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: e4a08217b5476f4934325518bec00d640003aece
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906029"
---
Чтобы запустить контейнер *Анализ тональности v3* , выполните следующую `docker run` команду. Замените заполнители ниже собственными значениями:

| Заполнитель | Значение | Формат или пример |
|-------------|-------|---|
| **{API_KEY}** | Ключ для ресурса Анализ текста. Его можно найти на странице **ключа и конечной точки** ресурса на портал Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Конечная точка для доступа к API анализа текста. Его можно найти на странице **ключа и конечной точки** ресурса на портал Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер *Анализ тональности* из образа контейнера.
* Выделение одного ядра ЦП и 8 гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.