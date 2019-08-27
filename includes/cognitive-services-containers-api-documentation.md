---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 081155c8984f39cc9cc8e905eb108c07dee98d2e
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034427"
---
## <a name="validate-that-a-container-is-running"></a>Проверка состояния контейнера 

Проверить это можно несколькими способами. Перейдите к *внешнему IP-* адресу и предоставленному порту рассматриваемого контейнера и откройте свой любимый веб-браузер. Используйте различные URL-адреса запросов ниже, чтобы проверить, выполняется контейнер. Примеры URL-адресов запросов приведены ниже `http://localhost:5000`, но конкретный контейнер может отличаться. Помните, что вам нужно полагаться на *внешний IP-* адрес контейнера и доступный порт.

| URL-адрес запроса | Цель |
|--|--|
| `http://localhost:5000/` | Контейнер предоставляет домашнюю страницу. |
| `http://localhost:5000/status` | Запрашивается с помощью HTTP GET, чтобы проверить, выполняется ли контейнер, не вызывая запрос конечной точки. Этот запрос может использоваться для [проб активности и готовности](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) Kubernetes. |
| `http://localhost:5000/swagger` | Контейнер предоставляет полный набор документации по конечным точкам и функцию **пробного** использования. Эта функция позволяет ввести параметры в веб-форму HTML и создать запрос без необходимости писать код. После возвращения результатов запроса предоставляется пример команды CURL с примером требуемого формата HTTP-заголовков и текста. |

![Домашняя страница контейнера](./media/cognitive-services-containers-api-documentation/container-webpage.png)
