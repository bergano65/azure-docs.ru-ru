---
title: Развернуть отдельный экземпляр Prometheus в кластере Azure Red Hat OpenShift | Документация Майкрософт
description: Создайте экземпляр Prometheus в кластере Azure Red Hat OpenShift для отслеживания метрик приложения.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, ато, openshift, метрики, red hat
ms.openlocfilehash: a9748932a72106413677b21fe0efd1f69fb02e47
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827017"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Развернуть отдельный экземпляр Prometheus в кластере Azure Red Hat OpenShift

В этой статье описывается настройка Prometheus автономный экземпляр, использует обнаружение службы в кластере Azure Red Hat OpenShift.

> [!NOTE]
> Административный доступ клиента к кластеру Azure Red Hat OpenShift не требуется.

Настройка целевой объект.

- Один проект (prometheus проект), который содержит Prometheus и Alertmanager.
- Два проекта (project1 приложения и приложения project2), которые содержат приложения для мониторинга.

Вы подготовитесь несколько Prometheus файлов конфигурации локально. Создайте новую папку для их хранения. Файлы конфигурации хранятся в кластере как секреты, в случае, если секретных маркеров позже добавляются к кластеру.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Войдите в кластер, используя средство OC

1. Откройте браузер и перейдите к веб-консоль кластера (https://openshift. *случайный идентификатор*. *регион*. azmosa.io).
2. Выполните вход с использованием учетных данных Azure.
3. Выберите свое имя пользователя в правом верхнем углу, а затем выберите **команды Login копирования**.
4. Вставьте свое имя пользователя в окне терминала, который будет использоваться.

> [!NOTE]
> Чтобы увидеть, если вы вошли в правильный кластер, запустите `oc whoami -c` команды.

## <a name="prepare-the-projects"></a>Подготовка проектов

Для создания проектов, выполните следующие команды:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Вы можете использовать `-n` или `--namespace` параметр или выберите активный проект, выполнив `oc project` команды.

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
Создайте секрет с именем ППЗУ, введя следующую конфигурацию:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

Файл prometheus.yml — это базовый файл конфигурации Prometheus. Он задает интервалы и настраивает автоматическое обнаружение в три проекта (prometheus проекта, project1 приложения, приложения project2). В предыдущем файле конфигурации конечные точки автоматически обнаружен полученное по протоколу HTTP без проверки подлинности.

Дополнительные сведения о извлечения данных конечных точек см. в разделе [Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>Подготовка файла config Alertmanager
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
Создайте секрет с именем ППЗУ оповещения, введя следующую конфигурацию:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml является диспетчер предупреждений об изменении файла конфигурации.

> [!NOTE]
> Чтобы проверить два предыдущих шага, запустите `oc get secret -n prometheus-project` команды.

## <a name="start-prometheus-and-alertmanager"></a>Запуск Prometheus и Alertmanager
Перейдите к [openshift или исходный репозиторий](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) и загрузите [prometheus standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) шаблона. Примените шаблон prometheus проект, введя следующую конфигурацию:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
Файл prometheus standalone.yaml приведен шаблона OpenShift. Он создает экземпляр Prometheus с oauth прокси, перед его и Alertmanager экземпляра, также защищены с помощью oauth для прокси-сервера. В этом шаблоне oauth для прокси-сервер настроен для разрешения любой пользователь, который может «get» пространство имен проекта prometheus (см. в разделе `-openshift-sar` флаг).

> [!NOTE]
> Чтобы проверить наличие ППЗУ StatefulSet равно ТРЕБУЕМОГО и текущий номер реплики, запустите `oc get statefulset -n prometheus-project` команды. Чтобы проверить все ресурсы в проекте, выполните `oc get all -n prometheus-project` команды.

## <a name="add-permissions-to-allow-service-discovery"></a>Добавьте разрешения, позволяющие обнаружение служб

Создайте файл prometheus sdrole.yml, введя следующее содержимое:
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
Чтобы применить шаблон ко всем проектам, которые вы хотите разрешить обнаружение службы, выполните следующие команды:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Чтобы Prometheus для сбора метрик от самого себя, примените разрешения в проекте prometheus.

> [!NOTE]
> Чтобы убедиться, что роли и RoleBinding были созданы правильно, выполните `oc get role` и `oc get rolebinding` команд.

## <a name="optional-deploy-example-application"></a>Необязательно: Развертывание примера приложения

Все работает, но нет источников метрик. Перейдите на URL-адрес для Prometheus (https://prom-prometheus-project.apps.*случайный идентификатор*.*регион*.azmosa.io/). Его можно найти с помощью следующей команды:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Не забудьте добавить префикс https:// начало имени узла.

**Состояние > обнаружение службы** странице будет отображаться 0/0 active целевых объектов.

Чтобы развернуть пример приложения, который предоставляет базовые метрики Python под /metrics конечной точкой, выполните следующие команды:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Новые приложения должно быть допустимые цели, на странице обнаружения службы в течение 30 секунд после развертывания.

Для получения дополнительных сведений выберите **состояние** > **целевых объектов**.

> [!NOTE]
> Для каждого целевого объекта успешно найдены Prometheus добавляет точку данных в актуальном метрики. Выберите **Prometheus** в левом верхнем углу введите **вверх** как выражение, а затем выберите **Execute**.

## <a name="next-steps"></a>Следующие шаги

Можно добавить пользовательское инструментирование Prometheus в приложения. Prometheus клиентская библиотека, упрощающая Prometheus метрики подготовки, готов для различных языков программирования.

Дополнительные сведения см. следующие библиотеки GitHub:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [GO](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
