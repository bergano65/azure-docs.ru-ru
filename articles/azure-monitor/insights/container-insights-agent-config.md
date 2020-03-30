---
title: Наконфигурните azure Monitor для сбора данных агентов контейнеров (ru) Документы Майкрософт
description: В этой статье описывается, как можно настроить Azure Monitor для агента контейнеров для управления сбором переменных stdout/stderr и переменных среды.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933022"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Настройка сбора данных агента для Azure Monitor для контейнеров

Azure Monitor для контейнеров собирает стдауты, стердеры и экологические переменные из контейнерных рабочих нагрузок, развернутых в управляемых кластерах Kubernetes, от контейнерного агента. Вы можете настроить настройки сбора данных агента, создав пользовательские Kubernetes ConfigMaps для управления этим опытом. 

В этой статье показано, как создать ConfigMap и настроить сбор данных на основе ваших требований.

>[!NOTE]
>Для Azure Red Hat OpenShift файл шаблона ConfigMap создается в пространстве имен *openshift-azure-logging.* 
>

## <a name="configmap-file-settings-overview"></a>Обзор настроек файлов ConfigMap

Шаблон ConfigMap файл при условии, что позволяет легко отобразить его с настройками без создания его с нуля. Перед запуском необходимо ознакомиться с документацией Kubernetes о [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) и ознакомиться с тем, как создавать, настраивать и развертывать ConfigMaps. Это позволит фильтровать stderr и stdout на одно пространство имен или по всему кластеру, а также переменные среды для любого контейнера, работающего по всем стручкам/узлам в кластере.

>[!IMPORTANT]
>Минимальная версия агента поддерживается для сбора stdout, stderr, и экологические переменные из контейнерных рабочих нагрузок ciprod06142019 или позже. To verify your agent version, from the **Node** tab select a node, and in the properties pane note value of the **Agent Image Tag** property. Для получения дополнительной информации о версиях агента и [agent release notes](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)о том, что включено в каждый релиз, см.

### <a name="data-collection-settings"></a>Настройки сбора данных

Ниже приведены параметры, которые могут быть настроены для управления сбором данных.

|Ключ |Тип данных |Значение |Описание |
|----|----------|------|------------|
|`schema-version` |Строка (дело чувствительное) |Версия 1 |Это версия схемы, используемая агентом при разборе этой ConfigMap. В настоящее время поддерживается схема-версия v1. Изменение этого значения не поддерживается и будет отклонено при оценке ConfigMap.|
|`config-version` |Строка | | Поддерживает возможность отслеживать версию этого конфигурационного файла в вашей системе управления исходным источником/репозитории. Максимально допустимых символов 10, а все остальные символы усечены. |
|`[log_collection_settings.stdout] enabled =` |Логическое | true или false | Это управление, если stdout контейнера журнал сбора включен. При установке `true` и отсутствии пространства имен исключаются`log_collection_settings.stdout.exclude_namespaces` для сбора журналов stdout (установка ниже), журналы stdout будут собраны из всех контейнеров во всех стручках/узлах в кластере. Если не указано в ConfigMaps, значение по умолчанию `enabled = true`находится . |
|`[log_collection_settings.stdout] exclude_namespaces =`|Строка | Блок, разделенный запятой |Массив областей имен Kubernetes, для которых не будут собраны журналы stdout. Эта настройка эффективна только в том случае, если `log_collection_settings.stdout.enabled` `true`установлена. Если не указано в ConfigMap, значение по умолчанию `exclude_namespaces = ["kube-system"]`находится .|
|`[log_collection_settings.stderr] enabled =` |Логическое | true или false |Это управление, если stderr контейнер агнетатии сбора включен. При установке `true` и отсутствии именных пространств исключаются для сбора журналов stdout (настройка),`log_collection_settings.stderr.exclude_namespaces` журналы stderr будут собраны из всех контейнеров во всех стручках/узлах кластера. Если не указано в ConfigMaps, значение по умолчанию `enabled = true`находится . |
|`[log_collection_settings.stderr] exclude_namespaces =` |Строка |Блок, разделенный запятой |Массив областей имен Kubernetes, для которых журналы stderr не будут собраны. Эта настройка эффективна только в том случае, если `log_collection_settings.stdout.enabled` `true`установлена. Если не указано в ConfigMap, значение по умолчанию `exclude_namespaces = ["kube-system"]`находится . |
| `[log_collection_settings.env_var] enabled =` |Логическое | true или false | Эта настройка управляет собранием переменных параметров среды во `enabled = true` всех стручках/узлах в кластере и по умолчанию, когда не указано в ConfigMaps. Если набор переменных среды включен глобально, можно отключить его для определенного `AZMON_COLLECT_ENV` контейнера, установив переменную среды на **False** либо с настройкой Dockerfile, либо в [файле конфигурации для Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) под **разделом env:** Если сбор переменных среды глобально отключен, то вы не можете включить сбор для конкретного контейнера (т.е. единственный переопределение, которое может быть применено на уровне контейнера, это отключить сбор, когда он уже включен во всем мире.). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Логическое | true или false | Эта настройка управляет обогащением контейнеров для заполнения значений свойства имени и изображения для каждой записи журнала, записанной на таблицу ContainerLog для всех журналов контейнеров в кластере. Он по `enabled = false` умолчанию, когда не указаны в ConfigMap. |

ConfigMaps является глобальным списком, и к агенту может быть применен только один ConfigMap. Вы не можете иметь другой ConfigMaps отменяя коллекции.

## <a name="configure-and-deploy-configmaps"></a>Настройка и развертывание ConfigMaps

Выполните следующие шаги для настройки и развертывания файла конфигурации ConfigMap в кластере.

