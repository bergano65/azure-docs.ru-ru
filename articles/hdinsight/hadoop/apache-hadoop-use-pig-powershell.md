---
title: Использование Apache Pig с PowerShell в HDInsight — Azure
description: Узнайте, как для отправки заданий Apache Pig в кластер Apache Hadoop в HDInsight с помощью Azure PowerShell.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 69a45a0c2c21ffafde8a4b366e1f3e90b7c8f59a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012614"
---
# <a name="use-azure-powershell-to-run-apache-pig-jobs-with-hdinsight"></a>Использование Azure PowerShell для запуска заданий Apache Pig в HDInsight

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

В этом документе приведен пример использования Azure PowerShell для отправки заданий Apache Pig, Apache hadoop, в кластере HDInsight. Pig позволяет написать задания MapReduce с использованием языка (Pig Latin), который моделирует преобразования данных, а не функции сопоставления и приведения.

> [!NOTE]  
> В этом документе не приводится подробное описание процессов, которые выполняют операторы Pig Latin, используемые в примерах. Информацию об операторах Pig Latin, используемых в этом примере, см. в статье [Использование Apache Pig с Apache Hadoop в HDInsight](hdinsight-use-pig.md).

## <a id="prereq"></a>Предварительные требования

* **Кластер Azure HDInsight**.

  > [!IMPORTANT]  
  > Linux — это единственная операционная система, используемая для работы с HDInsight 3.4 или более поздних версий. Дополнительные сведения см. в разделе [Приближается дата прекращения сопровождения HDI версии 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Рабочая станция с Azure PowerShell**.

## <a id="powershell"></a>Выполнение задания Apache Pig

Azure PowerShell предоставляет *командлеты* , позволяющие удаленно запускать задания Pig в HDInsight. Во внутренних процессах PowerShell использует вызовы REST к [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat), выполняющемуся на кластере HDInsight.

При выполнении заданий Pig на удаленном кластере HDInsight используются следующие командлеты:

* **Connect-AzureRmAccount**: выполняет аутентификацию Azure PowerShell для подписки Azure.
* **New-AzureRmHDInsightPigJobDefinition**: создает *определение задания* с использованием заданных операторов Pig Latin.
* **Start-AzureRmHDInsightJob**: отправляет определение задания в HDInsight и запускает задание. Будет возвращен объект *задания*.
* **Wait-AzureRmHDInsightJob**: проверяет состояние задания с помощью объекта-задания. Он ждет завершения задания или превышения времени ожидания.
* **Get-AzureRmHDInsightJobOutput**: используется для получения выходных данных задания.

Следующие шаги показывают, как использовать эти командлеты для выполнения задания в кластере HDInsight.

1. С помощью редактора сохраните следующий код как **pigjob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-pig/use-pig.ps1?range=5-51)]

1. Откройте командную строку Windows PowerShell. Перейдите к расположению файла **pigjob.ps1** , а затем используйте следующую команду для запуска сценария:

        .\pigjob.ps1

    Вам будет предложено войти в свою подписку Azure. Затем вам будет предложено ввести данные HTTPS, имя и пароль учетной записи администратора для кластера HDInsight.

2. После завершения задания должна быть возвращена информация следующего вида.

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="troubleshooting"></a>Устранение неполадок

Если данные не возвращаются при завершении задания, просмотрите журналы ошибок. Чтобы просмотреть информацию об ошибке для данного задания, добавьте следующую команду в конец файла **pigjob.ps1** , сохраните его, а затем запустите снова.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Этот командлет возвращает информацию, которая записывалась в STDERR на сервере при обработке задания.

## <a id="summary"></a>Сводка
Как можно видеть, Azure PowerShell позволяет с легкостью выполнять задания Pig в кластере HDInsight, отслеживать состояние задания и получать выходные данные.

## <a id="nextsteps"></a>Дальнейшие действия
Общая информация о Pig в HDInsight:

* [Использование Apache Pig с Apache Hadoop в HDInsight](hdinsight-use-pig.md)

Дополнительная информация о других способах работы с Hadoop в HDInsight:

* [Использование Apache Hive с Apache Hadoop в HDInsight](hdinsight-use-hive.md)
* [Использование MapReduce в Apache Hadoop в HDInsight](hdinsight-use-mapreduce.md)
