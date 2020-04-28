---
title: Ошибка InvalidNetworkSecurityGroupSecurityRules — Azure HDInsight
description: Сбой создания кластера с InvalidNetworkSecurityGroupSecurityRules ErrorCode
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894151"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Сценарий: InvalidNetworkSecurityGroupSecurityRules — сбой создания кластера в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Вы получаете код `InvalidNetworkSecurityGroupSecurityRules` ошибки с описанием, похожим на "правила безопасности в группе безопасности сети, настроенной с подсетью, не допускают обязательного входящего и исходящего подключения".

## <a name="cause"></a>Причина:

Вероятно, возникла ошибка в правилах [группы безопасности входящих сетей](../../virtual-network/virtual-network-vnet-plan-design-arm.md) , настроенных для кластера.

## <a name="resolution"></a>Разрешение

Перейдите в портал Azure и найдите NSG, связанный с подсетью, в которой развертывается кластер. В разделе **правила безопасности для входящего трафика** убедитесь, что правила разрешают входящий доступ к порту 443 для IP-адресов, указанных [здесь](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip).

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
