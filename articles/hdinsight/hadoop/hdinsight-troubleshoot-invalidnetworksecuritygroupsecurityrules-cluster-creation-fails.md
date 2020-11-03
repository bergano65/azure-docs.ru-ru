---
title: Ошибка InvalidNetworkSecurityGroupSecurityRules — Azure HDInsight
description: Сбой создания кластера с InvalidNetworkSecurityGroupSecurityRules ErrorCode
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289105"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Сценарий: InvalidNetworkSecurityGroupSecurityRules — сбой создания кластера в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Вы получаете код ошибки `InvalidNetworkSecurityGroupSecurityRules` с описанием, похожим на "правила безопасности в группе безопасности сети, настроенной с подсетью, не допускают обязательного входящего и исходящего подключения".

## <a name="cause"></a>Причина

Вероятно, возникла ошибка в правилах [группы безопасности входящих сетей](../../virtual-network/virtual-network-vnet-plan-design-arm.md) , настроенных для кластера.

## <a name="resolution"></a>Решение

Перейдите в портал Azure и найдите NSG, связанный с подсетью, в которой развертывается кластер. В разделе **правила безопасности для входящего трафика** убедитесь, что правила разрешают входящий доступ к порту 443 для IP-адресов, указанных [здесь](../control-network-traffic.md).

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]