---
title: Развернуть изолированный Prometheus в кластере Azure Red Hat OpenShift | Документация Майкрософт
description: Вот как можно создать экземпляр Prometheus в кластере Azure Red Hat OpenShift для отслеживания метрик приложения.
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, ато, openshift, метрики, red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342546"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Развернуть изолированный Prometheus в кластере Azure Red Hat OpenShift

В этом руководстве вы узнаете, как настроить автономный Prometheus с процессом обнаружения службы в кластере Azure Red Hat OpenShift.  «Администратор клиента» доступ к кластеру не требуется.

Целевой объект установки выглядит следующим образом:

- один проект (prometheus проект), который содержит Prometheus и Alertmanager
- два проекта (project1 приложения и приложения project2), содержащие приложения для мониторинга

Вы подготовитесь файлы конфигурации Prometheus локально. Создайте новую папку для их хранения.
Эти файлы конфигурации будут храниться в кластере как секреты в случае, если секретных маркеров добавляется к нему позже.

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>Шаг 1. Войдите в кластер с помощью `oc` средства
С помощью веб-браузер, перейдите к веб-консоль кластера (https://openshift. *случайный идентификатор*. *регион*. azmosa.io).
Выполните вход с использованием учетных данных Azure.
Щелкните свое имя пользователя в правом верхнем углу и выберите «Команда копирования имени входа». Вставьте его в окне терминала, которые будут использоваться.

Вы можете проверить, если вы вошли в правильный кластер с `oc whoami -c` команды.

## <a name="step-2-prepare-the-projects"></a>Шаг 2. Подготовка проектов

Создание проектов.
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Вы можете использовать `-n` или `--namespace` параметр или выберите активный проект с `oc project` команды

## <a name="step-3-prepare-prometheus-config"></a>Шаг 3. Подготовка конфигурации Prometheus
Создайте файл с именем prometheus.yml со следующим содержимым.
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Создайте секрет с именем «ППЗУ» с конфигурацией.
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Файлы, перечисленные выше — это базовый файл config Prometheus.
Он задает интервалы и настраивает автоматическое обнаружение в три проекта (prometheus проекта, project1 приложения, приложения project2).
В этом примере автоматически обнаруженных конечных точек будет использоваться по протоколу HTTP без проверки подлинности.
Дополнительные сведения о извлечения данных конечных точек см. в разделе https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config.


## <a name="step-4-prepare-alertmanager-config"></a>Шаг 4. Подготовка конфигурации Alertmanager
Создайте файл с именем alertmanager.yml со следующим содержимым.
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Создайте секрет с именем «ППЗУ — предупреждения» с конфигурацией.
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Файлы, перечисленные выше — это диспетчер предупреждений об изменении файла конфигурации.

> [!NOTE]
> Вы можете проверить два предыдущих шага с `oc get secret -n prometheus-project`

## <a name="step-5-start-prometheus-and-alertmanager"></a>Шаг 5. Запуск Prometheus и Alertmanager
Скачайте [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) шаблон из [openshift или исходный репозиторий](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) и применить его в проекте prometheus
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Файл prometheus standalone.yaml является OpenShift-шаблон, который создает экземпляр Prometheus с oauth-прокси-сервер перед его и Alertmanager экземпляра, также защищены с помощью oauth для прокси-сервера.  В этом шаблоне oauth для прокси-сервер настроен для разрешения любой пользователь, который может «get» пространство имен «prometheus-project» (см. в разделе `-openshift-sar` флаг).

> [!NOTE]
> Можно проверить наличие равно «ППЗУ» StatefulSet *ТРЕБУЕМОГО* и *текущей* число реплик с `oc get statefulset -n prometheus-project` команды.
> Вы также можете проверить все ресурсы в проект с `oc get all -n prometheus-project`.

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>Шаг 6. Добавьте разрешения, позволяющие обнаружение служб
Создайте prometheus sdrole.yml со следующим содержимым.
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Примените шаблон для всех проектов, где вы хотите разрешить обнаружение службы.
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Если также требуется Prometheus, чтобы иметь возможность собирать метрики от самого себя, не забывайте применить разрешения в проекте prometheus слишком.

> [!NOTE]
> Чтобы узнать, если роли и RoleBinding были созданы правильно с `oc get role` и `oc get rolebinding` соответственно команды

## <a name="optional-deploy-example-application"></a>Необязательно: Развертывание примера приложения
Все работает, но нет источников метрик. Перейдите на URL-адрес для Prometheus (https://prom-prometheus-project.apps. *случайный идентификатор*. *регион*.azmosa.io/), который можно найти, выполнив следующую команду.
```
oc get route prom -n prometheus-project
```
Помните, в качестве префикса имени узла с https://.

**Состояние > обнаружение службы** странице будет отображаться 0/0 active целевых объектов.

Чтобы развернуть пример приложения, который предоставляет основные python метрики под /metrics конечной точкой, выполните следующие команды.
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Новые приложения будут отображаться как допустимые цели, на странице обнаружения службы в пределах 30 с, после развертывания. Дополнительные сведения можно получить **состояние > целевых объектов** страницы.

> [!NOTE]
> Для каждого успешно найдены целевого объекта Prometheus добавляет datapoint в метрику «вверх». Нажмите кнопку **Prometheus** в левом верхнем углу, а затем введите «вверх» в качестве выражением и выберите пункт **Execute**.

## <a name="next-steps"></a>Дальнейшие действия

Можно добавить пользовательское инструментирование Prometheus в приложения.

Prometheus клиентская библиотека, которая упрощает подготовку Prometheus метрики, готов для различных языков программирования.

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Перейти https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
