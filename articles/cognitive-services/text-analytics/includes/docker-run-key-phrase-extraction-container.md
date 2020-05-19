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
ms.openlocfilehash: 7c7344b7cc9253ef761061b769644a363492d4bd
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588420"
---
Чтобы запустить контейнер *Извлечение ключевых фраз* , выполните следующую `docker run` команду.

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