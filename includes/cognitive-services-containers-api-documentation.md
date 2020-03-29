---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70034427"
---
## <a name="validate-that-a-container-is-running"></a>Проверка состояния контейнера 

Проверить это можно несколькими способами. Найдите *внешний IP-адрес* и открытый порт рассматриваемого контейнера и откройте свой любимый веб-браузер. Используйте различные URL-адреса запроса ниже, чтобы проверить запуск контейнера. Пример URL-адреса запроса, перечисленные ниже, являются, `http://localhost:5000`но ваш конкретный контейнер может варьироваться. Имейте в виду, что вы должны полагаться на *внешний IP-адрес* вашего контейнера и открытый порт.

| Request URL (URL-адрес запроса) | Назначение |
|--|--|
| `http://localhost:5000/` | Контейнер предоставляет домашнюю страницу. |
| `http://localhost:5000/status` | Запрос с HTTP GET, чтобы проверить, что контейнер работает без вызывая запрос конечная точка. Этот запрос может использоваться для [проб активности и готовности](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) Kubernetes. |
| `http://localhost:5000/swagger` | Контейнер предоставляет полный набор документации для конечных точек и **функцию Try it out.** Эта функция позволяет ввести параметры в веб-форму HTML и создать запрос без необходимости писать код. После возвращения результатов запроса предоставляется пример команды CURL с примером требуемого формата HTTP-заголовков и текста. |

![Домашняя страница контейнера](./media/cognitive-services-containers-api-documentation/container-webpage.png)
