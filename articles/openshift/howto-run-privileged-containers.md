---
title: Запуск привилегированных контейнеров в кластере Azure Red Hat OpenShift (ru) Документы Майкрософт
description: Запуск привилегированных контейнеров для мониторинга безопасности и соответствия требованиям.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: аро, openshift, аквасек, твистлок, красная шляпа
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271375"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Запуск привилегированных контейнеров в кластере Azure Red Hat OpenShift

Вы не можете запускать произвольные привилегированные контейнеры в кластерах Azure Red Hat OpenShift.
На кластерах ARO разрешено работать два решения по мониторингу безопасности и соблюдению требований.
В этом документе описаны отличия от общей документации развертывания OpenShift поставщиков продуктов безопасности.


Прочитайте эти инструкции, прежде чем следовать инструкциям поставщика.
Названия разделов в приведенных ниже шагах, посвященных конкретным продуктам, непосредственно относятся к заголовкам разделов в документации поставщиков.

## <a name="before-you-begin"></a>Перед началом

Документация большинства продуктов безопасности предполагает, что у вас есть привилегии кластерного админа.
Клиентские админы не имеют всех привилегий в Azure Red Hat OpenShift. Разрешения, необходимые для изменения ресурсов масштаба, ограничены.

Во-первых, убедитесь, что пользователь входит в кластер в качестве `oc get scc`клиента-админ, запустив. Все пользователи, входят в группу админ-клиентов, имеют разрешения на просмотр ограничений контекста безопасности (SCCs) в кластере.

Далее, убедитесь, что двоичная `oc` версия `3.11.154`.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Конкретные шаги по безопасности в области продукции
Базовые инструкции, которые будут изменены, можно найти в [документации развертывания Aqua Security.](https://docs.aquasec.com/docs/openshift-red-hat) Шаги здесь будут работать в сочетании с документацией о развертывании Aqua.

Первым шагом является аннотировать необходимые SCCs, которые будут обновлены. Эти аннотации не позволяют Sync Pod кластера возвращать любые изменения в эти SSC.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Шаг 1: Подготовка предварительных условий
Не забудьте войти в кластер в качестве админа клиента ARO вместо роли кластера-админ.

Создайте учетную запись проекта и службы.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Вместо того, чтобы назначать роль кластерного чтения, назначаем роль кластера клиента-админ-кластера аква-счету со следующей командой.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Продолжайте следовать оставшимся инструкциям в шаге 1.  Эти инструкции описывают создание секрета для реестра Aqua.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Шаг 2: Развертывание Aqua Server, базы данных и шлюза
Выполните действия, предусмотренные в документации Aqua для установки aqua-console.yaml.

Измените `aqua-console.yaml`предоставленное .  Удалите два верхних `kind: ClusterRole` `kind: ClusterRoleBinding`объекта с пометкой и .  Эти ресурсы не будут создаваться, так как у клиента, который `ClusterRole` в `ClusterRoleBinding` настоящее время не имеет разрешения на изменение и объекты.

Вторая модификация будет `kind: Route` в `aqua-console.yaml`части . Замените следующий yaml `kind: Route` для `aqua-console.yaml` объекта в файле.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Следуйте оставшимся инструкциям.

### <a name="step-3-login-to-the-aqua-server"></a>Шаг 3: Войти в Aqua Server
Этот раздел никоим образом не изменяется.  Следите за документацией Aqua.

Используйте следующую команду, чтобы получить адрес Aqua Console.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Шаг 4: Развертывание Aqua Enforcers
Установите следующие поля при развертывании силовиков:

| Поле          | Значение         |
| -------------- | ------------- |
| Оркестратор:   | OpenShift     |
| ServiceAccount | аква-счет  |
| Проект        | аква-безопасности |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Конкретные действия по продукту для Облака Prisma / Twistlock

Базовые инструкции, которые мы собираемся изменить, можно найти в [документации по развертыванию Prisma Cloud](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Начните с `twistcli` установки инструмента, как описано в разделах "Установка облака Prisma" и "Загрузить программное обеспечение Prisma Cloud".

Создание нового проекта OpenShift
```
oc new-project twistlock
```

Пропустить дополнительный раздел "Нажмите Prisma Облако изображения в частный реестр". Он не будет работать на Azure Red Hat Openshift. Вместо этого используйте онлайн-реестр.

Вы можете следить за официальной документацией при применении исправлений, описанных ниже.
Начните с раздела "Установка консоли".

### <a name="install-console"></a>Установка консоли

Во `oc create -f twistlock_console.yaml` время вшаг 2, вы получите ошибку при создании пространства имен.
Вы можете безопасно игнорировать его, пространство имен было `oc new-project` создано ранее с командой.

Используется `azure-disk` для хранения.

### <a name="create-an-external-route-to-console"></a>Создание внешнего маршрута для консоли

Вы можете либо следовать документации, или инструкции ниже, если вы предпочитаете команду OC.
Копируйте следующее определение маршрута в файле под названием twistlock_route.yaml на вашем компьютере
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
затем запустить:
```
oc create -f twistlock_route.yaml
```

Эту команду можно получить URL-адрес консоли Twistlock:`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Настройка консоли

Следите за документацией Twistlock.

### <a name="install-defender"></a>Установка Защитника

Во `oc create -f defender.yaml` время вшаг 2 вы получите ошибки при создании связывания роли кластера и кластера.
Их можно проигнорировать.

Защитники будут развернуты только на вычислительных узлах. Вы не должны ограничивать их селектора узла.
