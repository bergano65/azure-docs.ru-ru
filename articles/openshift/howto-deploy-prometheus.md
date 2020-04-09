---
title: Развертывание экземпляра Prometheus в кластере Azure Red Hat OpenShift
description: Создайте экземпляр Prometheus в кластере Azure Red Hat OpenShift для мониторинга метрик приложения.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, аро, openshift, метрики, красная шляпа
ms.openlocfilehash: 7f22df587f51af735e0ea663e53f6eef14d60692
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886894"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Развертывание автономного экземпляра Prometheus в кластере Azure Red Hat OpenShift

В этой статье описывается, как настроить автономный экземпляр Prometheus, в который используется обнаружение службы в кластере Azure Red Hat OpenShift.

> [!NOTE]
> Доступ клиента к кластеру Azure Red Hat OpenShift не требуется.

Целевая настройка:

- Один проект (прометей-проект), который содержит Prometheus и Alertmanager.
- Два проекта (app-project1 и app-project2), которые содержат приложения для мониторинга.

Вы подготовите некоторые файлы конфигурации Prometheus локально. Создайте новую папку для их хранения. Файлы config хранятся в кластере как секреты, в случае, если секретные токены добавляются позже в кластер.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Войти в кластер с помощью инструмента OC

1. Откройте веб-браузер, а затем перейдите наhttps://openshiftвеб-консоль вашего кластера ( .* случайный id*. *области*.azmosa.io).
2. Выполните вход с использованием учетных данных Azure.
3. Выберите имя пользователя в правом верхнем углу, а затем выберите **команду Copy Login.**
4. Вставьте имя пользователя в терминал, который вы будете использовать.

> [!NOTE]
> Чтобы узнать, вступин ли вы `oc whoami -c` в правильный кластер, запустите команду.

## <a name="prepare-the-projects"></a>Подготовка проектов

Для создания проектов запустите следующие команды:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Можно либо использовать `-n` `--namespace` параметр, либо выбрать активный `oc project` проект, запустив команду.

## <a name="prepare-the-prometheus-configuration-file"></a>Подготовка файла конфигурации Prometheus
Создайте файл prometheus.yml, введя следующее содержимое:
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
Создайте секрет под названием Prom, введя следующую конфигурацию:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Файл prometheus.yml является базовым файлом конфигурации Prometheus. Он устанавливает интервалы и настраивает автоматическое открытие в трех проектах (прометей-проект, приложение-проект1, приложение-проект2). В предыдущем файле конфигурации автоматически открытые конечные точки скребокится по HTTP без проверки подлинности.

Для получения дополнительной информации о соскоб конечных точек, см [Prometheus scape конфигурации](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Подготовьте файл конфигурации Alertmanager
Создайте файл alertmanager.yml, введя следующее содержимое:
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
Создайте секрет под названием Prom-Alerts, введя следующую конфигурацию:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml — это файл конфигурации оповещения менеджера.

> [!NOTE]
> Чтобы проверить два предыдущих `oc get secret -n prometheus-project` шага, запустите команду.

## <a name="start-prometheus-and-alertmanager"></a>Начните Prometheus и Alertmanager
Перейти к [openshift / происхождения репозитория](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) и скачать [prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) шаблон. Примените шаблон к промете-проекту, введя следующую конфигурацию:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Файл prometheus-standalone.yaml является шаблоном OpenShift. Он создаст экземпляр Prometheus с oauth-прокси перед ним и экземпляром Alertmanager, также обеспеченным oauth-прокси. В этом шаблоне настроен прокси-сервер oauth, чтобы позволить любому пользователю, который может `-openshift-sar` «получить» пространство имени прометея-проекта (см. флаг).

> [!NOTE]
> Чтобы проверить, имеет ли выпускной выпускной выпускные вечера `oc get statefulset -n prometheus-project` StatefulSet равные реплики номера DESIRED и CURRENT, запустите команду. Чтобы проверить все ресурсы в `oc get all -n prometheus-project` проекте, запустите команду.

## <a name="add-permissions-to-allow-service-discovery"></a>Добавление разрешений для обнаружения службы

Создайте файл prometheus-sdrole.yml, введя следующее содержимое:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
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
Чтобы применить шаблон ко всем проектам, из которых требуется разрешить обнаружение службы, запустите следующие команды:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Чтобы убедиться, что роль и RoleBinding `oc get role` `oc get rolebinding` были созданы правильно, запустите и команды.

## <a name="optional-deploy-example-application"></a>Дополнительно: Развертывание примера приложения

Все работает, но нет источников метрик. Перейдите на URL-адресhttps://prom-prometheus-project.appsPrometheus ( .* случайный id*. *области*.azmosa.io/). Вы можете найти его, используя следующую команду:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Не забудьте добавить https:// префикс к началу имени хоста.

Страница **"Состояние > службы обнаружения"** будет отображать 0/0 активных целей.

Чтобы развернуть пример приложения, которое предоставляет основные метрики Python под конечную точку /метрики, запустите следующие команды:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Новые приложения должны отображаться в качестве действительных целей на странице Service Discovery в течение 30 секунд после развертывания.

Для получения более подробной информации выберите**цели** **статуса.** > 

> [!NOTE]
> Для каждой успешно выцарапан цели, Prometheus добавляет точку данных в вверх метрики. Выберите **Прометей** в верхнем левом углу, **введите** в качестве выражения, а затем выберите **Выполнить**.

## <a name="next-steps"></a>Дальнейшие действия

Вы можете добавить пользовательские приборы Prometheus в свои приложения. Библиотека Prometheus Client, которая упрощает подготовку метрик Prometheus, готова для различных языков программирования.

Для получения дополнительной информации смотрите следующие библиотеки GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Перейти](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
