---
title: Использование MapReduce в Apache Hadoop в HDInsight
description: Узнайте, как выполнять задания Apache MapReduce на Apache Hadoop в кластерах HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 36413a4b7ba4dcb7e8e2af736a7dab6718f84799
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810478"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Использование MapReduce в Apache Hadoop в HDInsight

Узнайте, как выполнять задания MapReduce в кластерах HDInsight.

## <a id="data"></a>Демонстрационные данные

HDInsight предоставляет различные наборы демонстрационных данных, которые хранятся в каталогах `/example/data` и `/HdiSamples`. Эти каталоги находятся в хранилище по умолчанию для кластера. В этом документе мы используем файл `/example/data/gutenberg/davinci.txt`. Этот файл содержит записные книжки Леонардо Da Винчи.

## <a id="job"></a>Пример MapReduce

Пример приложения MapReduce для подсчета слов входит в состав кластера HDInsight. Этот примере расположен в каталоге `/example/jars/hadoop-mapreduce-examples.jar` в хранилище по умолчанию для кластера.

Ниже приведен исходный код Java приложения MapReduce из файла `hadoop-mapreduce-examples.jar`.

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Инструкции по написанию собственных приложений MapReduce см. в следующем документе:

* [Разработка программ MapReduce на Java для Hadoop в HDInsight на платформе Linux](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>Запуск MapReduce

HDInsight может выполнять задания HiveQL, используя различные методы. Используйте следующую таблицу, чтобы решить, какой метод подходит вам, а затем перейдите по ссылке к пошаговому руководству.

| **Используется**... | **... чтобы сделать** | ...with этим **кластером операционной системы** | ...из этого **кластера операционной системы** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Использование команды Hadoop через **SSH** |Linux |Linux, Unix, Mac OS X или Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Удаленная отправка заданий с помощью **REST** |Windows или Linux |Linux, Unix, Mac OS X или Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Удаленная отправка заданий с помощью **Windows PowerShell** |Windows или Linux |Windows |

## <a id="nextsteps"></a>Дальнейшие действия

Чтобы узнать больше о работе с данными в HDInsight, ознакомьтесь со следующими документами:

* [Разработка программ MapReduce на Java для HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j][hdinsight-use-hive]

* [Использование Pig с Hadoop в HDInsight][hdinsight-use-pig]

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs
