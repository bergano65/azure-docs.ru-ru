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
ms.openlocfilehash: 459ab7a254994929174e4f81ad25f0729f43fa0b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108976"
---
Чтобы запустить контейнер *Анализ тональности v3* , выполните следующую `docker run` команду. Замените заполнители ниже собственными значениями:

| Заместитель | Значение | Формат или пример |
|-------------|-------|---|
| **{API_KEY}** | Ключ для ресурса Анализ текста. Его можно найти на странице **ключа и конечной точки** ресурса на портал Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Конечная точка для доступа к API анализа текста. Его можно найти на странице **ключа и конечной точки** ресурса на портал Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер *Анализ тональности* из образа контейнера.
* Выделение одного ядра ЦП и 8 гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.