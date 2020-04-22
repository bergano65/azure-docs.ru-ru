---
title: Данные для сбора при открытии кейса для Microsoft Azure Automation Документы Майкрософт
description: В этой статье описана часть информации, которую необходимо собрать перед открытием кейса для Azure Automation с помощью службы поддержки Microsoft Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679405"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Данные, которые необходимо собрать для отправки запроса в службу автоматизации Microsoft Azure

В этой статье описана часть информации, которую необходимо собрать перед открытием кейса для Azure Automation с помощью службы поддержки Microsoft Azure. Эта информация не требуется для открытия дела. Тем не менее, это может помочь корпорации Майкрософт решить вашу проблему быстрее. Кроме того, после открытия дела вас может запросить у инженера службы поддержки.

## <a name="basic-data"></a>Базовые данные

Соберите основные данные, описанные в статье Базы знаний [4034605 - Как захватить Azure Автоматизация-сценарий диагностики](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Данные по вопросам управления обновлениями на Linux

1. В дополнение к элементам, которые перечислены в KB [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)запустите следующий инструмент сбора журналов:

   [OMS Linux Агент Журнал Коллектор](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Сжать содержимое **файла /var/opt/microsoft/omsagent/run/automationworker/,** а затем отправьте сжатый файл в службу поддержки Azure.
 
3. Убедитесь, что идентификатор рабочего пространства, который агент Log Analytics для Linux отчитывается, совпадает с идентификатором рабочего пространства, на который ведется мониторинг обновлений.

## <a name="data-for-update-management-issues-on-windows"></a>Данные для проблем управления обновлением в Windows

1. Сбор данных по элементам, перечисленным в [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Экспорт следующих журналов событий в формате EVTX:

   * Система
   * Приложение
   * Безопасность
   * Operations Manager
   * Microsoft-SMA/Оперативная

3. Убедитесь, что идентификатор рабочего пространства, о котором сообщает агент, совпадает с идентификатором рабочего пространства, контролируемым обновлениями Windows.

## <a name="data-for-job-issues"></a>Данные по вопросам трудоустройства

1. Сбор данных по элементам, перечисленным в [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Соберите идентификатор задания, у которого есть проблема:

   1. На портале Azure перейдите на **учетные записи автоматизации.**
   2. Выберите учетную запись Автоматизации, в которой вы занимаетесь устранением неполадок, и обратите внимание на имя.
   3. Выберите **вакансии**.
   4. Выберите задание, которое вы убираете.
   5. В панели Резюме вакансий ищите значение GUID в **Идентификаторе вакансий.**

   ![Идентификатор вакансий в рамках Pane](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Соберите образец запущенного сценария.

4. Соберите файлы журнала:

   1. На портале Azure перейдите на **учетные записи автоматизации.**
   2. Выберите учетную запись автоматизации, которую вы убираете.
   3. Выберите **вакансии**.
   4. Выберите задание, которое вы убираете.
   5. Выберите **все журналы**.
   6. В полученном стеколе соберите данные.

   ![Данные, перечисленные в всех журналах](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Данные по проблемам модуля

В дополнение к [основным элементам данных,](#basic-data)соберите следующую информацию:

* Шаги, которые вы следовали, так что проблема может быть воспроизведена.
* Скриншоты любых сообщений об ошибках.
* Скриншоты текущих модулей и их номера версий.

## <a name="next-steps"></a>Следующие шаги

Если вам нужна дополнительная помощь:

* Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
* Связаться [@AzureSupport](https://twitter.com/azuresupport)с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
