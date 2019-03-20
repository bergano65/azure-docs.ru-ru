---
title: Выполнение рабочих нагрузок Машинного обучения Azure с помощью автоматического машинного обучения в Apache Spark в Azure HDInsight
description: Узнайте, как выполнять рабочие нагрузки Машинного обучения Azure с помощью автоматического машинного обучения в Apache Spark в Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: 896cae9b7fc43765e340ba3b92351e04b5512efd
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762557"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Выполнение рабочих нагрузок Машинного обучения Azure с помощью автоматического машинного обучения в Apache Spark в Azure HDInsight

Машинное обучение Azure — это инструмент для совместной работы, поддерживающий функцию перетаскивания объектов и предназначенный для создания, тестирования и развертывания решений для прогнозного анализа данных. Машинное обучение Azure публикует модели как веб-службы, которые затем можно использовать в пользовательских приложениях и средствах бизнес-аналитики (например, в Excel). Автоматическое машинное обучение (AutoML) помогает создавать высококачественные модели машинного обучения с помощью интеллектуальной автоматизации и оптимизации. Автоматическое машинное обучение выбирает правильный алгоритм и гиперпараметры, которые следует использовать для решения проблем конкретных типов.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Установка Машинного обучения Azure в кластере HDInsight

> [!Note]
> Рабочая область Машинного обучения Azure в настоящее время доступна в следующих регионах: eastus, eastus2 и westcentralus. В одном из этих регионов также необходимо создать кластер HDInsight.

Общие руководства по Машинному обучению Azure и автоматическому машинному обучению см. в статьях [Руководство. Создание первого эксперимента по обработке и анализу данных в Студии машинного обучения Azure](../../machine-learning/studio/create-experiment.md) и [Руководство. Создание регрессионной модели с помощью автоматического машинного обучения](../../machine-learning/service/tutorial-auto-train-models.md).
Чтобы установить AzureML в кластере Azure HDInsight, выполните действие скрипта [install_aml](https://commonartifacts.blob.core.windows.net/automl/install_aml.sh) на головных и рабочих узлах кластера HDInsight 3.6 Spark 2.3.0 (рекомендуется). Этот скрипт можно запускать как часть процесса создания кластера или в существующем кластере с помощью портала Azure.

Дополнительные сведения о действиях скриптов см. в статье [Настройка кластеров HDInsight под управлением Linux с помощью действий сценариев](../hdinsight-hadoop-customize-cluster-linux.md). Вместе с установкой пакетов Машинного обучения Azure и зависимостей скрипт также скачивает пример Jupyter Notebook (в путь `HdiNotebooks/PySpark` хранилища по умолчанию). Этот пример Jupyter Notebook показывает, как использовать классификатор автоматического машинного обучения для решения простой проблемы классификации.

> [!Note]
> Пакеты Машинного обучения Azure устанавливаются в среду conda Python3. Установленная записная книжка Jupyter должна выполняться с помощью ядра PySpark3.

## <a name="authentication-for-workspace"></a>Аутентификация для рабочей области

Для создания рабочей области и отправки эксперимента требуется маркер аутентификации. Этот маркер можно создать с помощью [приложения Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Для создания требуемого маркера аутентификации также можно использовать [пользователя Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python), если многофакторная проверка подлинности не включена в учетной записи.  

Следующий фрагмент кода создает маркер аутентификации, используя **приложение Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
                tenant_id = '<Azure Tenant ID>',
                username = '<Azure AD Application ID>',
                password = '<Azure AD Application Key>'
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

В конфигурации автоматического машинного обучения пакету необходимо задать свойство `spark_context` для выполнения в распределенном режиме. Свойство `concurrent_iterations`, которое является максимальным числом параллельно выполняемых итераций, должно быть меньше количества ядер исполнителя для приложения Spark.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о преимуществах автоматического машинного обучения см. в [этой записи блога](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/).
* [Проект AutoML от Microsoft Research](https://www.microsoft.com/research/project/automl/)