---
title: Развертывание автономного экземпляра Prometheus в кластере Azure Red Hat OpenShift | Документация Майкрософт
description: Создайте экземпляр Prometheus в кластере Azure Red Hat OpenShift, чтобы отслеживать метрики приложения.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, АТО, openshift, метрики, Red Hat
ms.openlocfilehash: f81a993caa31578e689fb3a90108f3cf0ca81fc2
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875138"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Развертывание автономного экземпляра Prometheus в кластере Azure Red Hat OpenShift

В этой статье описывается, как настроить автономный экземпляр Prometheus, использующий обнаружение службы в кластере Azure Red Hat OpenShift.

> [!NOTE]
> Доступ администратора клиента к кластеру Azure Red Hat OpenShift не требуется.

Целевая установка:

- Один проект (Prometheus-Project), который содержит Prometheus и Алертманажер.
- Два проекта (App-проект1 и App-Project2), которые содержат приложения для отслеживания.

Некоторые файлы конфигурации Prometheus будут подготавливаться локально. Создайте новую папку для хранения. Файлы конфигурации хранятся в кластере как секреты, в случае если секретные маркеры добавляются позже в кластер.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Вход в кластер с помощью средства OC

1. Откройте веб-браузер и перейдите в веб-консоль кластера (https://openshift. *случайный идентификатор*. *Region*. azmosa.IO).
2. Выполните вход с использованием учетных данных Azure.
3. Выберите свое имя пользователя в правом верхнем углу и нажмите кнопку **Копировать имя входа**.
4. Вставьте имя пользователя в терминал, который вы будете использовать.

> [!NOTE]
> Чтобы проверить, вошли ли вы в правильный кластер, выполните `oc whoami -c` команду.

## <a name="prepare-the-projects"></a>Подготовка проектов

Чтобы создать проекты, выполните следующие команды:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Можно либо использовать `-n` параметр или `--namespace` , либо выбрать `oc project` активный проект, выполнив команду.

## <a name="prepare-the-prometheus-configuration-file"></a>Подготовка файла конфигурации Prometheus
Создайте файл Prometheus. yml, введя следующее содержимое:
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
Создайте секрет с именем Пром, введя следующую конфигурацию:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Файл Prometheus. yml является базовым файлом конфигурации Prometheus. Он задает интервалы и настраивает автоматическое обнаружение в трех проектах (Prometheus-Project, App-проект1, App-Project2). В предыдущем файле конфигурации Обнаруженные конечные точки отправляются по протоколу HTTP без проверки подлинности.

Дополнительные сведения о отходах конечных точек см. в разделе [Prometheus скапе config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Подготовка файла конфигурации Алертманажер
Создайте файл алертманажер. yml, введя следующее содержимое:
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
Создайте секрет с именем Пром-Alerts, введя следующую конфигурацию:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Алертманажер. yml — это файл конфигурации диспетчера предупреждений.

> [!NOTE]
> Чтобы проверить два предыдущих шага, выполните `oc get secret -n prometheus-project` команду.

## <a name="start-prometheus-and-alertmanager"></a>Запуск Prometheus и Алертманажер
Перейдите в [репозиторий openshift/Origin](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) и скачайте [шаблон Prometheus-standalone.](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) YAML. Примените шаблон к Prometheus-Project, введя следующую конфигурацию:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Файл Prometheus-standalone. YAML является шаблоном OpenShift. Он создаст экземпляр Prometheus с OAuth-proxy перед ним и экземпляром Алертманажер, также защищенным с помощью OAuth-proxy. В этом шаблоне OAuth-proxy настроен на разрешение любому пользователю, который может «получить» пространство имен Prometheus-Project (см `-openshift-sar` . флаг).

> [!NOTE]
> Чтобы проверить, равны ли Пром StatefulSet и текущие реплики числа, выполните `oc get statefulset -n prometheus-project` команду. Чтобы проверить все ресурсы в проекте, выполните `oc get all -n prometheus-project` команду.

## <a name="add-permissions-to-allow-service-discovery"></a>Добавление разрешений для разрешения обнаружения служб

Создайте файл промесеус-сдроле. yml, введя следующее содержимое:
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
Чтобы применить шаблон ко всем проектам, из которых необходимо разрешить обнаружение служб, выполните следующие команды:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Чтобы убедиться, что роль и ролебиндинг созданы правильно, выполните `oc get role` команды и. `oc get rolebinding`

## <a name="optional-deploy-example-application"></a>Дополнительно Развертывание примера приложения

Все работает, но источники метрик отсутствуют. Перейдите на URL-адрес для Prometheus (https://prom-prometheus-project.apps.*случайный идентификатор*.*регион*.azmosa.io/). Его можно найти с помощью следующей команды:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Не забудьте добавить префикс https://в начало имени узла.

На странице " **состояние > обнаружения службы** " отобразится 0/0 активных целевых объектов.

Чтобы развернуть пример приложения, который предоставляет основные метрики Python в конечной точке/Метрикс, выполните следующие команды:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Новые приложения должны отображаться как допустимые целевые объекты на странице Обнаружение службы в течение 30 секунд после развертывания.

Для получения дополнительных сведений выберите **состояние** > **целевые объекты**.

> [!NOTE]
> Для всех успешно бракованных целевых объектов Prometheus добавляет точку данных в метрику вверх. Выберите **Prometheus** в левом верхнем углу, введите в качестве выражения и нажмите кнопку **выполнить**.

## <a name="next-steps"></a>Следующие шаги

В приложения можно добавить настраиваемое инструментирование Prometheus. Клиентская библиотека Prometheus, которая упрощает подготовку метрик Prometheus, готова для разных языков программирования.

Дополнительные сведения см. в следующих библиотеках GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [GO](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
