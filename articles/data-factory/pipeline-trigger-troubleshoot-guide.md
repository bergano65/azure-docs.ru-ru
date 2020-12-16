---
title: Устранение неполадок оркестрации конвейера и триггеров в фабрике данных Azure
description: Используйте различные методы для устранения проблем с триггерами конвейера в фабрике данных Azure.
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: 0e67a316b012eda61607c84edfd8e10d6aa3318d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589174"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Устранение неполадок оркестрации конвейера и триггеров в фабрике данных Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Запуск конвейера в службе "Фабрика данных Azure" определяет экземпляр выполнения конвейера. Например, у вас есть конвейер, который выполняется в 8:00 AM, 9:00 AM и 10:00 AM. В этом случае выполняются три отдельных запуска конвейера или конвейера. Для каждого запуска конвейера предусмотрен уникальный идентификатор. Идентификатор запуска — это идентификатор GUID (глобальный уникальный идентификатор), определяющий конкретный запуск конвейера.

Запуск конвейера обычно создается путем передачи аргументов в параметры, определенные в конвейерах. Конвейер можно выполнить двумя способами: вручную или с помощью триггера. Дополнительные сведения см. в разделе [выполнение конвейера и триггеры в фабрике данных Azure](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Распространенные проблемы, причины и решения

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Конвейер с функцией Azure вызывает ошибку с частным подключением к конечной точке
 
#### <a name="issue"></a>Проблема
В некоторых контекстах у вас есть фабрика данных и Azure приложение-функция, работающие в частной конечной точке. Вы пытаетесь получить конвейер, который взаимодействует с приложение-функция Azure для работы. Вы предпринимали три разных метода, но один возвращает ошибку `Bad Request` , два других метода возвращают `103 Error Forbidden` .

#### <a name="cause"></a>Причина 
Сейчас фабрика данных не поддерживает закрытый соединитель конечной точки для Azure приложение-функция. Это должна быть причина, по которой Azure приложение-функция отклоняет вызовы, так как она настроена на разрешение только подключений с частного канала.

#### <a name="resolution"></a>Решение
Вы можете создать закрытую конечную точку типа **привателинксервице** и указать DNS приложения-функции, и подключение должно работать.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>Выполнение конвейера прервано, но монитор по-прежнему отображает состояние хода выполнения

#### <a name="issue"></a>Проблема
Часто при завершении выполнения конвейера мониторинг конвейера по-прежнему показывает состояние хода выполнения. Это происходит из-за проблемы с кэшем в браузере, и у вас нет нужных фильтров для мониторинга.

#### <a name="resolution"></a>Решение
Обновите браузер и примените нужные фильтры для мониторинга.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Ошибка конвейера копирования — найдено больше столбцов, чем ожидалось (Делимитедтекстмореколумнссандефинед)

#### <a name="issue"></a>Проблема  
Если файлы в определенной копируемой папке содержат файлы с разными схемами, такими как переменное число столбцов, различные разделители, параметры символов кавычек или некоторые проблемы с данными, конвейер фабрики данных завершит работу в этой ошибке:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Решение
При создании действия Копирование данных выберите параметр "двоичное копирование". Таким образом, при копировании или переносе данных с одного Data Lake на другой, с параметром **binary** фабрика данных не открывает файлы для чтения схемы, но обрабатывает все файлы как двоичные и копирует их в другое расположение.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>Сбой выполнения конвейера при достижении предела емкости среды выполнения интеграции

#### <a name="issue"></a>Проблема
Сообщение об ошибке:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

Эта ошибка указывает на ограничение для среды выполнения интеграции, которое в настоящее время 50. Дополнительные сведения см. в разделе [ограничения](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) .

Если вы выполняете большой объем потока данных одновременно с помощью одной и той же среды выполнения интеграции, это может вызвать ошибку такого рода.

#### <a name="resolution"></a>Решение 
- Разделите эти конвейеры для выполнения разных периодов активации.
- Создайте новую среду выполнения интеграции и разделите эти конвейеры между несколькими средами выполнения интеграции.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Мониторинг сбоев конвейера с регулярным интервалом

#### <a name="issue"></a>Проблема
Часто приходится отслеживать конвейеры фабрики данных за интервалы, скажем 5 минут. Вы можете запрашивать и фильтровать запуски конвейера из фабрики данных с помощью конечной точки. 

#### <a name="recommendation"></a>Рекомендация
1. Настройте приложение логики Azure для запроса всех конвейеров со сбоями каждые 5 минут.
2. Затем вы можете сообщить о происшествиях в нашу систему билетов в соответствии с [куерибифактори](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

#### <a name="reference"></a>Справочник
- [Внешняя отправка уведомлений из фабрики данных](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Обработка ошибок и сбоев на уровне действий в конвейерах

#### <a name="issue"></a>Проблема
Оркестрации фабрики данных Azure позволяет использовать условную логику и позволяет пользователю принимать различные пути на основе результатов предыдущего действия. Это позволяет выполнить четыре условных пути: "при успешном выполнении (по умолчанию)," после сбоя "," по завершении "и" при пропуске ". Допускается использование разных путей.

Фабрика данных Azure определяет успешное выполнение и сбой конвейера следующим образом.

- Оцените выходные данные для всех действий конечного уровня. Если конечное действие пропущено, вместо него вычисляется его родительское действие.
- Результат конвейера будет успешным, только если все листья завершились успешно.

#### <a name="recommendation"></a>Рекомендация
- Реализуйте проверки на уровне действий, следуя [указаниям по обработке сбоев и ошибок конвейера](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
- Используйте приложение логики Azure для мониторинга конвейеров через регулярные интервалы, следующие за [запросом]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог о Фабрике данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы на добавление функции в Фабрику данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видео по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Страница вопросов (раздел вопросов и ответов на сайте Майкрософт)](/answers/topics/azure-data-factory.html)
*  [Сведения о Фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)