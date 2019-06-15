---
title: Выполнение рабочих нагрузок Машинного обучения Azure с помощью автоматического машинного обучения в Apache Spark в Azure HDInsight
description: Узнайте, как выполнять рабочие нагрузки Машинного обучения Azure с помощью автоматического машинного обучения в Apache Spark в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 5135de0fc87af227073f96c653d928ace1a50fd0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64917041"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Выполнение рабочих нагрузок Машинного обучения Azure с помощью автоматического машинного обучения в Apache Spark в Azure HDInsight

Машинное обучение Azure упрощает и ускоряет создание, обучение и развертывания моделей машинного обучения. В автоматических в машинном обучении (AutoML) начинаться с обучающих данных, которые есть определенная функция и затем выполните итерацию через сочетания алгоритмов и выбора компонентов, чтобы автоматически выбрать оптимальную модель на основе этой оценки обучающих данных. HDInsight позволяет клиентам Подготовка кластеров с сотнями узлов. AutoML под управлением Spark в кластере HDInsight позволяет пользователям использовать вычислительные мощности по этим узлам для запуска заданий обучения в режиме горизонтального масштабирования и для параллельного выполнения нескольких заданий обучения. Это позволяет пользователям запускать эксперименты AutoML при совместном использовании вычислительных ресурсов с их для других рабочих нагрузок больших данных.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Установка Машинного обучения Azure в кластере HDInsight

Общие руководства автоматических машинного обучения см. в разделе [руководства: Создание регрессионной модели с помощью автоматического машинного обучения](../../machine-learning/service/tutorial-auto-train-models.md).
Все новые кластеры HDInsight Spark устанавливаются с помощью пакета SDK AutoML машинного обучения Azure. Вы можете начать работу с AutoML на HDInsight с этим [пример Jupyter notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). Этот пример Jupyter Notebook показывает, как использовать классификатор автоматического машинного обучения для решения простой проблемы классификации.

> [!Note]
> Пакеты Машинного обучения Azure устанавливаются в среду conda Python3. Установленная записная книжка Jupyter должна выполняться с помощью ядра PySpark3.

Также можно использовать записные книжки Zeppelin также использовать AutoML.

> [!Note]
> Имеет Zeppelin [известная проблема](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) котором PySpark3 не выбрать нужную версию Python. Используйте документированных временного решения.

## <a name="authentication-for-workspace"></a>Аутентификация для рабочей области

Для создания рабочей области и отправки эксперимента требуется маркер аутентификации. Этот маркер можно создать с помощью [приложения Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Для создания требуемого маркера аутентификации также можно использовать [пользователя Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python), если многофакторная проверка подлинности не включена в учетной записи.  

Следующий фрагмент кода создает маркер аутентификации, используя **приложение Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                service_principal_id = '<Azure AD Application ID>',
                service_principal_password = '<Azure AD Application Key>'
                )
```
Следующий фрагмент кода создает маркер аутентификации, используя **пользователя Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com','my_smart_password')
```

## <a name="loading-dataset"></a>Загрузка набора данных

Автоматическое машинное обучение в Spark использует **потоки данных**, которые являются неизменяемыми операциями с данными с отложенным вычислением.  Поток данных может загрузить набор данных из большого двоичного объекта с общедоступным доступом на чтение или с URL-адреса большого двоичного объекта с маркером SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Вы также можете зарегистрировать хранилище данных в рабочей области с использованием однократной регистрации.

## <a name="experiment-submission"></a>Отправка эксперимента

В [конфигурация автоматических машины обучения](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), свойство `spark_context` должно задаваться для выполнение пакета в распределенном режиме. Свойство `concurrent_iterations`, которое является максимальным числом параллельно выполняемых итераций, должно быть меньше количества ядер исполнителя для приложения Spark.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о стимулов автоматических машинного обучения, см. в разделе [моделей выпуска на скорости с помощью корпорации Майкрософт автоматической машинного обучения!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Дополнительные сведения об использовании возможностей автоматизированные службы машинного Обучения Azure ML см. в разделе [New автоматической возможности обучения машины в службе машинного обучения Azure](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Проект AutoML от Microsoft Research](https://www.microsoft.com/research/project/automl/)
