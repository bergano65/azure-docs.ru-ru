---
title: Понять, как работает средство произвольную миграцию для оповещений Azure Monitor
description: Понять, как работает средство миграции оповещения и устранения неполадок.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295529"
---
# <a name="understand-how-the-migration-tool-works"></a>Понять, как работает средство миграции

Как [было объявлено ранее](monitoring-classic-retirement.md), классических оповещений в Azure Monitor, прекращается по 31 августа 2019 г. (был изначально 30 июня 2019 г.). Средства миграции доступен на портале Azure клиентам, кто использовать классические правила генерации оповещений, которые хотят активировать миграции сами.

В этой статье объясняется, как работает средство произвольную миграцию. Здесь также описываются способы устранения некоторых распространенных проблем.

> [!NOTE]
> Даты прекращения использования для переноса классических оповещений было из-за задержки в развертывание средства миграции [расширить до 31 августа 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) с изначально было объявлено даты 30 июня 2019 г.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Классические правила генерации оповещений, которые не будут перенесены

> [!IMPORTANT]
> Оповещения журнала действий (включая оповещения о работоспособности службы) и оповещения журнала в процессе миграции не затрагиваются. Миграции применяется только к классической правила генерации оповещений, описанные [здесь](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Несмотря на то, что средство можно перенести почти все [классические правила генерации оповещений](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), существуют некоторые исключения. Следующие правила генерации оповещений переноситься не будут, с помощью средства (или во время автоматического переноса, начиная с сентября 2019 г.):

- Классические правила генерации оповещений на основе метрик гостевой виртуальной машины (Windows и Linux). См. в разделе [рекомендации для повторного создания такого правила генерации оповещений в новых оповещениях метрик](#guest-metrics-on-virtual-machines) далее в этой статье.
- Классические правила генерации оповещений на основе метрик классического хранилища. См. в разделе [руководство по наблюдению за учетные записи хранения классической](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Классические правила генерации оповещений на некоторые метрики учетной записи хранения. См. в разделе [сведения](#storage-account-metrics) далее в этой статье.
- Классические правила генерации оповещений на некоторые показатели Cosmos DB. Сведения будут добавляться в будущем обновлении.

Если ваша подписка имеет такие классической правила, их необходимо перенести вручную. Так как мы не можем дать автоматической миграции, любые существующие, классические оповещения метрик из этих типов будет продолжать работать до июня 2020 г. Это расширение дает вам время, чтобы переход на новые оповещения. Тем не менее новые классического оповещения могут создаваться после августа 2019.

> [!NOTE]
> Помимо приведенных выше исключений, если классические правила генерации оповещения являются недопустимыми т. е. они находятся в [устаревшие метрики](#classic-alert-rules-on-deprecated-metrics) или ресурсы, которые были удалены, они не перемещаются во время миграции добровольное. В случае автоматического переноса будут удалены все такие недопустимые классической правила генерации оповещений.

### <a name="guest-metrics-on-virtual-machines"></a>Метрики гостевой виртуальной машины на виртуальных машинах

Перед созданием нового оповещения на основе метрик для метрик гостевой виртуальной машины, метрики гостевой виртуальной машины должны отправляться в хранилище пользовательских метрик Azure Monitor. Выполните эти инструкции, чтобы включить в параметры диагностики в качестве приемника Azure Monitor.

- [Включение метрик гостевой виртуальной машины для виртуальных машин Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Включение метрик гостевой виртуальной машины для виртуальных машин Linux](collect-custom-metrics-linux-telegraf.md)

После эти шаги будут выполнены, можно создать новые оповещения метрик на метрики гостевой виртуальной машины. И после создания нового оповещения на основе метрик, можно удалить классические оповещения.

### <a name="storage-account-metrics"></a>Метрики учетной записи хранения

За исключением оповещения на основе этих метрик можно перенести все классические оповещения об учетных записях хранения:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

На основе классического оповещения, правила на основе метрик процента, должны быть перенесены [сопоставление между метрик хранилища старые и новые](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Пороговые значения, необходимо соответствующим образом изменить, так как новый доступных метрик является абсолютным.

Классические правила генерации оповещений на AnonymousThrottlingError, SASThrottlingError и ThrottlingError должен быть разделен на две новые оповещения, так как не объединенный показатель, который предоставляет те же функциональные возможности. Пороговые значения, необходимо соответствующим образом адаптировать.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Классические правила генерации оповещений на устаревшие метрики

Это классический правила генерации оповещений на основе метрик, которые раньше поддерживались, но в конечном итоге стали нерекомендуемыми. Небольшой процент от клиента может иметь недопустимые классические правила генерации оповещений на такие показатели. Поскольку эти правила являются недействительными, они переноситься не будут.

| Тип ресурса| Устаревшие metric(s) |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, регулирования, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Как создаются эквивалентные новые правила генерации оповещений и группы действий

Средство миграции преобразует классические правила генерации оповещений в эквивалентные новые правила генерации оповещений и группы действий. Для большинства классической правил генерации оповещений, эквивалентные новые правила генерации оповещений находятся на одной и той же метрики с теми же свойствами например `windowSize` и `aggregationType`. Однако существуют некоторые классического оповещения, правила на основе метрик, хотя бы одна метрика различных, эквивалентных в новой системе. Если не указано в разделе ниже, для переноса классических оповещений применяются следующие принципы:

- **Частота**: Определяет, как часто правило генерации оповещений классической или новой проверяет условие. `frequency` В классическом правила генерации оповещений не настраивается пользователем и всегда имело значение 5 минут для всех типов ресурсов, за исключением компонентов Application Insights, для которых было 1 мин. Поэтому частота эквивалентное правил задается значение 5 мин. и 1 мин соответственно.
- **Тип статистической обработки**: Определяет, как метрики агрегируются поверх окна интерес. `aggregationType` Также является одинаковым в разных классических оповещений и новые предупреждения для большинства метрик. В некоторых случаях, так как метрики отличается от классических оповещений и новые предупреждения, эквивалентные `aggregationType` или `primary Aggregation Type` определенный для метрики.
- **Единицы**: Свойство метрики, для которой создается оповещение. Некоторые эквивалентные метрики имеют различные единицы. Соответствующим образом, при необходимости скорректировать пороговое значение. Например если первоначальные метрика имеет секунды в виде единиц, но эквивалентные новые метрики есть миллисекунды как единицы, исходного пороговое значение умножается на 1000, чтобы обеспечить такое же поведение.
- **Размер окна**: Определяет окно, над какой показатель данные агрегируются для сравнения с пороговым значением. Для стандартных `windowSize` значений, таких как 5 минут, 15mins, 30 минут и, 1 час, 3 часа, 6 часов, 12 часов, 1 день нет изменений для эквивалентных новое правило генерации оповещений. Для других значений, ближайший `windowSize` выбирается для использования. Для большинства клиентов никак не отражается вместе с этим изменением. Для небольшой процент клиентов может возникнуть необходимость в настроить пороговое значение для точного аналогичных действий.

В следующих разделах мы подробно рассмотрим метрики, которые имеют разные, эквивалентных метрики в новой системе. Отсутствует какая-либо метрика, который одинаков для классических и новых правил генерации оповещений. Вы найдете список метрик, поддерживаемых в новой системе [здесь](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Для служб учетной записи хранилища, например больших двоичных объектов, таблиц, файлов и очередей следующие метрики сопоставляются с эквивалентное метрики, как показано ниже:

| Метрики в классических оповещений | Эквивалентное метрики в новые оповещения | Комментарии|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Метрики транзакций с измерениями «ResponseType» = «AuthorizationError» и «Проверка подлинности» = «Anonymous»| |
| AnonymousClientOtherError | Метрики транзакций с измерениями «ResponseType» = «ClientOtherError» и «Проверка подлинности» = «Anonymous» | |
| AnonymousClientTimeOutError| Метрики транзакций с измерениями «ResponseType» = «ClientTimeOutError» и «Проверка подлинности» = «Anonymous» | |
| AnonymousNetworkError | Метрики транзакций с измерениями «ResponseType» = «NetworkError» и «Проверка подлинности» = «Anonymous» | |
| AnonymousServerOtherError | Метрики транзакций с измерениями «ResponseType» = «ServerOtherError» и «Проверка подлинности» = «Anonymous» | |
| AnonymousServerTimeOutError | Метрики транзакций с измерениями «ResponseType» = «ServerTimeOutError» и «Проверка подлинности» = «Anonymous» | |
| AnonymousSuccess | Метрики транзакций с измерениями «ResponseType» = «Success» и «Проверка подлинности» = «Anonymous» | |
| AuthorizationError | Метрики транзакций с измерениями «ResponseType» = «AuthorizationError» | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacity | BlobCapacity | Используйте `aggregationType` «среднее» вместо «last». Метрика применяется только к службам BLOB-объектов |
| ClientOtherError | Метрики транзакций с измерениями «ResponseType» = «ClientOtherError»  | |
| ClientTimeoutError | Метрики транзакций с измерениями «ResponseType» = «ClientTimeOutError.» | |
| ContainerCount | ContainerCount | Используйте `aggregationType` «среднее» вместо «last». Метрика применяется только к службам BLOB-объектов |
| NetworkError | Метрики транзакций с измерениями «ResponseType» = «NetworkError» | |
| ObjectCount | BlobCount| Используйте `aggregationType` «среднее» вместо «last». Метрика применяется только к службам BLOB-объектов |
| SASAuthorizationError | Метрики транзакций с измерениями «ResponseType» = «AuthorizationError» и «Проверка подлинности» = «SAS» | |
| SASClientOtherError | Метрики транзакций с измерениями «ResponseType» = «ClientOtherError» и «Проверка подлинности» = «SAS» | |
| SASClientTimeOutError | Transactions metric with dimensions "ResponseType"="ClientTimeOutError" and "Authentication" = "SAS" | |
| SASNetworkError | Метрики транзакций с измерениями «ResponseType» = «NetworkError» и «Проверка подлинности» = «SAS» | |
| SASServerOtherError | Метрики транзакций с измерениями «ResponseType» = «ServerOtherError» и «Проверка подлинности» = «SAS» | |
| SASServerTimeOutError | Метрики транзакций с измерениями «ResponseType» = «ServerTimeOutError» и «Проверка подлинности» = «SAS» | |
| SASSuccess | Метрики транзакций с измерениями «ResponseType» = «Success» и «Проверка подлинности» = «SAS» | |
| ServerOtherError | Метрики транзакций с измерениями «ResponseType» = «ServerOtherError» | |
| ServerTimeOutError | Метрики транзакций с измерениями «ResponseType» = «ServerTimeOutError»  | |
| Успешно | Метрики транзакций с измерениями «ResponseType» = «Success» | |
| TotalBillableRequests| Транзакции | |
| TotalEgress | Исходящие | |
| TotalIngress | Входящие | |
| TotalRequests | Транзакции | |

### <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components.

Для Application Insights эквивалентные метрики, как показано ниже:

| Метрики в классических оповещений | Эквивалентное метрики в новые оповещения | Комментарии|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| Умножьте исходное пороговое значение 1000 как для классического метрики указываются в секундах и для новых один являются в миллисекундах.  |
| basicExceptionBrowser.count | exceptions/browser|  Используйте `aggregationType` «count» вместо «сумма». |
| basicExceptionServer.count | exceptions/server| Используйте `aggregationType` «count» вместо «сумма».  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Умножьте исходное пороговое значение 1000 как для классического метрики указываются в секундах и для новых один являются в миллисекундах.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Умножьте исходное пороговое значение 1000 как для классического метрики указываются в секундах и для новых один являются в миллисекундах. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Умножьте исходное пороговое значение 1000 как для классического метрики указываются в секундах и для новых один являются в миллисекундах.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Умножьте исходное пороговое значение 1000 как для классического метрики указываются в секундах и для новых один являются в миллисекундах.  |
| clientPerformance.total.value | browserTimings/totalDuration| Умножьте исходное пороговое значение 1000 как для классического метрики указываются в секундах и для новых один являются в миллисекундах.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| Пороговое значение, необходимо соответствующим образом изменить, так как был первоначальные метрика во всех ядрах и новую метрику нормализуется одному ядру. Средство миграции не изменяет пороговые значения.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| Request.Duration | requests/duration| Умножьте исходное пороговое значение 1000 как для классического метрики указываются в секундах и для новых один являются в миллисекундах.  |
| Request.Rate | запросы и скорость|   |
| requestFailed.count | requests/failed| Используйте `aggregationType` «count» вместо «сумма».   |
| View.Count | pageViews/count| Используйте `aggregationType` «count» вместо «сумма».   |

### <a name="how-equivalent-action-groups-are-created"></a>Как создаются эквивалентные действия группы

Классическое оповещение по электронной почте, веб-перехватчика, содержащая правила логики приложения и runbook действия, привязаны к оповещения правило, сам. Новые правила генерации оповещений используют группы действий, которые можно использовать в нескольких правил генерации оповещений. Средство миграции создает одной группы действий для действия независимо от того, сколько правила генерации оповещений при использовании действия. Группы действий, созданных с помощью средства миграции используйте формат имени «Migrated_AG *».

## <a name="rollout-phases"></a>Этапы развертывания

Средство миграции станет доступен на этапах для клиентов, использующих классические правила генерации оповещений. Владельцы подписки будут получать сообщение электронной почты, когда подписка будет готова для переноса с помощью средства.

> [!NOTE]
> Так как средство будет развертываться на этапах, вы можете увидеть, что некоторые из ваших подписок еще не готова для переноса на ранних стадиях.

В настоящее время большая часть подписки помечаются как готов к миграции. По-прежнему не готовы к миграции будут только те подписки, которые имеют классических оповещений от следующих типов ресурсов.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.Insights/Components.

## <a name="who-can-trigger-the-migration"></a>Кто может активировать миграции?

Любой пользователь, который имеет встроенную роль Monitoring Contributor на уровне подписки можно активировать миграции. Пользователи, имеющие пользовательскую роль со следующими разрешениями можно также активировать миграции:

- */чтение
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> Помимо выше разрешений, подписке Кроме того должен быть зарегистрирован с поставщиком ресурсов Microsoft.AlertsManagement. Это необходимо для успешного переноса аномалий сбоя оповещений в Application Insights. 

## <a name="common-problems-and-remedies"></a>Распространенные проблемы и способы устранения

После того как вы [активировать миграции](alerts-using-migration-tool.md), вы получите по электронной почте на адреса, указанный для уведомления о том, что миграция завершена, или если от вас требуется никаких действий. В этом разделе описываются некоторые общие проблемы и способы их устранения.

### <a name="validation-failed"></a>Сбой проверки

Из-за некоторых последние изменения в классическом правила генерации оповещений в подписке невозможно перенести подписку. Эта проблема является временным. Миграцию можно перезапустить после состояния миграции перемещает **готов к миграции** через несколько дней.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Политики или область блокировки, мы не можем перенос правил

В процессе миграции будут созданы новые оповещения метрик и группы действий, а затем будут удалены классические правила генерации оповещений. Однако есть область действия или политики блокировки препятствует созданию ресурсов. В зависимости от политики или область блокировки некоторые или все правила не могут быть перенесены. Эту проблему можно решить путем временное удаление данной политике или блокировка области и активации миграцию снова.

## <a name="next-steps"></a>Дальнейшие действия

- [Как использовать средство миграции](alerts-using-migration-tool.md)
- [Подготовка к миграции](alerts-prepare-migration.md)
