---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: 94e95864d8bac2d6dc0ff690a2a8f53bd2db5a40
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522526"
---
## <a name="validate-container-is-running"></a>Проверка выполняется контейнер. 

Существует несколько способов проверить контейнер работает: 

|Запрос|Назначение|
|--|--|
|`http://localhost:5000/`|Контейнер предоставляет домашнюю страницу.|
|`http://localhost:5000/status`|Запрошенный с помощью GET, проверяемый контейнер выполняется без возникновения запроса конечной точки. Это может использоваться для Kubernetes [проверки активности и готовности](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Контейнер предоставляет полный набор документации для конечных точек, а также функцию `Try it now`. Эта функция позволяет ввести параметры в веб-форму HTML и создать запрос без необходимости писать код. После возвращения результатов запроса предоставляется пример команды CURL с примером требуемого формата HTTP-заголовков и текста. |

![Домашняя страница контейнера](./media/cognitive-services-containers-api-documentation/container-webpage.png)
