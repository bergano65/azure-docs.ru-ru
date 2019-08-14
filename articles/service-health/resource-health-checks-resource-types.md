---
title: Поддерживаемые типы ресурсов в службе работоспособности ресурсов Azure | Документация Майкрософт
description: Поддерживаемые типы ресурсов в службе работоспособности ресурсов Azure
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.openlocfilehash: 1692e8acda88a51173eb0eabcd90c377b431a3c3
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955032"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Типы ресурсов и проверки работоспособности в службе работоспособности ресурсов Azure
Ниже приведен полный список проверок, выполняемых в системе работоспособности ресурсов для разных типов ресурсов.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Выполняемые проверки|
|---|
|<ul><li>Сервер запущен и работает?</li><li>На сервере недостаточно памяти?</li><li>Сервер запускается?</li><li>Сервер восстанавливается?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Выполняемые проверки|
|---|
|<ul><li>Служба Api Management запущена и работает?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Выполняемые проверки|
|---|
|<ul><li>Запускается ли учетная запись пакетной службы?</li><li>Превышена ли квота пула для этой учетной записи пакетной службы?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Выполняемые проверки|
|---|
|<ul><li>Все ли узлы кэша запущены и работают?</li><li>Доступен ли кэш из центра обработки данных?</li><li>Достигнуто ли для кэша максимальное число подключений?</li><li> Исчерпан ли лимит доступной памяти кэша? </li><li>Возникает ли в кэше большое число ошибок страниц?</li><li>Работает ли кэш с повышенной загрузкой?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Выполняемые проверки|
|---|
|<ul> <li>Доступен ли дополнительный портал для операций настройки CDN?</li><li>Существуют ли проблемы с доставкой между конечными точками CDN?</li><li>Могут ли пользователи изменять настройки ресурсов CDN?</li><li>Изменения конфигурации публикуются с ожидаемой скоростью?</li><li>Могут ли пользователи управлять конфигурацией CDN с помощью портала Azure, PowerShell или API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Выполняемые проверки|
|---|
|<ul><li>Сервер узла запущен и работает?</li><li>Завершена ли загрузка ОС на узле?</li><li>Контейнер виртуальной машины подготовлен и включен?</li><li>Есть ли сетевое подключение между узлом и учетной записи хранения?</li><li>Завершена ли загрузка гостевой операционной системы?</li><li>Есть ли назначенные работы по техническому обслуживанию?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Выполняемые проверки|
|---|
|<ul><li>Доступна ли учетная запись из центра обработки данных?</li><li>Доступен ли поставщик ресурсов Cognitive Services?</li><li>Доступна ли служба Cognitive Services из соответствующего региона?</li><li>Успешно ли выполняются операции чтения для учетной записи хранения, в которой хранятся метаданные ресурсов?</li><li>Достигнута ли квота на вызовы API?</li><li>Достигнут ли лимит чтения на вызовы API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Выполняемые проверки|
|---|
|<ul><li>Сервер, размещающий эту виртуальную машину, включен и работает?</li><li>Завершена ли загрузка ОС на узле?</li><li>Контейнер виртуальной машины подготовлен и включен?</li><li>Есть ли сетевое подключение между узлом и учетной записи хранения?</li><li>Завершена ли загрузка гостевой операционной системы?</li><li>Есть ли назначенные работы по техническому обслуживанию?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Фабрика Microsoft.
|Выполняемые проверки|
|---|
|<ul><li>Есть ли ошибки конвейерного выполнения?</li><li>Является ли кластер, на котором размещена фабрика данных, работоспособным?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Выполняемые проверки|
|---|
|<ul><li>У пользователей возникали проблемы с отправкой или размещением заданий Data Lake Analytics?</li><li>Не удается завершить задания Data Lake Analytics из-за системных ошибок?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Выполняемые проверки|
|---|
|<ul><li>У пользователей возникали проблемы с отправкой данных в Data Lake Store?</li><li>У пользователей возникали проблемы с загрузкой данных из Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft. Migration/Services
|Выполняемые проверки|
|---|
|<ul><li>Не удалось выполнить подготовку к работе службы миграции баз данных?</li><li>Остановлена ли служба миграции баз данных из-за неактивности или запроса пользователя?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. файл или учетные записи
|Выполняемые проверки|
|---|
|<ul><li>Работает ли учетная запись общего доступа к данным?</li><li>Доступен ли кластер, на котором размещена общая папка данных?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Выполняемые проверки|
|---|
|<ul><li>Сервер недоступен из-за обслуживания?</li><li>Сервер недоступен из-за перенастройки?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Выполняемые проверки|
|---|
|<ul><li>Сервер недоступен из-за обслуживания?</li><li>Сервер недоступен из-за перенастройки?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Выполняемые проверки|
|---|
|<ul><li>Сервер недоступен из-за обслуживания?</li><li>Сервер недоступен из-за перенастройки?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Выполняемые проверки|
|---|
|<ul><li>Работает ли Центр Интернета вещей?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Выполняемые проверки|
|---|
|<ul><li>Существуют ли запросы к базам данных или коллекциям, которые не были выполнены из-за недоступности службы Azure Cosmos DB?</li><li>Существуют ли запросы к документам, которые не были выполнены из-за недоступности службы Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft. eventhub/пространства имен
|Выполняемые проверки|
|---|
|<ul><li>Возникает ли в пространстве имен концентраторов событий ошибки, вызванные пользователем?</li><li>Обновляется ли пространство имен концентраторов событий в настоящее время?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft. hdinsight/кластеры
|Выполняемые проверки|
|---|
|<ul><li>Доступны ли основные службы в кластере HDInsight?</li><li>Может ли кластер HDInsight получить доступ к ключу для шифрования неактивных BYOK?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Выполняемые проверки|
|---|
|<ul><li>Запросы в хранилище ключей завершались сбоем из-за проблем с платформой Azure Key Vault?</li><li>Для запросов в хранилище ключей требовалось регулирование из-за слишком большого количества запросов от клиента?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Выполняемые проверки|
|---|
|<ul><li>Работает ли веб-служба?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/mediaservices
|Выполняемые проверки|
|---|
|<ul><li>Работает ли служба мультимедиа?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/аппликатионгатевайс
|Выполняемые проверки|
|---|
|<ul><li>Снижается ли производительность шлюза приложений?</li><li>Доступен ли шлюз приложений?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Выполняемые проверки|
|---|
|<ul><li>Подключен ли VPN-туннель?</li><li>Существуют ли конфликты конфигурации для соединения?</li><li>Правильно ли настроены предварительные общие ключи?</li><li>Доступно ли VPN-устройство в локальной среде?</li><li>Есть ли несоответствия в политике безопасности IPSec/IKE?</li><li>Правильно ли подготовлено подключение S2S VPN или оно находится в состоянии сбоя?</li><li>Правильно ли подготовлено подключение между виртуальными сетями или оно находится в состоянии сбоя?</li></ul>|

