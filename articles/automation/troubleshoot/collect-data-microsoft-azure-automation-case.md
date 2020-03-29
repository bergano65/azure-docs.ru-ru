---
title: Данные для сбора при открытии кейса для Microsoft Azure Automation Документы Майкрософт
description: В этой статье описаны некоторые основные сведения, которые необходимо собрать перед открытием кейса для Автоматизации Azure с помощью службы поддержки Microsoft Azure.
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
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849383"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Данные, которые необходимо собрать для отправки запроса в службу автоматизации Microsoft Azure

В этой статье описаны некоторые основные сведения, которые необходимо собрать перед открытием кейса для Автоматизации Azure с помощью службы поддержки Microsoft Azure. Эта информация не требуется для открытия дела. Тем не менее, это может помочь корпорации Майкрософт решить вашу проблему быстрее. Кроме того, после открытия дела вас может запросить у инженера службы поддержки.

## <a name="collect-more-information"></a>Сбор дополнительной информации

Перед тем, как открыть кейс для службы автоматизации Microsoft Azure, мы рекомендуем вам собрать следующую информацию.

### <a name="basic-data-collection"></a>Базовый сбор данных

Соберите данные, описанные в следующей статье Базы Знаний:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Как запечатлеть диагностику с сценариями автоматизации Azure

## <a name="collect-data-depending-on-issue"></a>Сбор данных в зависимости от проблемы
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Для проблем, влияющих на управление обновлением на Linux

1. В дополнение к элементам, которые перечислены в KB [4034605,](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)запустите следующий инструмент сбора журналов:

   [OMS Linux Агент Журнал Коллектор](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Сжать содержимое следующей папки, а затем отправить сжатый файл в поддержку Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Проверить, что рабочее пространство ID OMS Linux агент сообщает, это то же самое, что рабочее пространство контролируется для обновления.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Для проблем, влияющих на управление обновлением в Windows

В дополнение к пунктам, которые перечислены в [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), сделать следующее:

1. Экспорт следующих журналов событий в формате EVTX:

   * Система
   * Приложение
   * Безопасность
   * Operations Manager
   * Microsoft-SMA/Оперативная

2. Проверить, что идентификатор рабочего пространства агент сообщает, это то же самое, что рабочее пространство контролируется обновлениями Windows.

### <a name="for-issues-that-affect-a-job"></a>Для вопросов, влияющих на работу

В дополнение к пунктам, которые перечислены в [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), собирать следующую информацию:

1. Соберите номер **JobID** (для работы, которая испытывает проблему):

   1. На портале Azure перейдите на лезвие **учетных записей автоматизации.**
   2. Выберите **учетную запись автоматизации,** которую вы убираете.
   3. Выберите **вакансии**.
   4. Выберите задание, которое вы убираете.
   5. В соответствии с **резюме вакансий**ищите **идентификатор вакансий** (GUID).

   ![Идентификатор вакансий в рамках Pane](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Соберите имя учетной записи:

   1. На портале Azure перейдите на лезвие **учетных записей автоматизации.**
   2. Получите название **учетной записи автоматизации,** которую вы убираете.

3. Соберите образец сценария, который вы работаете.

4. Соберите файлы журнала:

   1. На портале Azure перейдите на лезвие **учетных записей автоматизации.**
   2. Выберите **учетную запись автоматизации,** которую вы убираете.
   3. Выберите **вакансии**.
   4. Выберите задание, которое вы убираете.
   5. Выберите **все журналы**.
   6. На полученном лезвии соберите данные.

   ![Данные, перечисленные в всех журналах](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Для проблем, влияющих на модули

В дополнение к пунктам в рамках "Основной сбор данных", собрать следующую информацию:

* Шаги, которые вы следовали, чтобы проблема была воспроизведена.
* Скриншот любых сообщений об ошибках.
* Скриншот текущих модулей и их номеров версий.

## <a name="next-steps"></a>Дальнейшие действия

Если вам нужна дополнительная помощь в какой-либо момент этой статьи, обратитесь к экспертам Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/forums/)

Кроме того, подайте в файл инцидента с поддержкой Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**
