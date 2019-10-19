---
title: Общие сведения о журналах ресурсов Azure | Документация Майкрософт
description: Узнайте о поддерживаемых службах и схеме событий для журналов ресурсов Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 09/20/2019
ms.openlocfilehash: a418e3d1a59379284422d1d24c1457ab61d84a4c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551751"
---
# <a name="azure-resource-logs-overview"></a>Общие сведения о журналах ресурсов Azure
Журналы ресурсов Azure — это [журналы платформ](platform-logs-overview.md) , создаваемые ресурсами Azure, которые описывают их внутреннюю работу. Все журналы ресурсов совместно используют общую схему верхнего уровня с гибкостью для каждой службы, чтобы создавать уникальные свойства для собственных событий.

> [!NOTE]
> Журналы ресурсов ранее назывались журналами диагностики.

## <a name="collecting-resource-logs"></a>Идет сбор журналов ресурсов
Журналы ресурсов автоматически создаются поддерживаемыми ресурсами Azure, но они не собираются, если вы не настроите их с помощью [параметра диагностики](diagnostic-settings.md). Создайте параметр диагностики для каждого ресурса Azure, чтобы перенаправить журналы в следующие места назначения:

| Место назначения | Сценарий |
|:---|:---|:---|
| [Рабочая область Log Analytics](resource-logs-collect-storage.md) | Анализируйте журналы с помощью других данных мониторинга и используйте Azure Monitor функции, такие как запросы журнала и оповещения журналов. |
| [служба хранилища Azure](archive-diagnostic-logs.md) | Архивируйте журналы для аудита или резервного копирования. |
| [Концентратор событий](resource-logs-stream-event-hubs.md) | Потоковая передача журналов в сторонние системы ведения журналов и телеметрии.  |

