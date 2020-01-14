---
title: Настройка Azure Monitor для сбора данных агента контейнеров | Документация Майкрософт
description: В этой статье описывается настройка Azure Monitor для агента контейнеров для управления набором журналов stdout/stderr и переменных среды.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933022"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Настройка сбора данных агента для Azure Monitor для контейнеров

Azure Monitor для контейнеров собирает потоки stdout, stderr и переменные среды из рабочих нагрузок контейнера, развернутых в управляемых кластерах Kubernetes из контейнерного агента. Параметры сбора данных агента можно настроить, создав настраиваемый Конфигмапс Kubernetes для управления этим интерфейсом. 

В этой статье показано, как создать ConfigMap и настроить сбор данных в соответствии с вашими требованиями.

>[!NOTE]
>Для Azure Red Hat OpenShift в пространстве имен *OpenShift-Azure-Logging* создается файл шаблона ConfigMap. 
>

## <a name="configmap-file-settings-overview"></a>Общие сведения о параметрах файлов ConfigMap

Предоставляется файл шаблона ConfigMap, который позволяет легко редактировать его с помощью настроек без необходимости создавать его с нуля. Прежде чем начать, ознакомьтесь с документацией по Kubernetes о [конфигмапс](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) и ознакомьтесь с созданием, настройкой и развертыванием конфигмапс. Это позволит вам фильтровать stderr и stdout на пространство имен или во всем кластере, а также переменные среды для любого контейнера, работающего во всех модулях Pod и узлах в кластере.

