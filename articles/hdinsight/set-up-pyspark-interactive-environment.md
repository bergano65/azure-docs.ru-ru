---
title: Инструменты Azure HDInsight. Настройка интерактивной среды PySpark для Visual Studio Code
description: Сведения о создании и отправке запросов и скриптов с помощью средств Azure HDInsight для Visual Studio Code.
keywords: VScode,средства Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: d7d6bf45a8cdf22fbff0d3fda8175e8007a612d9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65789278"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Настройка интерактивной среды PySpark для Visual Studio Code

Следующие шаги показывают, как настроить интерактивную среду PySpark в VS Code.

Мы используем команду **python или pip** для сборки виртуального окружения в основной папке. Если вы хотите использовать другую версию, вам нужно вручную изменить версию по умолчанию команды **python или pip**. Дополнительные сведения см. в статье о команде [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Установите [Python](https://www.python.org/downloads/) и [pip](https://pip.pypa.io/en/stable/installing/).
   
   + Установите Python отсюда: [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Установите pip отсюда: [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/). (Если он не установился при установке Python)
   + Проверьте правильность установки Python и pip с помощью следующих команд. (Дополнительно)
 
        ![Версия pip для Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Мы советуем вручную устанавливать Python вместо использования версии по умолчанию для MacOS.


2. Установите **virtualenv**, выполнив команду, приведенную ниже.
   
   ```
   pip install virtualenv
   ```

3. Только для Linux: установите необходимые пакеты, выполнив команды, приведенные ниже, если вы столкнулись с сообщением об ошибке.
   
    ![Версия pip для Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Перезапустите VS Code, а затем снова перейдите к редактору сценариев, который работает на базе **интерактивной среды PySpark HDInsight**.

## <a name="next-steps"></a>Дальнейшие действия

### <a name="demo"></a>Демонстрация
* HDInsight для VS Code: [Видео](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Средства и расширения
* [Использование средств Azure HDInsight для Visual Studio Code](hdinsight-for-vscode.md)
* [Создание приложений Apache Spark для кластера HDInsight с помощью Azure Toolkit for IntelliJ](spark/apache-spark-intellij-tool-plugin.md)
* [Удаленная или локальная отладка приложений Spark в кластере HDInsight с помощью Azure Toolkit for IntelliJ через SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Удаленная отладка приложений Spark в HDInsight через VPN с помощью Azure Toolkit for IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Использование средств HDInsight в Azure Toolkit for Eclipse для создания приложений Apache Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Использование инструментов HDInsight для IntelliJ с песочницей Hortonworks](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Использование записных книжек Zeppelin с кластером Apache Spark в Azure HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Ядра для записной книжки Jupyter в кластерах Spark в Azure HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Использование внешних пакетов с записными книжками Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Установка записной книжки Jupyter на компьютере и ее подключение к кластеру Apache Spark в Azure HDInsight (предварительная версия)](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Визуализация данных Apache Hive с Microsoft Power BI с использованием ODBC в Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [С помощью Apache Zeppelin для выполнения запросов Apache Hive в Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