## <a name="compute-resources"></a>Вычислительные ресурсы
Журналы ресурсов отличаются от журналов на уровне гостевой ОС в ресурсах вычислений Azure. Для вычислений ресурсов требуется, чтобы агент составил журналы и метрики из гостевой ОС, включая такие данные, как журналы событий, системный журнал и счетчики производительности. Используйте [диагностическое расширение](agents-overview.md#azure-diagnostic-extension) для маршрутизации данных журнала с виртуальных машин Azure и [агента log Analytics](agents-overview.md#log-analytics-agent) для сбора журналов и метрик из виртуальных машин в Azure, в других облаках и в локальной среде в log Analytics рабочей области. Дополнительные сведения см. в разделе [Источники данных мониторинга для Azure Monitor](data-sources.md) .

## <a name="resource-logs-schema"></a>Схема журналов ресурсов
Каждая служба Azure имеет собственную схему, когда журналы ресурсов записываются в одно из назначений, но все они имеют схему верхнего уровня, приведенную в следующей таблице. Ссылки на схему для каждой службы см. в разделе [схемы, зависящие от службы](#service-specific-schemas) . 

| Name | Обязательный/необязательный | Описание |
|---|---|---|
| time | Обязательно для заполнения | Метка времени события (UTC). |
| ResourceId | Обязательно для заполнения | Идентификатор ресурса, создавшего событие. Для служб клиента он имеет формат "/tenants/ИД_клиента/providers/имя_поставщика". |
| tenantId | Требуется для журналов клиента | Идентификатор клиента Active Directory клиента, к которому привязано это событие. Это свойство используется только для журналов уровня клиентов и не отображается в журналах уровня ресурсов. |
| operationName | Обязательно для заполнения | Имя операции, которую представляет это событие. Если событие представляет операцию RBAC, то это имя операции RBAC (например, Microsoft.Storage/storageAccounts/blobServices/blobs/Read). Обычно моделируются в виде операции Resource Manager, даже если они фактически не являются задокументированными операциями Resource Manager (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | Необязательно | Версия API, связанного с операцией, если операция operationName выполнялась с помощью API (например, `http://myservice.windowsazure.net/object?api-version=2016-06-01`). Если для этой операции не существует соответствующего API, то версия представляет версию этой операции, чтобы в дальнейшем изменить свойства, связанные с операцией. |
| category | Обязательно для заполнения | Категория журнала для события. Категория — степень детализации, при которой можно включать или отключать журналы для определенного ресурса. Свойства, которые отображаются в свойствах BLOB-объекта события, одинаковы в пределах определенной категории журнала и типа ресурса. Типичными категориями журнала являются "Аудит", "Операционный", "Выполнение" и "Запрос". |
| resultType | Необязательно | Состояние события. Обычные значения: "Запущен", "Выполняется", "Успешно", "Сбой", "Активно", "Разрешено". |
| resultSignature | Необязательно | Дополнительное состояние события. Если эта операция соответствует вызову REST API, то это код состояния HTTP соответствующего вызова REST. |
| resultDescription | Необязательно | Статическое текстовое описание этой операции, например "Получить файл хранилища". |
| durationMs | Необязательно | Время выполнения операции в миллисекундах. |
| callerIpAddress | Необязательно | IP-адрес вызывающего объекта, если операция соответствует вызову API, который будет приходить с объекта с общедоступным IP-адресом. |
| correlationId | Необязательно | Идентификатор GUID, используемый для формирования набора связанных событий. Как правило, если два события имеют одно и то же значение параметра operationName, но имеют различные состояния (например, "Запущен" и "Успешно"), они совместно используют один и тот же идентификатор корреляции. Идентификатор может также представлять другие связи между событиями. |
| удостоверение | Необязательно | BLOB-объект типа JSON, описывающий идентификацию пользователя или приложения, выполнившего операцию. Обычно он включает авторизацию и утверждения или токен JWT из Active Directory. |
| уровень | Необязательно | Уровень серьезности события. Должен быть одним из следующих значений: "Информационное", "Предупреждение", "Ошибка" или "Критическое". |
| location | Необязательно | Регион ресурса, создавшего событие, например "Восточная часть США" или "Южная Франция" |
| properties | Необязательно | Любые расширенные свойства, связанные с этой конкретной категорией событий. Все пользовательские или уникальные свойства должны быть размещены внутри этой "части B" схемы. |

## <a name="service-specific-schemas"></a>Зависящие от службы схемы
Схема для журналов диагностики ресурсов зависит от типа ресурса, определяемого свойством `resourceId`) и свойств `category`. В следующем списке перечислены все службы Azure, которые поддерживают журналы ресурсов со ссылками на схему службы и конкретной категории, где это возможно.

| Служба | Схемы и документы |
| --- | --- |
| Azure Active Directory | [Общие сведения](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md), [схема журнала аудита](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md) и [схема входа](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Службы Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Управление API | [Журналы диагностики управления API](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Шлюзы приложений |[Ведение журнала диагностики для шлюза приложений](../../application-gateway/application-gateway-diagnostics.md) |
| Автоматизация Azure |[Log Analytics для службы автоматизации Azure](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Пакетная служба Azure |[Ведение журналов диагностики пакетной службы Azure](../../batch/batch-diagnostics.md) |
| База данных Azure для MySQL | [Журналы сервера в базе данных Azure для MySQL](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| База данных Azure для PostgreSQL | [Журналы сервера в базе данных Azure для PostgreSQL](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| Когнитивные сервисы | [Ведение журнала диагностики для Azure Cognitive Services](../../cognitive-services/diagnostic-logging.md) |
| Сеть кэширующих серверов | [Журналы Системы диагностики Azure для CDN](../../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB; | [Журнал ведения диагностики Azure Cosmos DB](../../cosmos-db/logging.md) |
| Фабрика данных | [Мониторинг фабрик данных с помощью Azure Monitor](../../data-factory/monitor-using-azure-monitor.md) |
| Аналитика озера данных |[Доступ к журналам диагностики для Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Storage |[Доступ к журналам диагностики Azure Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Концентраторы событий |[Журналы диагностики Центров событий Azure](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | Схема недоступна. |
| Брандмауэр Azure | Схема недоступна. |
| Центр Интернета вещей | [Использование Azure Monitor](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Ведение журнала хранилища ключей Azure](../../key-vault/key-vault-logging.md) |
| Load Balancer |[Log Analytics для Azure Load Balancer](../../load-balancer/load-balancer-monitor-log.md) |
| приложения логики; |[Настраиваемая схема отслеживания сообщений B2B для приложений логики](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| Группы безопасности сети |[Аналитика журналов для групп безопасности сети](../../virtual-network/virtual-network-nsg-manage-log.md) |
| Защита от атак DDoS | [Управление службой "Защита от атак DDoS Azure" уровня "Стандартный"](../../virtual-network/manage-ddos-protection.md) |
| Power BI (цен. категория "Выделенный") | [Ведение журнала диагностики для Power BI Embedded в Azure](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Службы восстановления | [Модель данных для Azure Backup](../../backup/backup-azure-reports-data-model.md)|
| служба поиска. |[Включение и использование аналитики поискового трафика](../../search/search-traffic-analytics.md) |
| Служебная шина Azure |[Журналы диагностики служебной шины Azure](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| База данных SQL | [Метрики и журналы диагностики Базы данных SQL Azure](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Журналы диагностики задания](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Диспетчер трафика | Схема журнала диспетчера трафика |
| Виртуальные сети | Схема недоступна. |
| Шлюзы виртуальной сети | Схема недоступна. |

## <a name="next-steps"></a>Следующие шаги

* [Узнайте больше о других журналах платформы Azure](platform-logs-overview.md) , которые можно использовать для мониторинга Azure.
