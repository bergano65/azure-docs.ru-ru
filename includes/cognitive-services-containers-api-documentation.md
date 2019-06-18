---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2019
ms.openlocfilehash: f4925401235aedb341a7e29ca36b079126647f7b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185561"
---
## <a name="validate-that-a-container-is-running"></a>Проверка состояния контейнера 

Проверить это можно несколькими способами. 

|Запрос|Назначение|
|--|--|
|`http://localhost:5000/`|Контейнер предоставляет домашнюю страницу.|
|`http://localhost:5000/status`|Запрашивается с помощью GET для проверки того, что контейнер работает, без отправки запроса к конечной точке. Этот запрос может использоваться для [проб активности и готовности](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) Kubernetes.|
|`http://localhost:5000/swagger`|Контейнер предоставляет полный набор документации для конечных точек, а также функцию `Try it now`. Эта функция позволяет ввести параметры в веб-форму HTML и создать запрос без необходимости писать код. После возвращения результатов запроса предоставляется пример команды CURL с примером требуемого формата HTTP-заголовков и текста. |

![Домашняя страница контейнера](./media/cognitive-services-containers-api-documentation/container-webpage.png)
