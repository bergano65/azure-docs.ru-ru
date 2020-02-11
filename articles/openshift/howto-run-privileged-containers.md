---
title: Выполнение привилегированных контейнеров в кластере Azure Red Hat OpenShift | Документация Майкрософт
description: Выполнение привилегированных контейнеров для мониторинга безопасности и соответствия требованиям.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: АТО, openshift, акуасек, Twistlock, Red Hat
ms.openlocfilehash: 4241296a991283f14fbb294fdc059ecde58d6d75
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069666"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Выполнение привилегированных контейнеров в кластере Azure Red Hat OpenShift

Вы не можете запускать произвольные привилегированные контейнеры в кластерах Azure Red Hat OpenShift.
В кластерах АТО может выполняться два решения для мониторинга безопасности и соответствия требованиям.
В этом документе описаны отличия общей документации по развертыванию OpenShift для поставщиков продуктов безопасности.


Прочтите эти инструкции перед выполнением инструкций поставщика.
Заголовки разделов в шагах, связанных с конкретными продуктами, приведены непосредственно к заголовкам разделов в документации поставщиков.

## <a name="before-you-begin"></a>Перед началом

В документацию по большинству продуктов безопасности предполагается, что у вас есть права администратора кластера.
Администраторы клиентов не имеют всех привилегий в Azure Red Hat OpenShift. Разрешения, необходимые для изменения ресурсов на уровне кластера, ограничены.

Сначала убедитесь, что пользователь вошел в кластер в качестве администратора клиента, запустив `oc get scc`. Все пользователи, являющиеся членами группы администраторов клиентов, имеют разрешения на просмотр ограничений контекста безопасности (SCC) в кластере.

Затем убедитесь, что `oc` двоичная версия `3.11.154`.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Действия по обеспечению безопасности для голубого, специфичные для продукта
Базовые инструкции, которые планируется изменить, можно найти в [документации по развертыванию системы безопасности в голубом цвете](https://docs.aquasec.com/docs/openshift-red-hat). Описанные здесь действия будут выполняться в сочетании с документацией по развертыванию по голубому цвету.

Первым шагом является добавление заметка к требуемым SCC, которые будут обновлены. Эти аннотации не позволяют модулю синхронизации кластера восстановить изменения, внесенные в эти ССКС.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Шаг 1. Подготовка необходимых компонентов
Не забудьте войти в кластер как администратор клиента АТО, а не роль администратора кластера.

Создайте проект и учетную запись службы.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Вместо назначения роли "читатель кластера" назначьте роль "клиент-Администратор-кластер" голубой учетной записи с помощью следующей команды.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Продолжайте выполнение оставшихся инструкций, описанных в шаге 1.  Эти инструкции описывают настройку секрета для голубого реестра.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Шаг 2. Развертывание голубого сервера, базы данных и шлюза
Выполните действия, описанные в голубой документации по установке голубого Console. YAML.

Измените предоставленный `aqua-console.yaml`.  Удалите два верхних объекта с меткой, `kind: ClusterRole` и `kind: ClusterRoleBinding`.  Эти ресурсы не будут созданы, так как администратор клиента не имеет разрешения на это время для изменения `ClusterRole` и `ClusterRoleBinding` объектов.

Вторым изменением будет `kind: Route` часть `aqua-console.yaml`. Замените следующий YAML для объекта `kind: Route` в `aqua-console.yaml`ном файле.
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

### <a name="step-3-login-to-the-aqua-server"></a>Шаг 3. вход на голубой сервер
Этот раздел не изменяется каким бы то ни было образом.  Следуйте документации по голубому цвету.

Чтобы получить голубой адрес консоли, используйте следующую команду.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Шаг 4. Развертывание голубых триггеров
При развертывании принудительно установите следующие поля:

| Поле          | Значение         |
| -------------- | ------------- |
| Оркестратор:   | OpenShift     |
| ServiceAccount | Голубой — учетная запись  |
| Проект        | Голубой — безопасность |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Действия, относящиеся к продукту для Присма Cloud/Twistlock

Базовые инструкции, которые мы собираемся изменить, можно найти в [документации по развертыванию Присма Cloud](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html) .

Начните с создания нового проекта OpenShift
```
oc new-project twistlock
```

Вы можете следовать документации до раздела "Установка консоли", используя реестр контейнеров облака Присма вместо создания внутреннего.

### <a name="install-console"></a>Установить консоль

Во время `oc create -f twistlock_console.yaml` на шаге 2 при создании пространства имен возникнет ошибка.
Можно спокойно проигнорировать его, пространство имен было создано ранее с помощью команды `oc new-project`.

### <a name="create-an-external-route-to-console"></a>Создание внешнего маршрута к консоли

Вы можете воспользоваться документацией или приведенными ниже инструкциями, если вы предпочитаете команду OC.
Скопируйте следующее определение маршрута в файл с именем twistlock_route. YAML на компьютере.
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
затем выполните:
```
oc create -f twistlock_route.yaml
```

URL-адрес, назначенный консоли Twistlock, можно получить с помощью следующей команды: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Настройка консоли

Следуйте указаниям в документации по Twistlock.

### <a name="install-defender"></a>Установка защитника

Во время `oc create -f defender.yaml` на шаге 2 вы получите сообщения об ошибках при создании роли кластера и привязки роли кластера.
Их можно игнорировать.

Защитники будут развернуты только на узлах вычислений. Их не нужно ограничивать с помощью селектора узлов.