1. [Загрузите](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) шаблон файла ConfigMap yaml и сохраните его в виде контейнера-azm-ms-agentconfig.yaml. 

   >[!NOTE]
   >Этот шаг не требуется при работе с Azure Red Hat OpenShift, так как шаблон ConfigMap уже существует в кластере.

2. Отредите файл ConfigMap yaml с настройками для сбора стдутов, стердереров и/или экологических переменных. Если вы редактируете файл ConfigMap yaml для Azure `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` Red Hat OpenShift, сначала запустите команду, чтобы открыть файл в текстовом редакторе.

    - Чтобы исключить определенные области имен для коллекции журналов stdout, `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`вы настраиваете ключ/значение на следующий пример: .
    
    - Чтобы отключить сбор переменных среды для конкретного `[log_collection_settings.env_var] enabled = true` контейнера, установите ключ/значение для включения сбора переменных в глобальном масштабе, а затем выполните шаги [здесь,](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) чтобы завершить конфигурацию для конкретного контейнера.
    
    - Чтобы отключить кластерную коллекцию журналов stderr, вы настраиваете ключ/значение на следующий пример: `[log_collection_settings.stderr] enabled = false`.

3. Для кластеров, помимо Azure Red Hat OpenShift, создайте ConfigMap, запустив следующую команду kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` на кластерах, помимо Azure Red Hat OpenShift. 
    
    Например, `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Для Azure Red Hat OpenShift сохраните изменения в редакторе.

Изменение конфигурации может занять несколько минут, прежде чем вступит в силу, и все стручки omsagent в кластере будут перезастраны. Перезагрузка является перезапуском для всех стручков omsagent, не все перезагрузки в то же время. Когда перезагрузка закончена, отображается сообщение, аналогичное следующему и включает `configmap "container-azm-ms-agentconfig" created`результат: .

## <a name="verify-configuration"></a>Проверка конфигурации

Для проверки конфигурации, которая была успешно применена к кластеру, помимо Azure Red Hat `kubectl logs omsagent-fdf58 -n=kube-system`OpenShift, используйте следующую команду для просмотра журналов из стручка агента: При наличии ошибок конфигурации из стручков omsagent на выходе будут отображаться ошибки, аналогичные следующим:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Ошибки, связанные с изменением конфигурации, также доступны для просмотра. Доступны следующие опции для выполнения дополнительных неполадок изменений конфигурации:

- Из журналов стручка агента `kubectl logs` с использованием той же команды. 

    >[!NOTE]
    >Эта команда не применима к кластеру Azure Red Hat OpenShift.
    > 

- Из живых журналов. В живых журналах показаны ошибки, аналогичные следующим:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Из таблицы **KubeMonAgentEvents** в рабочем пространстве журнала Analytics. Данные отправляются каждый час с серьезностью *ошибки* для ошибок конфигурации. Если нет ошибок, запись в таблице будет иметь данные с серьезностью *Информация*, которая не сообщает ошибок. Свойство **Тегов** содержит больше информации о капсуле и идентификаторе контейнера, на котором произошла ошибка, а также о первом возникновении, последнем возникновении и подсчете в последний час.

- С помощью Azure Red Hat OpenShift проверьте журналы omsagent, исключив таблицу **ContainerLog,** чтобы проверить, включена ли коллекция журналов openshift-azure-logging.

После исправления ошибки (ы) в ConfigMap на кластерах, помимо Azure Red Hat OpenShift, сохраните `kubectl apply -f <configmap_yaml_file.yaml`файл yaml и примените обновленные ConfigMaps, запустив команду: . Для Azure Red Hat OpenShift отредкии и сохранения обновленных ConfigMaps, запустив команду:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Применение обновленного ConfigMap

Если вы уже развернули ConfigMap в кластерах, помимо Azure Red Hat OpenShift, и вы хотите обновить его с новой конфигурацией, вы можете отменить `kubectl apply -f <configmap_yaml_file.yaml`файл ConfigMap, который вы ранее использовали, а затем применить с помощью той же команды, что и раньше, Для Azure Red Hat OpenShift отредкии и сохранения обновленных ConfigMaps, запустив команду:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Изменение конфигурации может занять несколько минут, прежде чем вступит в силу, и все стручки omsagent в кластере будут перезастраны. Перезагрузка является перезапуском для всех стручков omsagent, не все перезагрузки в то же время. Когда перезагрузка закончена, отображается сообщение, аналогичное следующему и включает `configmap "container-azm-ms-agentconfig" updated`результат: .

## <a name="verifying-schema-version"></a>Проверка версии схемы

Поддерживаемые версии схемы конфигурации доступны в виде аннотации стручка (схема-версии) на omsagent pod. Вы можете увидеть их со следующей командой kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

Выход будет отображаться по аналогии со схемой аннотации:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Дальнейшие действия

- Azure Monitor для контейнеров не включает заранее определенный набор оповещений. Просмотрите [оповещения о производительности Create с помощью Azure Monitor для контейнеров,](container-insights-alerts.md) чтобы узнать, как создавать рекомендуемые оповещения для высокого процессора и использования памяти для поддержки DevOps или операционных процессов и процедур.

- Благодаря мониторингу, позволяющим собирать использование akS и ресурсов вашего AKS или гибридного кластера и рабочих нагрузок, работающих на них, [научитесь использовать](container-insights-analyze.md) Azure Monitor для контейнеров.

- Просмотр [примеров запросов журналов](container-insights-log-search.md#search-logs-to-analyze-data) для просмотра заранее определенных запросов и примеров для оценки или настройки для оповещения, визуализации или анализа кластеров.
