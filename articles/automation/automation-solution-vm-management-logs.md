---
title: Как запрашивать журналы из Запуск и остановка виртуальных машин в нерабочее время
description: В этой статье описывается, как запрашивать данные журнала, созданные Запуск и остановка виртуальных машин в нерабочее время решением из Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 472baa3f4b3cbb970a8f365ccc94929ad565c421
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864238"
---
# <a name="how-to-query-logs-from-startstop-vms-during-off-hours"></a>Как запрашивать журналы из Запуск и остановка виртуальных машин в нерабочее время

Служба автоматизации Azure перенаправляет два типа записей в связанную рабочую область Log Analytics: журналы заданий и потоки заданий. В этой статье рассматриваются данные, доступные для [запроса](../azure-monitor/log-query/log-query-overview.md) в Azure Monitor.

## <a name="job-logs"></a>Журналы заданий

|Свойство. | Описание|
|----------|----------|
|Caller |  Сторона, инициировавшая операцию. Допустимые значения: электронный адрес или system для запланированных заданий.|
|Категория | Классификация типа данных. Для службы автоматизации значением является JobLogs.|
|CorrelationId | GUID, представляющий собой идентификатор корреляции задания runbook.|
|JobId | GUID, представляющий собой идентификатор задания runbook.|
|operationName | Указывает тип операции, выполняемой в Azure. Для службы автоматизации значением является Job.|
|resourceId | Указывает тип ресурса в Azure. Для службы автоматизации значением является учетная запись службы автоматизации, связанная с Runbook.|
|ResourceGroup | Указывает имя группы ресурсов задания Runbook.|
|ResourceProvider | Указывает службу Azure, которая предоставляет ресурсы для развертывания и управления. Для службы автоматизации значением будет Azure Automation.|
|ResourceType | Указывает тип ресурса в Azure. Для службы автоматизации значением является учетная запись службы автоматизации, связанная с Runbook.|
|resultType | Состояние задания Runbook. Возможны следующие значения:<br>Started<br>- Остановлена<br>Приостановлено<br>Сбой<br>- Succeeded.|
|resultDescription | Описывает состояние результата задания Runbook. Возможны следующие значения:<br>— Job is started;<br>— Job Failed;<br>- Job Completed.|
|RunbookName | Указывает имя модуля Runbook.|
|SourceSystem | Указывает исходную систему для отправленных данных. Для службы автоматизации значением является OpsManager.|
|StreamType | Задает тип события. Возможны следующие значения:<br>- Verbose.<br>Выходные данные<br>error<br>Предупреждение|
|SubscriptionId | Указывает идентификатор подписки задания.
|Время | Дата и время выполнения задания Runbook.|

## <a name="job-streams"></a>Потоки заданий

|Свойство. | Описание|
|----------|----------|
|Caller |  Сторона, инициировавшая операцию. Допустимые значения: электронный адрес или system для запланированных заданий.|
|Категория | Классификация типа данных. Для службы автоматизации значением является JobStreams.|
|JobId | GUID, представляющий собой идентификатор задания runbook.|
|operationName | Указывает тип операции, выполняемой в Azure. Для службы автоматизации значением является Job.|
|ResourceGroup | Указывает имя группы ресурсов задания Runbook.|
|resourceId | Указывает идентификатор ресурса в Azure. Для службы автоматизации значением является учетная запись службы автоматизации, связанная с Runbook.|
|ResourceProvider | Указывает службу Azure, которая предоставляет ресурсы для развертывания и управления. Для службы автоматизации значением будет Azure Automation.|
|ResourceType | Указывает тип ресурса в Azure. Для службы автоматизации значением является учетная запись службы автоматизации, связанная с Runbook.|
|resultType | Результат задания Runbook во время создания события. Возможное значение:<br>- InProgress.|
|resultDescription | Включает в себя выходной поток из Runbook.|
|RunbookName | Имя Runbook.|
|SourceSystem | Указывает исходную систему для отправленных данных. Для службы автоматизации значением является OpsManager.|
|StreamType | Тип потока задания. Возможны следующие значения:<br>- Progress<br>Выходные данные<br>Предупреждение<br>error<br>debug<br>- Verbose.|
|Время | Дата и время выполнения задания Runbook.|

При выполнении поиска по журналам, в результате которого возвращаются записи категории **JobLogs** или **JobStreams**, можно выбрать представление **JobLogs** или **JobStreams**. Эти представления отображают набор элементов с перечнем обновлений, возвращенных в результатах поиска.

## <a name="sample-log-searches"></a>Пример поисков журналов

Следующая таблица содержит примеры поисков по журналу для получения записей заданий, собранных этим решением.

|Запрос | Описание|
|----------|----------|
|Поиск успешно выполненных заданий runbook ScheduledStartStop_Parent. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Поиск заданий для ScheduledStartStop_Parent Runbook, которые не были успешно выполнены | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Поиск успешно выполненных заданий runbook SequencedStartStop_Parent. | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Поиск заданий для SequencedStartStop_Parent Runbook, которые не были успешно выполнены | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Дальнейшие действия

**Запуск и остановка виртуальных машин в нерабочее время** решение не включает предопределенный набор предупреждений. Дополнительные сведения о создании оповещений о сбое задания для поддержки DevOps или рабочих процессов и процедур см. в статье [Создание оповещений журнала](../azure-monitor/platform/alerts-log.md) с помощью Azure Monitor.