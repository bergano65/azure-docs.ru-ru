---
title: Отправка заданий из расширения "Инструменты R для Visual Studio" в Azure HDInsight
description: Отправка заданий R с локального компьютера Visual Studio в кластер HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: 0ae717487f1538536601c8578e744d976798bf76
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899933"
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Отправка заданий из расширения "Инструменты R для Visual Studio"

[Инструменты R для Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) — это бесплатное расширение с открытым исходным кодом для выпусков Community (бесплатно), Professional и Enterprise как [Visual Studio 2017](https://www.visualstudio.com/downloads/), так и [Visual Studio 2015 с обновлением 3](https://go.microsoft.com/fwlink/?LinkId=691129) или выше. RTVS недоступен для [Visual Studio 2019](https://docs.microsoft.com/visualstudio/porting/port-migrate-and-upgrade-visual-studio-projects?view=vs-2019).

Расширение RTVS улучшает ваш рабочий процесс R, предлагая такие инструменты, как [Интерактивное окно R](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), технология IntelliSense (завершение кода), [визуализация в виде графиков](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) через библиотеки R, такие как ggplot2 и ggviz, [отладка кода R](https://docs.microsoft.com/visualstudio/rtvs/debugging) и другие.

## <a name="set-up-your-environment"></a>Настройка среды

1. Установите [Инструменты R для Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Установка RTVS в Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Выберите рабочую нагрузку *Приложения для обработки и анализа данных и аналитические приложения*, а затем выберите пункты **Поддержка языка R**, **Поддержка среды выполнения для разработки R** и **Microsoft R Client**.

3. Для проверки подлинности по SSH необходимы открытые и закрытые ключи.
   <!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Установите на компьютер [ML Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows). ML Server предоставляет функции [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) и `RxSpark`.

5. Установите [PuTTY](https://www.putty.org/), чтобы предоставить контекст вычисления для выполнения функций `RevoScaleR` из локального клиента к кластеру HDInsight.

6. Вы можете применить параметры обработки и анализа данных к среде Visual Studio, которая предоставляет новый макет рабочего пространства для инструментов R.
   1. Чтобы сохранить текущие параметры Visual Studio, используйте команду **Инструменты > Импорт и экспорт параметров**, а затем выберите **Экспортировать выбранные параметры среды** и укажите имя файла. Чтобы восстановить эти параметры, используйте ту же команду и выберите **Импортировать выбранные параметры среды**.

   2. Перейдите в пункт меню **Инструменты R**, а затем выберите **Параметры обработки и анализа данных...**

       ![Параметры обработки и анализа данных...](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

      > [!NOTE]  
      > С помощью подхода из шага 1 вы также можете сохранить и восстановить персонализированный макет обработки и анализа данных вместо того, чтобы повторять команду **Параметры обработки и анализа данных**.

## <a name="execute-local-r-methods"></a>Выполнение локальных методов R

1. Создайте кластер служб машинного обучения HDInsight.
2. Установите [расширение RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Загрузите [ZIP-файл с примерами](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Откройте `examples/Examples.sln` для запуска решения в Visual Studio.
5. Откройте файл `1-Getting Started with R.R` в папке решений `A first look at R`.
6. Начиная сверху файла, нажимайте клавиши CTRL+ENTER, чтобы по очереди отправлять каждую строку в интерактивное окно R. Для некоторых строк это может занять определенное время, так как код в них устанавливает новые пакеты.
    * Кроме того, вы можете выбрать все строки в файле R (CTRL+A) и выполнить их все (CTRL+ENTER) либо на панели инструментов выбрать значок выполнения в интерактивном режиме.
        ![Выполнение в интерактивном режиме](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Когда все строки в сценарии будут выполнены, у вас должен быть следующий результат:

    ![Рабочая область](./media/r-server-submit-jobs-r-tools-vs/visual-studio-workspace.png)

## <a name="submit-jobs-to-an-hdinsight-ml-services-cluster"></a>Отправка заданий в кластер HDInsight R для Служб машинного обучения

С помощью Microsoft ML Server или Microsoft R Client с компьютера Windows, где установлена программа PuTTY, вы можете создать контекст вычисления, который будет запускать распределенные функции `RevoScaleR` из вашего локального клиента к вашему кластеру HDInsight. Используйте `RxSpark`, чтобы создать контекст вычисления, указав свое имя пользователя, граничный узел кластера Apache Hadoop, ключи SSH и т. д.

1. В качестве адреса узла служб ML в HDInsight `CLUSTERNAME-ed-ssh.azurehdinsight.net` `CLUSTERNAME` используется имя кластера служб ml.

1. Вставьте следующий код в интерактивное окно R в Visual Studio, изменив значения переменных настройки в соответствии с вашей средой.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
      sshHostname = mySshHostname,
      sshSwitches = mySshSwitches,
      sshProfileScript = mySshProfileScript,
      consoleOutput = TRUE,
      hdfsShareDir = myHdfsShareDir,
      shareDir = myShareDir,
      sshClientDir = mySshClientDir
    )
    
    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```
    
    ![Настройка контекста Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

1. В интерактивном окне R выполните следующие команды:

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Должен отобразиться результат, аналогичный приведенному ниже:

    ![Успешное выполнение команды rx](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

1. Убедитесь, что команда `rxHadoopCopy` успешно скопировала файл `people.json` из папки данных для примера в недавно созданную папку `/user/RevoShare/newUser`:

    1. На панели кластера HDInsight для Служб машинного обучения в Azure в меню слева выберите **Учетные записи хранения**.

        ![Учетные записи хранения](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Выберите учетную запись хранения по умолчанию для своего кластера, записав имя контейнера и каталога.

    3. На панели учетных записей хранения в меню слева выберите **Контейнеры**.

        ![Контейнеры](./media/r-server-submit-jobs-r-tools-vs/hdi-storage-containers.png)

    4. Выберите имя контейнера вашего кластера, перейдите в папку **пользователя** (возможно, внизу списка нужно будет нажать *Загрузить еще*), выберите *RevoShare*, а затем — **newUser**. Файл `people.json` должен появиться в папке `newUser`.

        ![Скопированный файл](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

1. После этого остановите выполнение текущего контекста Apache Spark. Одновременное выполнение нескольких контекстов не поддерживается.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Следующие шаги

* [Варианты контекста вычислений для Служб машинного обучения в HDInsight](r-server-compute-contexts.md)
* Пример прогнозирования задержки рейсов см. в статье [Совместное использование ScaleR и SparkR в HDInsight](../hdinsight-hadoop-r-scaler-sparkr.md).

