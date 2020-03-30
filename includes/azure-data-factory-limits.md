---
title: включить файл
description: включить файл
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086139"
---
Azure Data Factory — это мультитенантная служба, которая имеет следующие ограничения по умолчанию, чтобы убедиться, что подписка клиентов защищена от рабочих нагрузок друг друга. Чтобы повысить лимиты до максимума для подписки, обратитесь в службу поддержки.

### <a name="version-2"></a>версия 2

| Ресурс | Ограничение по умолчанию | Максимальное ограничение |
| -------- | ------------- | ------------- |
| Число фабрик данных в подписке Azure. | 800 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Общее число сущностей, таких как конвейеры, наборы данных, триггеры, связанные службы и время выполнения интеграции, на фабрике данных | 5 000 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Всего ядер процессора для интеграции Azure-SSIS Runtimes по одной подписке | 256 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Одновременные запуски конвейеров на фабрику данных, которая разделена между всеми трубопроводами на заводе | 10 000  | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Одновременные внешние действия выполняется в течение подписки в [регионе runtime интеграции Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Внешние действия управляются на время выполнения интеграции, но выполняются на связанных службах, включая Databricks, сохраненную процедуру, HDInsights, Web и другие.</small> | 3000 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Одновременные действия трубопровода выполняется в течение подписки в [регионе запуска azure Integration](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Действия трубопровода выполняются во время выполнения интеграции, включая Lookup, GetMetadata и Delete.</small>| 1000 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Параллельные операции авторизации на подписку в [регионе Runtime интеграции Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Включая тест-соединение, список папок просмотра и список таблиц, предварительные данные. | 200 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Одновременное потребление единиц интеграции данных<sup>1</sup> в одну подписку в [регионе runtime интеграции Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Группа 1<sup>2</sup>: 6000<br>Группа региона 2<sup>2</sup>: 3000<br>Группа 3<sup>2 :</sup>1500 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Максимальная активность на конвейер, которая включает внутреннюю деятельность для контейнеров | 40 | 40 |
| Максимальное количество связанных периодов интеграции, которые могут быть созданы на основе единого автономного времени выполнения интеграции | 100 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Максимальные параметры на конвейер | 50 | 50 |
| Элементы ForEach | 100 000 | 100 000 |
| Параллелизм ForEach | 20 | 50 |
| Максимальные очереди на конвейер | 100 | 100 |
| Максимальное число символов в выражении | 8192 | 8192 |
| Минимальный интервал триггера окна акробатика | 15 мин. | 15 мин. |
| Максимальный тайм-аут для работы конвейера | 7 дней | 7 дней |
| Байты на объект для объектов трубопровода<sup>3</sup> | 200 КБ | 200 КБ |
| Байты на объект для набора данных и связанных объектов<sup>службы 3</sup> | 100 КБ | 2000 кБ |
| Единицы интеграции данных<sup>1</sup> на запуск действия копии | 256 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Количество вызовов API записи | 1200/ч<br/><br/> Это ограничение накладывается Azure Resource Manager, а не фабрикой данных Azure. | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Количество вызовов API чтения | 12 500/ч<br/><br/> Это ограничение накладывается Azure Resource Manager, а не фабрикой данных Azure. | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Мониторинг запросов в минуту | 1000 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Операции CRUD с сущностями в минуту | 50 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Максимальное время сеанса отлажения потока данных | 8 часов | 8 часов |
| Одновременное количество потоков данных на фабрику | 50 | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Одновременное количество сеансов отладки потока данных на одного пользователя на фабрику | 3 | 3 |
| Ограничение потока данных Azure IR TTL | 4 часа | [Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> Блок интеграции данных (DIU) используется в операции копирования от облака к облаку, узнайте больше из [блоков интеграции данных (версия 2).](../articles/data-factory/copy-activity-performance.md#data-integration-units) Для получения информации [Azure Data Factory pricing](https://azure.microsoft.com/pricing/details/data-factory/)о выставлении счетов см.

<sup>2</sup> [Запуск Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) [доступен по всему миру для](https://azure.microsoft.com/global-infrastructure/services/) обеспечения соответствия данных, эффективности и снижения затрат на выход из сети. 

| Группа регионов | Регионы | 
| -------- | ------ |
| Группа 1 региона | Центральная часть США, Восток США, Восток США2, Северная Европа, Западная Европа, Западная США, Западная США, Западная США 2 |
| Группа 2 региона | Австралия Восток, Австралия юго-восток, Бразилия южнее, Центральная Индия, Япония Восток, Северо-Центральный США, Southcentral США, юго-восточной Азии, Западно-Центральной США |
| Группа 3 региона | Канада Центральная, Восточная Азия, Франция Центральная, Корея Центральная, Великобритания на юге |

<sup>3</sup> Конвейер, набор данных и связанные объекты службы представляют собой логическую группировку рабочей нагрузки. Ограничения для этих объектов не связаны с объемом данных, которые можно перемещать и обрабатывать с помощью Azure Data Factory. Data Factory предназначен для масштабирования для обработки петабайт данных.

### <a name="version-1"></a>версия 1

| **Ресурсов** | **Ограничение по умолчанию** | **Максимальное ограничение** |
| --- | --- | --- |
| Число конвейеров в фабрике данных. |2500 |[Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Наборы данных на фабрике данных |5 000 |[Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Параллельные срезы на набор данных |10 |10 |
| Байты на объект для объектов трубопровода<sup>1</sup> |200 КБ |200 КБ |
| Байты на объект для набора данных и связанных объектов<sup>службы 1</sup> |100 КБ |2000 кБ |
| Ядра кластера Azure HDInsight по требованию в рамках подписки<sup>2</sup> |60 |[Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Единицы перемещения облачных данных на одну активность копирования работают<sup>3</sup> |32 |[Обратитесь в службу поддержки](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Число повторных попыток выполнить действие конвейера  |1000 |MaxInt (32 разряда) |

<sup>1</sup> Конвейер, набор данных и связанные объекты службы представляют собой логическую группировку рабочей нагрузки. Ограничения для этих объектов не связаны с объемом данных, которые можно перемещать и обрабатывать с помощью Azure Data Factory. Data Factory предназначен для масштабирования для обработки петабайт данных.

<sup>2</sup> Ядра HDInsight по запросу назначаются из подписки, которая содержит фабрику данных. В результате предыдущим ограничением является установленный на основе хранилища ядро Data Factory для ядер HDInsight по требованию. Он отличается от основного предела, связанного с подпиской Azure.

<sup>3</sup> Блок перемещения облачных данных (DMU) для версии 1 используется в операции копирования от облака к облаку, узнайте больше из [единиц перемещения данных облака (версия 1).](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) Для получения информации [Azure Data Factory pricing](https://azure.microsoft.com/pricing/details/data-factory/)о выставлении счетов см.

| **Ресурсов** | **Нижнее ограничение по умолчанию** | **Минимальное ограничение** |
| --- | --- | --- |
| Интервал планирования |15 минут |15 минут |
| Интервал между повторными попытками |1 с |1 с |
| Значение времени ожидания повтора |1 с |1 с |

#### <a name="web-service-call-limits"></a>Ограничения вызова веб-служб
Для Azure Resource Manager действуют ограничения для вызовов API. Можно вызывать API с частотой, соответствующей [ограничениям API диспетчера ресурсов Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
