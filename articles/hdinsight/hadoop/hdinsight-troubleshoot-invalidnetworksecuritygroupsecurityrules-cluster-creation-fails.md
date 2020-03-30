---
title: Ошибка НедействительнойСетевойгруппгруппыБезопасностиПравила ошибка - Azure HDInsight
description: Кластер Создание не удается с ошибкойCode InvalidNetworkSecuritySecuritySecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: a73e1e9f7a9c017dd29b627a24c25ae2e064c0a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894151"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Сценарий: InvalidNetworkSecuritySecuritySecurityRules - создание кластера не удается в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Вы получаете `InvalidNetworkSecurityGroupSecurityRules` код ошибки с описанием, похожим на "Правила безопасности в группе сетевой безопасности, настроенные с подсетью, не позволяют входить в систему и/или исходящие подключения".

## <a name="cause"></a>Причина

Скорее всего, проблема с входящими [правилами группы сетевой безопасности,](../../virtual-network/virtual-network-vnet-plan-design-arm.md) настроенной для кластера.

## <a name="resolution"></a>Решение

Перейдите на портал Azure и определите NSG, которая связана с подсетью, в которой развертывается кластер. В разделе **Правила безопасности Входящих** убедитесь, что правила позволяют входящий доступ к порту 443 для IP-адресов, упомянутых [здесь.](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
