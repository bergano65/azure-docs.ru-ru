---
title: Зависание приложения Apache Tez в Azure HDInsight
description: Зависание приложения Apache Tez в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 4e4d1ec77d989fd354e8b3938a052be94ff4ba34
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539638"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>Сценарий: Apache Tez приложение зависает в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

После отправки задания Apache Hive из представления "Tez" состояние задания — "выполняется", но не выполняется.

## <a name="cause"></a>Причина

Отправлено слишком много заданий; длинная очередь Yarn.

## <a name="resolution"></a>Решение

Увеличьте масштаб кластера или подождите, пока очередь Yarn не заряжается.

По умолчанию `yarn.scheduler.capacity.maximum-applications` определяет максимальное число выполняющихся или ожидающих приложений, а по умолчанию — `10000` .

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подпишитесь на [@AzureSupport](https://twitter.com/azuresupport) — официальный канал Microsoft Azure для работы с клиентами. Вступайте в сообщество Azure для получения нужных ресурсов: ответов, поддержки и советов экспертов.

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка** . Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).