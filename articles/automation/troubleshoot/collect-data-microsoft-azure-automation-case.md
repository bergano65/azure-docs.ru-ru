---
title: Данные, которые будут собраны при открытии обращения для Microsoft Azure Automation | Документация Майкрософт
description: В этой статье описываются некоторые сведения, которые необходимо собрать перед открытием обращения к службе автоматизации Azure с поддержкой Microsoft Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679405"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Данные, которые необходимо собрать для отправки запроса в службу автоматизации Microsoft Azure

В этой статье описываются некоторые сведения, которые необходимо собрать перед открытием обращения к службе автоматизации Azure с поддержкой Microsoft Azure. Эта информация не является обязательной для открытия обращения. Тем не менее, он может помочь корпорации Майкрософт быстрее решить вашу проблему. Кроме того, эти данные могут быть запрошены инженером службы поддержки после открытия варианта.

## <a name="basic-data"></a>Основные данные

Собирайте основные данные, описанные в статье базы знаний [4034605 — как записать в службу автоматизации Azure сценарии диагностики](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Данные для Управление обновлениями проблем в Linux

1. Помимо элементов, перечисленных в KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), запустите следующее средство сбора журналов:

   [Сборщик данных журнала агента OMS для Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Сжимать содержимое папки **/Вар/ОПТ/Микрософт/омсажент/рун/аутоматионворкер/** , а затем передавать сжатый файл в службу поддержки Azure.
 
3. Убедитесь, что идентификатор для рабочей области, в которую записывается агент Log Analytics для Linux, совпадает с ИДЕНТИФИКАТОРом рабочей области, для которой выполняется отслеживание обновлений.

## <a name="data-for-update-management-issues-on-windows"></a>Данные для Управление обновлениями проблем в Windows

1. Собирайте данные для элементов, перечисленных в [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Экспортируйте следующие журналы событий в формат EVTX:

   * система
   * Приложение
   * Безопасность
   * Operations Manager
   * Microsoft-SMA/Рабочий

3. Убедитесь, что идентификатор рабочей области, на которую отправляется агент, совпадает с ИДЕНТИФИКАТОРом рабочей области, отслеживаемой обновлениями Windows.

## <a name="data-for-job-issues"></a>Данные для проблем задания

1. Собирайте данные для элементов, перечисленных в [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Собирайте идентификатор задания, в котором возникла ошибка:

   1. В портал Azure перейдите в раздел **учетные записи службы автоматизации**.
   2. Выберите учетную запись службы автоматизации, для которой выполняется устранение неполадок, и запишите имя.
   3. Выберите **задания**.
   4. Выберите задание, которое вы хотите устранить.
   5. В области Сводка по заданию найдите значение GUID в поле **идентификатор задания**.

   ![Идентификатор задания на панели "Сводка по заданию"](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Соберете образец выполняемого сценария.

4. Собирайте файлы журналов:

   1. В портал Azure перейдите в раздел **учетные записи службы автоматизации**.
   2. Выберите учетную запись службы автоматизации, для которой выполняется устранение неполадок.
   3. Выберите **задания**.
   4. Выберите задание, которое вы хотите устранить.
   5. Выберите **все журналы**.
   6. В полученной области собирайте данные.

   ![Данные, перечисленные во всех журналах](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Данные для проблем с модулем

Помимо [основных элементов данных](#basic-data), соберите следующие сведения:

* Вы выполнили следующие действия, чтобы проблема могла быть воспроизведена.
* Снимки экрана всех сообщений об ошибках.
* Снимки экрана текущих модулей и их номера версий.

## <a name="next-steps"></a>Дальнейшие шаги

Если вам нужна дополнительная помощь:

* Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport)помощью официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.
* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **получить поддержку**.
