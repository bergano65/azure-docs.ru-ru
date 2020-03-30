---
title: Watchdog BUG мягкая ошибка блокировки процессора из кластера Azure HDInsight
description: Watchdog BUG мягкий процессор блокировки появляется в слогах ядра из кластера Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 701e314ad2a3762b1e8ca022ce18d9435ce2db37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894107"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Сценарий: "сторожевой пёс: BUG: мягкая блокировка - CPU" ошибка из кластера Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Слогы ядра содержат сообщение об `watchdog: BUG: soft lockup - CPU`ошибке: .

## <a name="cause"></a>Причина

[Ошибка](https://bugzilla.kernel.org/show_bug.cgi?id=199437) в linux Kernel вызывает мягкие блокировки процессора.

## <a name="resolution"></a>Решение

Нанесите патч ядра. Скрипт ниже модернизирует ядро linux и перезагружает машины в разное время в течение 24 часов. Выполните действие скрипта двумя партиями. Первая партия находится на всех узлах, кроме головного узла. Вторая партия находится на головном узлах. Не запускайте одновременно головной узел и другие узлы.

1. Перейдите к кластеру HDInsight с портала Azure.

1. Перейдите к действиям скрипта.

1. Выберите **Отправить новый** и ввести вход следующим образом

    | Свойство | Значение |
    | --- | --- |
    | Тип скрипта | -Таможня |
    | name |Исправление проблемы мягкого блокировки ядра |
    | URI bash-скрипта |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Типы узлов |Рабочий, зоозащитник |
    | Параметры |Недоступно |

    Выберите **упорное действие этого сценария...,** если вы хотите выполнить сценарий при добавлении новых узлов.

1. Выберите **Создать**.

1. Дождитесь успеха исполнения.

1. Выполните действие скрипта на головном узлах, следуя тем же шагам, что и шаг 3, но на этот раз с типами узлов: Head.

1. Дождитесь успеха исполнения.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
