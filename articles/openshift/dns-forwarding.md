---
title: Настройка пересылки DNS для Azure Red Hat OpenShift 4
description: Настройка пересылки DNS для Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6784c69c049945f3bd4977ecd647d57ec59bf9f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82232638"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Настройка перенаправления DNS в кластере Azure Red Hat OpenShift 4

Чтобы настроить пересылку DNS в кластере Azure Red Hat OpenShift, необходимо изменить оператор DNS. Это изменение позволит приложениям, работающим внутри кластера, разрешать имена, размещенные на частном DNS-сервере за пределами кластера. Эти действия описаны для OpenShift 4,3 [здесь](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).

Например, если вы хотите перенаправить все DNS-запросы для *. example.com, которые должны быть разрешены сервером 192.168.100.10 DNS, можно изменить конфигурацию оператора, выполнив команду:
 
```bash
oc edit dns.operator/default
```
 
Запустится редактор и можно будет заменить `spec: {}` на:
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Сохраните файл и закройте редактор.

## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с дополнительными сведениями о пересылке DNS для OpenShift 4,3 [здесь](https://docs.openshift.com/container-platform/4.3/networking/dns-operator.html).