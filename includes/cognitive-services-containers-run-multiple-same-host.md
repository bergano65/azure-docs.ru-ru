---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/21/2019
ms.openlocfilehash: e80feac7dbf16652cc2e2a6176ed8b2c8c48e35b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598903"
---
### <a name="running-multiple-containers-on-the-same-host"></a>Запуск нескольких контейнеров на одном узле

Если планируется выполнить несколько контейнеров с помощью открытых портов, следует проводить каждый контейнер с другим портом. Например выполните первый контейнер на порт 5000 и второй контейнер на порту 5001.

Замените `<container-registry>` и `<container-name>` со значениями из контейнеров, можно использовать. Они больше не нужно быть тот же контейнер. Может иметь контейнер лиц и LUIS контейнеров, работающих на узле друг с другом или может иметь несколько контейнеров лиц. 

Запустите первый контейнер с портом 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Второй контейнер запуска на порту 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Каждый последующий контейнер должен находиться на другой порт. 
