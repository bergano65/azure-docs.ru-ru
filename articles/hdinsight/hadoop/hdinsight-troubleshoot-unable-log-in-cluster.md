---
title: Не удалось войти в кластер Azure HDInsight.
description: Устранение неполадок, почему не удается войти в кластер Apache Hadoop в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 222c48ab2b9b718bc373e710f37f906e68bd31d4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289013"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Сценарий: не удается войти в кластер Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Не удалось войти в кластер Azure HDInsight.

## <a name="cause"></a>Причина

Причины могут отличаться. Помните, что при входе на панели мониторинга кластера или приложения используйте учетные данные для входа в кластер или HTTP. При удаленном подключении используйте учетные данные SSH или удаленного рабочего стола.

## <a name="resolution"></a>Решение

Устранить распространенные неполадки можно с помощью одного или нескольких описанных методов.

* Попробуйте открыть панель мониторинга кластера в новой вкладке браузера в режиме конфиденциальности.

* Если вы не можете отозвать учетные данные SSH, вы можете [сбросить учетные данные в пользовательском интерфейсе Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]