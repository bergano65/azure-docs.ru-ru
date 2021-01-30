---
title: Настройка пересылки DNS для Azure Red Hat OpenShift 4
description: Настройка пересылки DNS для Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: aa18959d0dc78dedf8b57dc120ab6744afa9051f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070599"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Настройка перенаправления DNS в кластере Azure Red Hat OpenShift 4

Чтобы настроить пересылку DNS в кластере Azure Red Hat OpenShift, необходимо изменить оператор DNS. Это изменение позволит приложениям, работающим внутри кластера, разрешать имена, размещенные на частном DNS-сервере за пределами кластера. Эти действия описаны для OpenShift 4,6 [здесь](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).

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
Ознакомьтесь с дополнительными сведениями о пересылке DNS для OpenShift 4,6 [здесь](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).