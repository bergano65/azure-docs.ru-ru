---
title: Инвалидклассексцептион ошибка из Apache Spark — Azure HDInsight
description: Сбой задания Apache Spark с Инвалидклассексцептион, несоответствие версий класса в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 124d5586180258589c5db17454b8fbf1e465fc24
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106486"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Сбой задания Apache Spark с Инвалидклассексцептион, несоответствие версий класса в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Вы пытаетесь создать задание Apache Spark в кластере Spark 2. x. Она завершается ошибкой, как показано ниже.

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Причина:

Эта ошибка может быть вызвана добавлением дополнительного JAR-файла в `spark.yarn.jars` config, в частности затененного JAR, который включает другую версию пакета `commons-lang3` и представляет несоответствие класса. По умолчанию в Spark 2.1/2/3 используется `commons-lang3`версии 3,5.

> [!TIP]
> Чтобы затениить библиотеку, необходимо разместить ее содержимое в собственном JAR-файле, изменив его пакет. Это отличается от упаковки библиотеки, которая помещает библиотеку в собственный JAR-файл без переупаковки.

## <a name="resolution"></a>Способы устранения:

Удалите JAR-файл или выполните повторную компиляцию настроенного JAR (Азурелогаппендер) и используйте [подключаемый модуль Maven-Shader](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) для перемещения классов.

## <a name="next-steps"></a>Дополнительная информация

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. в статье [Как создать запрос на получение поддержки Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к поддержке в рамках управления подпиской и выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через тот или иной [план поддержки Azure](https://azure.microsoft.com/support/plans/).
