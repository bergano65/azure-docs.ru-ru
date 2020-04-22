---
title: Безопасность транспортного слоя в Azure HDInsight
description: Безопасность транспортного слоя (TLS) и безопасный слой розеток (SSL) являются криптографическими протоколами, которые обеспечивают безопасность связи по компьютерной сети.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: fbe602581ebcea6385fb9cc9953d8e48272ce429
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771967"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Безопасность транспортного слоя в Azure HDInsight

Соединения с кластером HDInsight через `https://CLUSTERNAME.azurehdinsight.net` конечную точку общедоступного кластера проксиируются через узлов шлюзов кластеров. Эти соединения защищены с помощью протокола под названием TLS. Обеспечение более высоких версий TLS на шлюзах повышает безопасность этих соединений. Для получения дополнительной информации о том, почему вы должны использовать новые версии TLS, [см.](https://docs.microsoft.com/security/solving-tls1-problem)

По умолчанию кластеры Azure HDInsight принимают соединения TLS 1.2 на общедоступных конечных точках HTTPS и старые версии для обратной совместимости. Можно управлять минимальной версией TLS, поддерживаемой на узлах шлюза во время создания кластера, используя либо портал Azure, либо шаблон менеджера ресурсов. Для портала выберите версию TLS из сетевой вкладки **«Безопасность и сеть»** во время создания кластера. Для шаблона менеджера ресурсов во время развертывания используйте свойство **minSupportedTlsVersion.** Для шаблона образца [см.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls) Это свойство поддерживает три значения: "1.0", "1.1" и "1.2", которые соответствуют TLS 1.0 ", TLS 1.1" и TLS 1.2 "соответственно.

> [!IMPORTANT]
> Начиная с 30 июня 2020 года, Azure HDInsight будет применять TLS 1.2 или более поздние версии для всех соединений HTTPS. Мы рекомендуем вам убедиться, что все ваши клиенты готовы обрабатывать TLS 1.2 или более поздние версии. Для получения дополнительной [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/)информации см.

## <a name="next-steps"></a>Следующие шаги

* [Запланируйте виртуальную сеть для Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Создавайте виртуальные сети для кластеров Azure HDInsight.](hdinsight-create-virtual-network.md)
* [Группы сетевой безопасности](../virtual-network/security-overview.md).