## <a name="microsoftnetworkexpressreoutecircuits"></a>Microsoft. Network/експрессреаутеЦиркуитс
|Выполняемые проверки|
|---|
|<ul><li>Является ли канал ExpressRoute работоспособным?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/фронтдурс
|Выполняемые проверки|
|---|
|<ul><li>Зависает ли Серверная дверца с ошибками в пробах работоспособности?</li><li>Откладывается ли изменение конфигурации?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/LoadBalancers
|Выполняемые проверки|
|---|
|<ul><li>Доступны ли конечные точки балансировки нагрузки?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Выполняемые проверки|
|---|
|<ul><li>Доступен ли VPN-шлюз из Интернета?</li><li>Находится ли VPN-шлюз в режиме ожидания?</li><li>Запущена ли служба VPN на шлюзе?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Выполняемые проверки|
|---|
|<ul><li>Успешно ли выполняются для пространства имен операции времени выполнения, такие как регистрации, установки и отправки?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. operationalinsights/рабочие области
|Выполняемые проверки|
|---|
|<ul><li>Существуют ли задержки индексации для рабочей области?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Выполняемые проверки|
|---|
|<ul><li>Запущен и работает ли ресурс производительности?</li><li>Все ли рабочие нагрузки запущены и работают?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Выполняемые проверки|
|---|
|<ul><li>Работает ли операционная система узла?</li><li>Доступна ли коллекция workspaceCollection из-за пределов центра обработки данных?</li><li>Доступен ли Power BI поставщик ресурсов?</li><li>Доступна ли служба Power BI в соответствующем регионе?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Выполняемые проверки|
|---|
|<ul><li>Успешно ли выполняются операции диагностики для кластера?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Выполняемые проверки|
|---|
|<ul><li>Сталкиваются ли клиенты с ошибками, связанными со Служебной шиной?</li><li>Возникает ли у пользователей увеличение переходных ошибок из-за обновления пространства имен Служебной шины?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/Манажединстанцес/базы данных
|Выполняемые проверки|
|---|
|<ul><li>Работает ли база данных?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft. SQL/Server/базы данных
|Выполняемые проверки|
|---|
|<ul><li>Выполнялись ли входы в базу данных?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Выполняемые проверки|
|---|
|<ul><li>Запросы на чтение данных из учетной записи хранения завершались сбоем из-за проблем с платформой Azure Storage?</li><li>Запросы на запись данных в учетную запись хранения завершались сбоем из-за проблем с платформой Azure Storage?</li><li>Кластер хранилищ, в котором размещена учетная запись хранения, недоступен?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Выполняемые проверки|
|---|
|<ul><li>Все ли узлы, на которых выполняются задания, успешно работают?</li><li>Существуют ли задания, которые не удалось запустить?</li><li>Существуют ли запланированные обновления среды выполнения?</li><li>Находится ли задание в запланированном состоянии (запущено или остановлено клиентом)?</li><li>Возникали ли исключения нехватки памяти при выполнении задания?</li><li>Существуют ли запланированные обновления вычислительной среды?</li><li>Доступен ли диспетчер выполнения (план управления)?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Выполняемые проверки|
|---|
|<ul><li>Сервер узла запущен и работает?</li><li>Работает ли служба Internet Information Services?</li><li>Работает ли подсистема балансировки нагрузки?</li><li>Доступен ли план службы приложений из центра обработки данных?</li><li>Доступна ли учетная запись хранилища, в которой размещается содержимое узлов для фермы серверов?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Выполняемые проверки|
|---|
|<ul><li>Сервер узла запущен и работает?</li><li>Работает ли сервер Internet Information?</li><li>Работает ли подсистема балансировки нагрузки?</li><li>Доступно ли веб-приложение из центра обработки данных?</li><li>Доступна ли учетная запись хранения, в которой размещается контент сайта?</li></ul>|

## <a name="next-steps"></a>Следующие шаги
-  Дополнительные сведения см. в [общих сведениях о панели мониторинга работоспособности службы Azure](service-health-overview.md) и [общих сведениях о службе "Работоспособность ресурсов Azure"](resource-health-overview.md). 
-  [Azure Resource Health FAQ](resource-health-faq.md) (Часто задаваемые вопросы о службе работоспособности ресурсов Azure)
- Настройте оповещения о проблемах, связанных с работоспособностью. Дополнительные сведения см. в статье [Создание оповещений журнала действий для уведомлений службы](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
