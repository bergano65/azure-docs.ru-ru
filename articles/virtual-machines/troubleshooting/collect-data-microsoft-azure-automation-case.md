---
title: Данные, которые будут собраны при открытии обращения для Microsoft Azure Automation | Документация Майкрософт
description: В этой статье описываются некоторые основные сведения, которые необходимо собрать перед открытием обращения к службе автоматизации Azure с поддержкой Microsoft Azure.
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
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846684"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Данные, которые будут собраны при открытии обращения для службы автоматизации Microsoft Azure

В этой статье описываются некоторые основные сведения, которые необходимо собрать перед открытием обращения к службе автоматизации Azure с поддержкой Microsoft Azure. Эта информация не является обязательной для открытия обращения. Тем не менее, он может помочь корпорации Майкрософт быстрее решить вашу проблему. Кроме того, эти данные могут быть запрошены инженером службы поддержки после открытия варианта.

## <a name="collect-more-information"></a>Получить дополнительные сведения

Перед открытием обращения в службу поддержки Microsoft Azure Automation рекомендуется собираются следующие сведения.

### <a name="basic-data-collection"></a>Базовый сбор данных

Собирайте данные, описанные в следующей статье базы знаний:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) . как записать в службу автоматизации Azure сценарии диагностики

## <a name="collect-data-depending-on-issue"></a>Получение данных в зависимости от проблемы
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Проблемы, влияющие на Управление обновлениями в Linux

1. Помимо элементов, перечисленных в KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), запустите следующее средство сбора журналов:

   [Сборщик данных журнала агента OMS для Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Выполните сжатие содержимого следующей папки, а затем отправьте сжатый файл в службу поддержки Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Убедитесь, что идентификатор рабочей области, с которой агент OMS для Linux сообщается, совпадает с рабочей областью, для которой выполняется отслеживание обновлений.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Проблемы, влияющие на Управление обновлениями в Windows

Помимо элементов, перечисленных в [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), выполните следующие действия.

1. Экспортируйте следующие журналы событий в формат EVTX:

   * Система
   * Приложение
   * Группа безопасности
   * Operations Manager
   * Microsoft-SMA/Рабочий

2. Убедитесь, что идентификатор рабочей области, на который сообщает агент, совпадает с рабочей областью, отслеживаемой обновлениями Windows.

### <a name="for-issues-that-affect-a-job"></a>Проблемы, влияющие на задание

Помимо элементов, перечисленных в [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), собирайте следующие сведения:

1. Собирайте номер **JOBID** (для задания, в котором возникла проблема):

   1. На портал Azure перейдите в колонку **учетные записи службы автоматизации** .
   2. Выберите **учетную запись службы автоматизации** , для которой выполняется устранение неполадок.
   3. Выберите **задания**.
   4. Выберите задание, которое вы хотите устранить.
   5. В разделе **Сводка по заданию**найдите **идентификатор задания** (GUID).

   ![Идентификатор задания на панели "Сводка по заданию"](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Собирайте имя учетной записи:

   1. На портал Azure перейдите в колонку **учетные записи службы автоматизации** .
   2. Получите имя **учетной записи службы автоматизации** , которую вы используете для устранения неполадок.

3. Собирайте образец выполняемого сценария.

4. Собирайте файлы журналов:

   1. На портал Azure перейдите в колонку **учетные записи службы автоматизации** .
   2. Выберите **учетную запись службы автоматизации** , для которой выполняется устранение неполадок.
   3. Выберите **задания**.
   4. Выберите задание, которое вы хотите устранить.
   5. Выберите **все журналы**.
   6. В результирующей колонке собирайте данные.

   ![Данные, перечисленные во всех журналах](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Проблемы, влияющие на модули

Помимо элементов в разделе "Базовый сбор данных", соберите следующие сведения:

* После этого вы выполните действия, которые помогут воспроизвести проблему.
* Снимок экрана любых сообщений об ошибках.
* Снимок экрана текущих модулей и их номеров версий.

## <a name="next-steps"></a>Следующие шаги

Если вам нужна дополнительная помощь в любой момент в этой статье, обратитесь к экспертам по Azure на [форумах MSDN Azure и Stack overflow](https://azure.microsoft.com/support/forums/).

Кроме того, можно зафайлировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.