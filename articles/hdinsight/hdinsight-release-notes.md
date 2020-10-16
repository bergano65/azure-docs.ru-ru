---
title: Заметки о выпуске Azure HDInsight
description: Последние заметки о выпуске Azure HDInsight. Ознакомьтесь с советами по разработке и подробными сведениями о Hadoop, Spark, R Server, Hive и т. д.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 616e3e6c37faa3c085b8531173b557973e09fbf8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974572"
---
# <a name="azure-hdinsight-release-notes"></a>Заметки о выпуске Azure HDInsight

Эта статья содержит сведения о **последних** обновлениях выпуска Azure HDInsight. Дополнительные сведения о предыдущих выпусках см. в статье [Заметки о выпуске для компонентов Hadoop в Azure HDInsight (архив)](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Сводка

Azure HDInsight является одной из самых популярных среди корпоративных клиентов служб для аналитики с открытым кодом в Azure.

## <a name="release-date-10082020"></a>Дата выпуска: 10/08/2020

Этот выпуск относится как к HDInsight 3,6, так и к HDInsight 4,0. Выпуск HDInsight предоставляется для разных регионов на протяжении нескольких дней. Указанная здесь дата выпуска обозначает дату выпуска для первого из регионов. Если вы не видите приведенных ниже изменений, дождитесь выхода выпуска в вашем регионе в течение нескольких дней.

## <a name="new-features"></a>Новые функции
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>Частные кластеры HDInsight без общедоступных IP-адресов и частной связи (Предварительная версия)
HDInsight теперь поддерживает создание кластеров без общедоступных IP-адресов и закрытых ссылок на кластеры в предварительной версии. Клиенты могут использовать новые дополнительные сетевые параметры, чтобы создать полностью изолированный кластер без общедоступного IP-адреса и использовать собственные закрытые конечные точки для доступа к кластеру. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Переход на масштабируемые наборы виртуальных машин Azure
Сейчас HDInsight использует для подготовки кластера виртуальные машины Azure. Начиная с этого выпуска, служба постепенно переносится в [масштабируемые наборы виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Весь процесс может занять несколько месяцев. После переноса регионов и подписок вновь созданные кластеры HDInsight будут работать в масштабируемых наборах виртуальных машин без действий клиента. Критические изменения не требуются.

## <a name="deprecation"></a>Устаревшее
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Прекращение использования кластера служб ML в HDInsight 3,6
Тип кластера служб ML HDInsight 3,6 будет иметь окончание поддержки через Dec 31 2020. После этого клиенты не будут создавать новые кластеры служб ML 3,6. Существующие кластеры будут продолжать работу без изменений, но без поддержки со стороны корпорации Майкрософт. Проверьте срок действия поддержки для версий HDInsight и типов кластеров [здесь](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions).

## <a name="behavior-changes"></a>Изменения в работе
Для этого выпуска изменение поведения не выполняется.

## <a name="upcoming-changes"></a>Предстоящие изменения
В предстоящих выпусках будут внесены описанные ниже изменения.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Возможность выбора различных размеров виртуальных машин Zookeeper для служб Spark, Hadoop и ML
Сейчас HDInsight не поддерживает настройку размера узла Zookeeper для типов кластера Spark, Hadoop и ML. По умолчанию используется A2_v2 размеров виртуальных машин/a2, которые предоставляются бесплатно. В следующем выпуске можно выбрать размер виртуальной машины Zookeeper, наиболее подходящий для вашего сценария. Zookeeper узлам с размером виртуальной машины, отличным от A2_v2/a2, будет начисляться счет. Виртуальные машины A2_v2 и a2 по-прежнему предоставляются бесплатно.

## <a name="bug-fixes"></a>Исправления ошибок
HDInsight постоянно повышает надежность и производительность кластеров. 

## <a name="component-version-change"></a>Изменение версий компонентов
В этом выпуска не вносилось изменений в версии компонентов. Текущие версии компонентов для HDInsight 4,0 и HDInsight 3,6 можно найти в [этом документе](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).