>[!IMPORTANT]
>Минимальная версия агента, поддерживаемая для получения переменных среды stdout, stderr и Variable из рабочих нагрузок контейнера, — ciprod06142019 или более поздней версии. Чтобы проверить версию агента, на вкладке **узел** выберите узел и в области свойства запишите значение свойства **тег образа агента** . Дополнительные сведения о версиях агентов и о том, что входит в каждый выпуск, см. в разделе [заметки о выпуске агента](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Параметры сбора данных

Ниже приведены параметры, которые можно настроить для управления сбором данных.

|Ключ |Тип данных |Значение |Description |
|----|----------|------|------------|
|`schema-version` |Строка (с учетом регистра) |Версия 1 |Это версия схемы, используемая агентом при анализе этого ConfigMap. Текущая поддерживаемая версия схемы — v1. Изменение этого значения не поддерживается и будет отклонено при вычислении ConfigMap.|
|`config-version` |String | | Поддерживает возможность отслеживания версии этого файла конфигурации в системе управления версиями или в репозитории. Максимально допустимое количество символов равно 10, а все остальные символы усекаются. |
|`[log_collection_settings.stdout] enabled =` |Логическое | true или false | Определяет, включен ли сбор журналов контейнеров stdout. Если задано значение `true` и никакие пространства имен не исключены для сбора журналов stdout (`log_collection_settings.stdout.exclude_namespaces` параметр ниже), журналы stdout будут собираться из всех контейнеров во всех модулях или узлах в кластере. Если не указано в Конфигмапс, значение по умолчанию — `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Массив с разделителями-запятыми |Массив пространств имен Kubernetes, для которых не будут собираться журналы stdout. Этот параметр эффективен, только если для параметра `log_collection_settings.stdout.enabled` задано значение `true`. Если не указано в ConfigMap, значение по умолчанию — `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Логическое | true или false |Определяет, включен ли сбор журналов контейнеров stderr. Если задано значение `true` и пространства имен не исключены для сбора журналов stdout (`log_collection_settings.stderr.exclude_namespaces` параметр), то журналы stderr будут собираться из всех контейнеров во всех модулях или узлах в кластере. Если не указано в Конфигмапс, значение по умолчанию — `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Массив с разделителями-запятыми |Массив пространств имен Kubernetes, для которых не будут собираться журналы stderr. Этот параметр эффективен, только если для параметра `log_collection_settings.stdout.enabled` задано значение `true`. Если не указано в ConfigMap, значение по умолчанию — `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Логическое | true или false | Этот параметр управляет коллекцией переменных среды во всех модулях Pod и узлах в кластере, а значение по умолчанию — `enabled = true`, если не указано в Конфигмапс. Если коллекция переменных среды включена глобально, ее можно отключить для определенного контейнера, задав для `AZMON_COLLECT_ENV` переменной среды значение **false** в параметре Dockerfile или в [файле конфигурации для Pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) в разделе **env:** . Если коллекция переменных среды глобально отключена, то нельзя включить сбор для определенного контейнера (то есть единственным переопределением, которое можно применить на уровне контейнера, является отключение коллекции, если она уже включена глобально). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Логическое | true или false | Этот параметр управляет расширением журнала контейнера для заполнения значений свойств Name и Image для каждой записи журнала, записанной в таблицу Контаинерлог для всех журналов контейнера в кластере. По умолчанию используется `enabled = false`, если не указано в ConfigMap. |

Конфигмапс является глобальным списком, и к нему может быть применен только один ConfigMap. У вас не может быть другой Конфигмапс.

## <a name="configure-and-deploy-configmaps"></a>Настройка и развертывание Конфигмапс

Выполните следующие действия, чтобы настроить и развернуть файл конфигурации ConfigMap в кластере.

1. [Скачайте](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) файл шаблона ConfigMap YAML и сохраните его как Container-АЗМ-MS-ажентконфиг. YAML. 

   >[!NOTE]
   >Этот шаг не требуется при работе с Azure Red Hat OpenShift, так как шаблон ConfigMap уже существует в кластере.

2. Измените файл YAML ConfigMap, используя настройки для получения переменных среды stdout, stderr и (или). Если вы редактируете файл YAML ConfigMap для Azure Red Hat OpenShift, сначала выполните команду `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`, чтобы открыть файл в текстовом редакторе.

    - Чтобы исключить определенные пространства имен для сбора журналов stdout, необходимо настроить ключ или значение, используя следующий пример: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Чтобы отключить коллекцию переменных среды для определенного контейнера, задайте ключ/значение, `[log_collection_settings.env_var] enabled = true` включить глобальную коллекцию переменных, а затем выполните действия, описанные [здесь](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) , чтобы завершить настройку для конкретного контейнера.
    
    - Чтобы отключить кластер для сбора журналов stderr, настройте ключ или значение, используя следующий пример: `[log_collection_settings.stderr] enabled = false`.

3. Для кластеров, отличных от Azure Red Hat OpenShift, создайте ConfigMap, выполнив следующую команду kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` в кластерах, отличных от Azure Red Hat OpenShift. 
    
    Пример: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Для Azure Red Hat OpenShift сохраните изменения в редакторе.

До вступления в силу изменение конфигурации может занять несколько минут, и все omsagent Pod в кластере будут перезапущены. Перезагрузка является пошаговым перезапуском для всех модулей omsagent Pod, а не всех перезапусков одновременно. После завершения перезагрузки отображается сообщение, похожее на следующее и содержащее результат: `configmap "container-azm-ms-agentconfig" created`.

## <a name="verify-configuration"></a>Проверка конфигурации

Чтобы убедиться, что конфигурация была успешно применена к кластеру, отличному от Azure Red Hat OpenShift, выполните следующую команду, чтобы проверить журналы из модуля Pod: `kubectl logs omsagent-fdf58 -n=kube-system`. При наличии ошибок конфигурации из модулей Pod omsagent в выходных данных отобразятся ошибки, аналогичные приведенным ниже.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Ошибки, связанные с применением изменений конфигурации, также доступны для проверки. Для выполнения дополнительных действий по устранению изменений конфигурации можно использовать следующие параметры.

- Из журналов Pod агента с помощью одной и той же команды `kubectl logs`. 

    >[!NOTE]
    >Эта команда неприменима к кластеру Azure Red Hat OpenShift.
    > 

- Из активных журналов. В журналах в реальном времени отображаются ошибки, аналогичные приведенным ниже.

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Из таблицы **кубемонажентевентс** в рабочей области log Analytics. Данные отправляются каждый час с серьезностью *ошибки* конфигурации. Если ошибок нет, запись в таблице будет содержать данные со *сведениями об*уровне серьезности, которые не сообщают об ошибках. Свойство **Tags** содержит дополнительные сведения о Pod и идентификаторе контейнера, в которых произошла ошибка, а также первое вхождение, Последнее повторение и количество за последний час.

- С помощью Azure Red Hat OpenShift Проверьте журналы omsagent, выполнив поиск в таблице **контаинерлог** , чтобы проверить, включено ли ведение журнала OpenShift-Azure-Logging.

После исправления ошибок в ConfigMap для кластеров, отличных от Azure Red Hat OpenShift, сохраните файл YAML и примените обновленную Конфигмапс, выполнив команду `kubectl apply -f <configmap_yaml_file.yaml`. Для Azure Red Hat OpenShift измените и сохраните обновленный Конфигмапс, выполнив команду:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Применение обновленных ConfigMap

Если вы уже развернули ConfigMap в кластерах, отличных от Azure Red Hat OpenShift, и хотите обновить его с помощью более новой конфигурации, вы можете изменить ранее использовавшийся файл ConfigMap, а затем применить его с помощью той же команды, что и ранее, `kubectl apply -f <configmap_yaml_file.yaml`. Для Azure Red Hat OpenShift измените и сохраните обновленный Конфигмапс, выполнив команду:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

До вступления в силу изменение конфигурации может занять несколько минут, и все omsagent Pod в кластере будут перезапущены. Перезагрузка является пошаговым перезапуском для всех модулей omsagent Pod, а не всех перезапусков одновременно. После завершения перезагрузки отображается сообщение, похожее на следующее и содержащее результат: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Проверка версии схемы

Поддерживаемые версии схемы конфигурации доступны в виде заметки к Pod (версии схемы) в модуле omsagent. Их можно увидеть с помощью следующей команды kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Выходные данные будут выглядеть так, как показано ниже, с помощью схемы аннотации — версии:

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

- Azure Monitor для контейнеров не включает предопределенный набор предупреждений. Ознакомьтесь с разработкой [оповещений о производительности с помощью Azure Monitor для контейнеров](container-insights-alerts.md) , чтобы узнать, как создавать Рекомендуемые оповещения для высокой загрузки ЦП и памяти для поддержки DevOps или рабочих процессов и процедур.

- С включенным мониторингом для получения сведений о работоспособности и использовании ресурсов в AKS или гибридном кластере и рабочих нагрузках Вы узнаете, [как использовать](container-insights-analyze.md) Azure Monitor для контейнеров.

- Просмотрите [примеры запросов журналов](container-insights-log-search.md#search-logs-to-analyze-data) , чтобы просмотреть предварительно определенные запросы и примеры для проверки или настройки предупреждений, визуализации или анализа кластеров.
