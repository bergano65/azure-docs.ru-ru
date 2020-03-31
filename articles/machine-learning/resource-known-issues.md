---
title: Известные проблемы и устранение неполадок
titleSuffix: Azure Machine Learning
description: Получите список известных проблем, обходных пути и устранения неполадок для машинного обучения Azure.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5525c02edb30eff0ee8971a382f2acb8f2e57ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455729"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Известные проблемы и устранение неполадок в машинном обучении Azure

Эта статья поможет вам найти и исправить ошибки или сбои, возникающие при использовании машинного обучения Azure.

## <a name="sdk-installation-issues"></a>Проблемы с установкой пакета SDK

**Сообщение об ошибке: не удается удалить "PyYAML"**

Azure Machine Learning SDK для Python: PyYAML является проектом, установленным distutils. Поэтому невозможно точно определить, какие файлы принадлежат ему в случае частичного удаления. Чтобы продолжить установку SDK игнорируя эту ошибку, используйте:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Сообщение об ошибке:`ERROR: No matching distribution found for azureml-dataprep-native`**

В дистрибутиве Python 3.7.4 Anaconda есть ошибка, которая ломает установку azureml-sdk. Эта проблема обсуждается в данном [выпуске GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195) Это можно обойти, создав новую среду Conda с помощью этой команды:
```bash
conda create -n <env-name> python=3.7.3
```
Что создает среду Conda с помощью Python 3.7.3, который не имеет проблемы с установкой присутствует в 3.7.4.

## <a name="training-and-experimentation-issues"></a>Вопросы обучения и экспериментов

### <a name="metric-document-is-too-large"></a>Метрический документ слишком велик
Azure Machine Learning имеет внутренние ограничения на размер метрических объектов, которые могут быть зарегистрированы сразу с обучаемого запуска. Если при регистрации метрика, оцениваемого в списке, вы сталкиваетесь с ошибкой «Метриум документ слишком велик», попробуйте разделить список на более мелкие части, например:

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

Внутренне Azure ML конкатирует блоки с тем же метрическим именем в смежный список.

### <a name="moduleerrors-no-module-named"></a>ModuleErrors (Не назван модуль)
Если вы столкнулись с ModuleErrors во время отправки экспериментов в Azure ML, это означает, что обучающий скрипт ожидает установку пакета, но он не добавлен. Как только вы предоставите название пакета, Azure ML установит пакет в среду, используемую для обучения. 

