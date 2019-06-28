---
title: Настройка Azure Monitor для сбора данных агент контейнеры | Документация Майкрософт
description: В этой статье описывается, как можно настроить Azure Monitor для агента контейнеров для управления stdout и stderr и переменные среды журнала коллекции.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341647"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Настройка сбора данных агент для Azure Monitor для контейнеров

Azure Monitor для контейнеров собирает stdout, stderr и переменные среды из контейнера нагрузок, развернутых в управляемые кластеры Kubernetes, размещенные в службе Azure Kubernetes (AKS) из контейнерных агента. Параметры сбора данных агент можно настроить с помощью пользовательского Kubernetes ConfigMaps для управления этот опыт. В этой статье показано, как создать ConfigMap и настройке сбора данных, на основе требований.

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>Настройте кластер с параметрами коллекции пользовательских данных

Файл шаблона ConfigMap предоставляется, позволяющий легко изменить его вместе с настройками без необходимости создавать его с нуля. Прежде чем начать, изучите в документации по Kubernetes о [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) и узнайте, как создать, настроить и развернуть ConfigMaps. Это позволит выполнять фильтрацию stderr и stdout на пространство имен, или для всего кластера и переменные среды для любого контейнера, запущенного на всех модулей POD/узлах в кластере.

>[!IMPORTANT]
>Минимальная версия агента поддерживается этим средством является microsoft / oms:ciprod06142019 или более поздней версии. 

### <a name="overview-of-configurable-data-collection-settings"></a>Общие сведения о параметрах коллекцию настраиваемых данных

Ниже приведены параметры, которые могут быть настроены для управления сбором данных.

|Ключ |Тип данных |Value |Описание |
|----|----------|------|------------|
|`schema-version` |Строка (с учетом регистра) |Версия 1 |Это версия схемы, используемый агентом при синтаксическом анализе этого ConfigMap. В настоящее время поддерживаются версии схемы — версии 1. Изменение этого значения не поддерживается и при вычислении ConfigMap, будут отклонены.|
|`config-version` |String | | Поддерживает возможность для отслеживания версии этот файл config в репозиторий системы управления версиями системы /. Максимально допустимое число символов, 10, а все остальные символы усекаются. |
|`[log_collection_settings.stdout] enabled =` |Boolean | Значение true или false | Эта подсистема управляет Если включен сбор журналов stdout контейнера. Если задано значение `true` и пространства имен не исключаются для сбора журналов stdout (`log_collection_settings.stdout.exclude_namespaces` параметр ниже), будут собираться журналы stdout из всех контейнеров для всех модулей POD и узлов в кластере. Если не указан в ConfigMaps, значение по умолчанию — `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Массив с разделителями запятыми |Массив пространств имен Kubernetes, для которых stdout журналы не будут собраны. Этот параметр действует, только если `log_collection_settings.stdout.enabled` присваивается `true`. Если не указан в ConfigMap, значение по умолчанию — `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | Значение true или false |Эта подсистема управляет Если включен сбор журналов stderr контейнера. Если задано значение `true` и пространства имен не исключаются для сбора журналов stdout (`log_collection_settings.stderr.exclude_namespaces` параметр), будут собираться журналы stderr из всех контейнеров для всех модулей POD и узлов в кластере. Если не указан в ConfigMaps, значение по умолчанию — `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Массив с разделителями запятыми |Массив пространств имен Kubernetes, для которых stderr не собираются журналы. Этот параметр действует, только если `log_collection_settings.stdout.enabled` присваивается `true`. Если не указан в ConfigMap, значение по умолчанию — `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | Значение true или false | Эта подсистема управляет активна коллекции переменных среды. Если задано значение `false`, переменные среды, собираются для любого контейнера, запущенного на всех модулей POD/узлах в кластере. Если не указан в ConfigMap, значение по умолчанию — `enabled = true`. |

### <a name="configure-and-deploy-configmaps"></a>Настройка и развертывание ConfigMaps

Выполните следующие действия для настройки и развертывания файла конфигурации ConfigMap в кластере.

1. [Скачайте](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) yaml ConfigMap шаблон файла и сохраните его как контейнер azm-ms-agentconfig.yaml.  
1. Измените файл yaml ConfigMap вместе с настройками. 

    - Чтобы исключить определенных пространств имен для сбора журналов stdout, настроить ключ/значение, используя приведенный ниже пример: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    - Чтобы отключить коллекции переменных среды для конкретного контейнера, задайте ключ/значение `[log_collection_settings.env_var] enabled = true` для включения коллекции переменных глобально и затем выполните действия, [здесь](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) для завершения настройки для конкретного контейнера.
    - Чтобы отключить сбор данных журналов stderr на уровне кластера, настроить ключ/значение, используя приведенный ниже пример: `[log_collection_settings.stderr] enabled = false`.

1. Создайте ConfigMap, выполнив следующую команду kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Пример: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Изменение конфигурации может занять несколько минут, чтобы завершить вступят в силу, и все модули omsagent в кластере будет перезапущен. Это последовательное перезагрузки для всех модулей omsagent, перезапустите не все одновременно. После завершения перезапуска отображается сообщение, аналогичную следующей и включает результат: `configmap "container-azm-ms-agentconfig" created`.

Чтобы проверить, удалось применить конфигурацию, используйте следующую команду для просмотра журналов из агента pod: `kubectl logs omsagent-fdf58 -n=kube-system`. При возникновении ошибки конфигурации из модулей osmagent, будут показаны результаты ошибки следующего вида:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Ошибки синтаксического анализа файла, вызывая перезапустить и использовать конфигурацию по умолчанию позволяют omsagent. После исправления ошибок в ConfigMap, сохраните файл yaml и применить обновленные ConfigMaps, выполнив команду: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Применение обновления ConfigMap

Если вы уже развернули ConfigMap для кластера, и вы хотите обновить ее с новой конфигурацией, можно просто изменить файл ConfigMap вы использовали ранее, а затем применить с помощью той же команды, как и раньше, `kubectl apply -f <configmap_yaml_file.yaml`.

Изменение конфигурации может занять несколько минут, чтобы завершить вступят в силу, и все модули omsagent в кластере будет перезапущен. Это последовательное перезагрузки для всех модулей omsagent, перезапустите не все одновременно. После завершения перезапуска отображается сообщение, аналогичную следующей и включает результат: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Проверка версии схемы

Поддерживаемые конфигурации схемы версии доступны как аннотация pod (версий схемы) в omsagent pod. Вы увидите их с помощью следующей команды kubectl. `kubectl describe pod omsagent-fdf58 -n=kube-system`

Будут показаны результаты с версий схемы заметка следующего вида:

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

- Чтобы продолжить изучение способов использования Azure Monitor и отслеживать другие аспекты кластера AKS, ознакомьтесь со статьей [Общие сведения о мониторинге производительности кластера AKS с помощью Azure Monitor для контейнеров (предварительная версия)](container-insights-analyze.md).
- Представление [журнала примеры запросов](container-insights-log-search.md#search-logs-to-analyze-data) предопределенных запросов и примеры для настройки предупреждений, визуализация и анализ кластеров и оценки.