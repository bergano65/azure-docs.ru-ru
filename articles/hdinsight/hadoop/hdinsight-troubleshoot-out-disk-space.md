---
title: Кластерный узел иссякает из дискового пространства в Azure HDInsight
description: Проблемы устранения проблем Apache Hadoop кластерного дискового пространства в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894128"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>Сценарий: Узел кластера иссякает из дискового пространства в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Задание может выйти из строя с сообщением об ошибке, похожим на:`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

Или вы можете получить Apache Ambari оповещения похож на: `local-dirs usable space is below configured utilization percentage`.

## <a name="cause"></a>Причина

Кэш приложения Apache Yarn, возможно, потребляется все доступное пространство диска. Ваше приложение Spark, скорее всего, работает неэффективно.

## <a name="resolution"></a>Решение

1. Используйте uI Ambari, чтобы определить, в каком уеде кону заканчивается дисковое пространство.

1. Определите, какая папка в тревожном узлах вносит свой вклад в большую часть дискового пространства. SSH к узлам сперва, тогда бегите `df` для того чтобы перечислить использование диска для всех монтирует. Обычно это `/mnt` то, что темп диск, используемый OSS. Можно ввести в папку, `sudo du -hs` а затем ввести, чтобы показать суммированные размеры файлов под папкой. Если вы видите папку, похожую на, `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`это означает, что приложение все еще работает. Это может быть связано с сохранением RDD или промежуточными файлами перетасовки.

1. Чтобы смягчить проблему, убейте приложение, которое выпустит дисковое пространство, используемое этим приложением.

1. Чтобы в конечном итоге решить проблему, оптимизируйте приложение.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