Если вы используете [Оценщиков для отправки экспериментов,](concept-azure-machine-learning-architecture.md#estimators) вы можете указать имя пакета через `pip_packages` или `conda_packages` параметр в оценщике на основе того, из какого источника вы хотите установить пакет. Вы также можете указать файл yml `conda_dependencies_file`со всеми вашими зависимостями, использующими `pip_requirements_file` или перечислить все ваши требования пипса в файле txt с использованием параметра. Если у вас есть собственный объект Azure ML Environment, который требуется переопределить изображение по `environment` умолчанию, используемое оценщиком, можно указать эту среду по параметру конструктора оценщика.

Azure ML также предоставляет специальные оценщики для Tensorflow, PyTorch, Chainer и SKLearn. Использование этих оценщиков гарантирует, что основные зависимости фреймворка устанавливаются от вашего имени в среде, используемой для обучения. У вас есть возможность указать дополнительные зависимости, как описано выше. 
 
Azure ML поддерживает докер изображения и их содержимое можно увидеть в [AzureML контейнеров](https://github.com/Azure/AzureML-Containers).
В соответствующей рамочной документации перечислены рамочные зависимости - [Chainer,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks) [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks) [TensorFlow,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks) [SKLearn.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks)

> [!Note]
> Если вы считаете, что определенный пакет достаточно распространен для добавления в сохраненные в Azure ML изображения и среды, пожалуйста, поднимите проблему GitHub в [контейнерах AzureML.](https://github.com/Azure/AzureML-Containers) 
 
### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>NameError (Имя не определено), AttributeError (Объект не имеет атрибута)
Это исключение должно исходить из ваших сценариев обучения. Вы можете посмотреть файлы журнала с портала Azure, чтобы получить более подробную информацию о конкретном имени, не определенном или атрибуте ошибки. Из SDK можно использовать `run.get_details()` для поиска сообщения об ошибке. В этом также будет указаны все файлы журнала, созданные для выполнения. Пожалуйста, не забудьте взглянуть на ваш сценарий обучения и исправить ошибку, прежде чем повторно отправлять запустить. 

### <a name="horovod-has-been-shut-down"></a>Хорошоватова закрыли
В большинстве случаев, если вы столкнулись с "AbortedError: Horovod был закрыт" это исключение означает, что было основное исключение в одном из процессов, которые привели к закрытию Гороводова. Каждое звание в заданиях MPI получает свой собственный файл журнала в Azure ML. Эти журналы `70_driver_logs`названы . В случае распределенного обучения, имена журналов суффиксируются с, `_rank` чтобы было легче дифференцировать журналы. Чтобы найти точную ошибку, которая привела к отключению Горовода, пройдите все файлы журнала и ищите `Traceback` в конце driver_log файлов. Один из этих файлов даст вам фактическое основное исключение. 

### <a name="sr-iov-availability-on-ncv3-machines-in-amlcompute-for-distributed-training"></a>Доступность SR-IOV на машинах NCv3 в AmlCompute для распределенного обучения
Azure Compute внедряет [обновление SR-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku/) машин NCv3, которое клиенты могут использовать с помощью управляемого вычислительного предложения Azure ML (AmlCompute). Обновления позволят поддерживать весь стек MPI и использовать сеть Infiniband RDMA для улучшения мультиузловой распределенной производительности обучения, особенно для глубокого обучения.

Просмотр [расписания](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku/) обновлений, чтобы узнать, когда поддержка будет развернута для вашего региона.

### <a name="run-or-experiment-deletion"></a>Выполнить или поэкспериментировать удаление
Эксперименты можно архивировать с помощью метода [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) или из представления вкладки Experiment в студии Azure Machine Learning с помощью кнопки «Архивный эксперимент». Это действие скрывает эксперимент от запросов и представлений списка, но не удаляет его.

Постоянное удаление отдельных экспериментов или запусков в настоящее время не поддерживается. Для получения дополнительной информации об удалении ресурсов Рабочего пространства [см.](how-to-export-delete-data.md)

## <a name="azure-machine-learning-compute-issues"></a>Проблемы с машинным обучением Azure
Известные проблемы с использованием Azure Machine Learning Compute (AmlCompute).

### <a name="trouble-creating-amlcompute"></a>Проблемы с созданием AmlCompute

Существует редкая вероятность того, что некоторые пользователи, создавшие рабочее пространство Azure Machine Learning с портала Azure до выпуска GA, не смогут создать AmlCompute в этом рабочем пространстве. Вы можете либо повысить запрос на поддержку службы, либо создать новое рабочее пространство через портал или SDK, чтобы немедленно разблокировать себя.

### <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Простой: SR-IOV обновление до nCv3 машин в AmlCompute

Azure Compute будет обновлять NCv3 SKUs с начала ноября 2019 года для поддержки всех реализаций и версий MPI, а также глаголов RDMA для виртуальных машин, оснащенных InfiniBand. Для этого потребуется короткое время простоя - [подробнее о обновлении SR-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Как клиент управляемого вычислительного предложения Azure Machine Learning (AmlCompute), в настоящее время от вас не требуется вносить какие-либо изменения. Основываясь на [графике обновления,](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) вам нужно будет спланировать короткий перерыв в обучении. Служба возьмет на себя ответственность за обновление vM-изображений на кластерных узлах и автоматические масштабирование кластера. После завершения обновления вы сможете использовать все другие дистрибутивы MPI (например, OpenMPI с Pytorch), кроме получения более высокой пропускной способности InfiniBand, более низкой просрочности и лучшей производительности приложений.

## <a name="azure-machine-learning-designer-issues"></a>Проблемы дизайнера машинного обучения Azure

Известные проблемы с дизайнером.

### <a name="long-compute-preparation-time"></a>Долгое время подготовки вычислений

Создание новых вычислений или вызвать выход из расчета занимает много времени, может быть несколько минут или даже дольше. Команда работает над оптимизацией.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Не удается запустить эксперимент только содержит набор данных 

Возможно, вы хотите запустить эксперимент только содержит набор данных для визуализации набора данных. Тем не менее, не разрешается запускать эксперимент только содержит набор данных сегодня. Мы активно исправляем эту проблему.
 
Перед исправлением можно подключить набор данных к любому модулю преобразования данных (Выберите столбцы в наборе данных, Edit Metadata, Split Data и т.д.) и запустить эксперимент. Затем можно визуализировать набор данных. 

Ниже изображение показывает, как: ![висулизировать данные](./media/resource-known-issues/aml-visualize-data.png)

## <a name="image-building-failure"></a>Сбой создания образа

Сбой создания образа при развертывании веб-службы. Обходным решением является добавление "pynacl==1.2.1" в качестве зависимости пика для файла Conda для конфигурации изображения.

## <a name="deployment-failure"></a>Сбой развертывания

Если отображается `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, измените номер SKU для виртуальных машин, используемых в развертывании, на другой с большим объемом памяти.

## <a name="fpgas"></a>FPGA

Вы не сможете развернуть модели на FPGA до тех пор, пока не будет запрошена и одобрена квота FPGA. Чтобы запросить доступ, заполните форму запроса квоты: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Автоматическое машинное обучение

В настоящее время Тензор Фотль Автоматизированное машинное обучение не поддерживает версию потока 1.13. Установка этой версии приведет к прекращению работы зависимостей пакетов. Мы работаем, чтобы исправить эту проблему в будущем выпуске.

### <a name="experiment-charts"></a>Диаграммы эксперимента

Двоичные диаграммы классификации (точность отзыва, ROC, кривая усиления и т.д.), показанные в автоматизированных итерациях эксперимента ML, не правильно отображаются в пользовательском интерфейсе с 4/12. Диаграммы в настоящее время показывают обратные результаты, где более эффективные модели показаны с более низкими результатами. В настоящее время проводится расследование в отношении решения.

## <a name="datasets-and-data-preparation"></a>Наборы данных и подготовка данных

Это известные проблемы для наборов данных машинного обучения Azure.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>TypeError: FileNotFound: Нет такого файла или каталога

Эта ошибка возникает, если путь файла, который вы предоставляете, не находится там, где находится файл. Необходимо убедиться, что способ отсылки к файлу соответствует тому, где вы смонтировали набор данных в вычислительной цели. Для обеспечения детерминированного состояния мы рекомендуем использовать абстрактный путь при установке набора данных в вычислительную цель. Например, в следующем коде мы устанавливаем набор данных под корень `/tmp`файловой системы вычислительной цели. 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Если вы не включаете ведущую передний слэш,'/', вам нужно префиксировать рабочий каталог, например, `/mnt/batch/.../tmp/dataset` на цель вычисления, чтобы указать, где вы хотите, чтобы набор данных был установлен.

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Не прочитайте файл паркета из HTTP или ADLS Gen 2

Существует известная проблема в версии 1.1.25 AzureML DataPrep SDK, которая вызывает сбой при создании набора данных, читая файлы Паркета из HTTP или ADLS Gen 2. Он не `Cannot seek once reading started.`сможет с . Чтобы исправить эту проблему, пожалуйста, пересмотрите `azureml-dataprep` до версии выше 1.1.26, или понизить до версии ниже, чем 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: горе () получил неожиданный аргумент ключевого слова "invocation_id"

Эта ошибка возникает, если у `azureml-core` вас `azureml-dataprep`есть несовместимая версия между и . Если вы видите `azureml-dataprep` эту ошибку, обновить пакет до более новой версии (больше, чем или равна 1.1.29).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Ниже описаны проблемы, которые могут возникать в службе "Машинное обучение Azure" и Databricks.

### <a name="failure-when-installing-packages"></a>Сбой при установке пакетов

Установка Azure Machine Learning SDK выходит из строя на Azure Databricks при установке дополнительных пакетов. Некоторые пакеты, такие как `psutil`, могут приводить к конфликтам. Чтобы избежать ошибок установки, установите пакеты, заморозив версию библиотеки. Эта проблема связана с Databricks, а не с SDK для машинного обучения Azure. Вы можете возникнуть эта проблема с другими библиотеками, тоже. Пример

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Кроме того, можно использовать сценарии init, если вы продолжаете сталкиваться с проблемами установки с библиотеками Python. Этот подход официально не поддерживается. Для получения дополнительной [информации](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)см.

### <a name="cancel-an-automated-machine-learning-run"></a>Отмена автоматического запуска машинного обучения

При использовании возможностей автоматизированного машинного обучения в Azure Databricks, чтобы отменить запуск и запустить новый запуск эксперимента, перезапустите кластер Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>>10 итераций для автоматизированного машинного обучения

В автоматизированных настройках машинного обучения, если у вас `show_output` `False` есть более 10 итераций, установить, когда вы отправляете запустить.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Виджет для SDK машинного обучения Azure и автоматизированного машинного обучения

Виджет Azure Machine Learning SDK не поддерживается в блокноте Databricks, поскольку ноутбуки не могут разбирать HTML виджеты. Просмотреть виджет можно на портале, используя этот код Python в блокнотной ячейке Azure Databricks:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-cannot-import-name-timedelta-from-pandas_libstslibs"></a>Ошибка импорта: не может импортировать название "Timedelta" из 'pandas._libs.tslibs'

Если вы видите эту ошибку при использовании автоматизированного машинного обучения, запустите две следующие строки в блокноте:
```
%sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
%sh /databricks/python/bin/pip install pandas==0.23.4
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Ошибка импорта: Нет модуля под названием 'pandas.core.indexes'

Если вы видите эту ошибку при использовании автоматизированного машинного обучения:

1. Запустите эту команду для установки двух пакетов в кластер Azure Databricks:

   ```bash
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Отсоедините, а затем прикрепите кластер к блокноту.

Если эти шаги не решат проблему, попробуйте перезапустить кластер.

### <a name="failtosendfeather"></a>FailToSendFeather

Если вы `FailToSendFeather` видите ошибку при чтении данных в кластере Azure Databricks, обратитесь к следующим решениям:

* Обновление `azureml-sdk[automl]` пакета до последней версии.
* Добавьте `azureml-dataprep` версию 1.1.8 или выше.
* Добавьте `pyarrow` версию 0.11 или выше.

## <a name="azure-portal"></a>Портал Azure

При переходе непосредственно к просмотру рабочей области с помощью ссылки для общего доступа из пакета SDK или на портале невозможно будет отобразить обычную страницу обзора со сведениями о подписке в расширении. Кроме того, вы не сможете переключиться на другую рабочую область. Если вам нужно просмотреть другое рабочее пространство, решение заключается в том, чтобы перейти непосредственно к [студии машинного обучения Azure](https://ml.azure.com) и поиск имени рабочего пространства.

## <a name="diagnostic-logs"></a>Журналы диагностики

Иногда при обращении за помощью полезно предоставить диагностические сведения. Чтобы увидеть некоторые журналы, посетите [студию машинного обучения Azure](https://ml.azure.com) и перейдите в рабочее пространство и выберите **Рабочее пространство > Эксперимент > запустите журналы >.**  

> [!NOTE]
> В ходе обучения Azure Machine Learning регистрирует информацию из различных источников, таких как AutoML или контейнер Docker, который выполняет учебную работу. Многие из этих журналов не документированы. Если вы столкнулись с проблемами и обратились в службу поддержки майкрософт, они смогут использовать эти журналы во время устранения неполадок.

## <a name="activity-logs"></a>Журналы действий

Некоторые действия в рабочей области Azure Machine Learning не регистрировать информацию в __журнале activity.__ Например, запуск обучающего запуска или регистрация модели.

Некоторые из этих действий отображаются в области __действий__ в рабочей области, однако они не указывают, кто инициировал действие.

## <a name="resource-quotas"></a>Квоты ресурсов

Дополнительные сведения о квотах ресурсов в службе "Машинное обучение Azure" см. [здесь](how-to-manage-quotas.md).

## <a name="authentication-errors"></a>Ошибки проверки подлинности

При выполнении операции управления на целевом объекте вычислений из удаленного задания вы получите одну из следующих ошибок:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Например, вы получите сообщение об ошибке, если попытаетесь создать или вложить целевой объект вычислений из конвейера Машинного обучения, который передается для удаленного выполнения.

## <a name="overloaded-azurefile-storage"></a>Перегруженное хранилище AzureFile

Если вы получили `Unable to upload project files to working directory in AzureFile because the storage is overloaded`ошибку, применить следующие обходные пути.

Если вы используете общую часть файлов для других рабочих нагрузок, таких как передача данных, рекомендуется использовать капли, чтобы доля файлов была бесплатной для отправки. Можно также разделить рабочую нагрузку между двумя различными рабочими пространствами.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Сбои web-сервисов в сбоях службы Azure Kubernetes

Многие сбои веб-службы в службе Azure Kubernetes можно `kubectl`отладить, подключившись к кластеру с помощью. Вы можете `kubeconfig.json` получить для кластера обслуживания Azure Kubernetes, запустив

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Обновление компонентов машинного обучения Azure в кластере AKS

Обновления компонентов машинного обучения Azure, установленные в кластере службы Azure Kubernetes, должны применяться вручную. 

Эти обновления можно применить, отделив кластер из рабочего пространства Машинного обучения Azure, а затем прикрепив кластер к рабочему пространству. Если SSL включен в кластере, при повторном подключении кластера необходимо предоставить сертификат SSL и закрытый ключ. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Если у вас больше нет sSL-сертификата и закрытого ключа, или вы используете сертификат, генерируемый Azure Machine Learning, вы `kubectl` можете получить `azuremlfessl`файлы до отсоединения кластера, подключившись к кластеру с помощью и извлечения секрета.

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes хранит секреты в закодированном формате базы-64. Вам нужно будет базы-64 `cert.pem` расшифровать и `key.pem` компоненты `attach_config.enable_ssl`секретов до предоставления их . 

## <a name="labeling-projects-issues"></a>Проблемы с маркировкой проектов

Известные проблемы с проектами маркировки.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Можно использовать только наборы данных, созданные в хранилищах данных blob

Это известное ограничение текущего выпуска.

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>После создания, проект показывает "Инициализация" в течение длительного времени

Вручную обновите страницу. Инициализация должна осуществляться примерно со скоростью 20 точек данных в секунду. Отсутствие автообновления является известной проблемой. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>При просмотре изображений недавно помеченные изображения не отображаются

Чтобы загрузить все помеченные изображения, выберите **первую** кнопку. **Первая** кнопка передаст вас обратно в передней части списка, но загружает все помеченные данные.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>Нажатие клавиши Esc при маркировке для обнаружения объектов создает метку нулевого размера в левом верхнем углу. Отправка меток в этом состоянии не удается.

Удалите метку, нажав на перекрестную отметку рядом с ней.

## <a name="moving-the-workspace"></a>Перемещение рабочего пространства

> [!WARNING]
> Перемещение рабочего пространства Azure Machine Learning в другую подписку или перемещение подписки на новый арендатор не поддерживается. Это может привести к ошибкам.
