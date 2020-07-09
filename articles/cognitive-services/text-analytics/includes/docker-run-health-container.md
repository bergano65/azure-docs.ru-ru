---
title: Пример запуска контейнера команды запуска DOCKER
titleSuffix: Azure Cognitive Services
description: Команда запуска DOCKER для контейнера работоспособности
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108947"
---
Чтобы запустить контейнер, сначала найдите его идентификатор образа:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Выполните следующую `docker run` команду. Замените заполнители ниже собственными значениями:

| Заместитель | Значение | Формат или пример |
|-------------|-------|---|
| **{API_KEY}** | Ключ для ресурса Анализ текста. Его можно найти на странице **ключа и конечной точки** ресурса на портал Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Конечная точка для доступа к API анализа текста. Его можно найти на странице **ключа и конечной точки** ресурса на портал Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | Идентификатор образа для контейнера. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Входной каталог для контейнера. | Windows: `C:\healthcareMount` <br> Linux и MacOS:`/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Эта команда:

- Предполагается, что входной каталог существует на размещающем компьютере
- Запускает Анализ текста для контейнера работоспособности из образа контейнера.
- Выделяет 6 ядер ЦП и 12 гигабайт (ГБ) памяти.
- предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
- автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.
