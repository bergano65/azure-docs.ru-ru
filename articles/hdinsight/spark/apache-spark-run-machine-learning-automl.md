---
title: Выполнить рабочие нагрузки По грузоподъемности Машинного обучения Azure на Apache Spark в HDInsight
description: Узнайте, как выполнять рабочие нагрузки Машинного обучения Azure с помощью автоматического машинного обучения в Apache Spark в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: 6fc0d4cfe29e0fb189c44b307576bd08d2da8a31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638890"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Выполнить рабочие нагрузки машинного обучения Azure с автоматизированным машинным обучением на Apache Spark в HDInsight

Azure Machine Learning упрощает и ускоряет создание, обучение и развертывание моделей машинного обучения. В автоматизированном машинном обучении (AutoML) вы начинаете с обучающих данных, которые имеют определенную целевую функцию, а затем итерируете с помощью комбинаций алгоритмов и выбора функций, чтобы автоматически выбрать лучшую модель для ваших данных на основе результатов обучения. HDInsight позволяет клиентам предоставлять кластеры с сотнями узлов. AutoML работает на Spark в кластере HDInsight позволяет пользователям использовать вычислительную емкость через эти узлы для выполнения обучающих заданий в масштабе, а также для выполнения нескольких учебных заданий параллельно. Это позволяет пользователям запускать эксперименты AutoML при совместном использовании вычислений с другими рабочими нагрузками больших данных.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Установка Машинного обучения Azure в кластере HDInsight

Для общих учебников автоматизированного машинного обучения см. [Tutorial: Используйте автоматизированное машинное обучение для построения модели регрессии.](../../machine-learning/tutorial-auto-train-models.md)
Все новые кластеры HDInsight-Spark поставляются с предустановленной установкой AzureML-AutoML SDK.

> [!Note]
> Пакеты Машинного обучения Azure устанавливаются в среду conda Python3. Установленная записная книжка Jupyter должна выполняться с помощью ядра PySpark3.

Вы можете использовать ноутбуки zeppelin для использования AutoML, а также.

> [!Note]
> У Цеппелина есть [известная проблема,](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) в которой PySpark3 не выбирает правильную версию Python. Пожалуйста, используйте документированный обход.

## <a name="authentication-for-workspace"></a>Аутентификация для рабочей области

Для создания рабочей области и отправки эксперимента требуется маркер аутентификации. Этот маркер можно создать с помощью [приложения Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Для создания требуемого маркера аутентификации также можно использовать [пользователя Azure AD](/azure/python/python-sdk-azure-authenticate), если многофакторная проверка подлинности не включена в учетной записи.  

Следующий фрагмент кода создает маркер аутентификации, используя **приложение Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```

Следующий фрагмент кода создает маркер аутентификации, используя **пользователя Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Загрузка набора данных

Автоматическое машинное обучение в Spark использует **потоки данных**, которые являются неизменяемыми операциями с данными с отложенным вычислением.  Поток данных может загрузить набор данных из большого двоичного объекта с общедоступным доступом на чтение или с URL-адреса большого двоичного объекта с маркером SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Вы также можете зарегистрировать хранилище данных в рабочей области с использованием однократной регистрации.

## <a name="experiment-submission"></a>Отправка эксперимента

В [автоматизированной конфигурации машинного обучения](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)свойство `spark_context` должно быть установлено для выполнения пакетом в распределенном режиме. Свойство `concurrent_iterations`, которое является максимальным числом параллельно выполняемых итераций, должно быть меньше количества ядер исполнителя для приложения Spark.

## <a name="next-steps"></a>Дальнейшие действия

* Для получения дополнительной информации о мотивации автоматизированного машинного обучения, см [Релиз модели в темпе с помощью автоматизированного машинного обучения Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Для получения дополнительной информации об использовании возможностей Azure ML Automated ML [см.](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Проект AutoML от Microsoft Research](https://www.microsoft.com/research/project/automl/)
