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
ms.openlocfilehash: 253c391b222c757a367bd5cd39939052cc697b00
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588421"
---
Чтобы запустить контейнер *Анализ тональности v3* , выполните следующую `docker run` команду.

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