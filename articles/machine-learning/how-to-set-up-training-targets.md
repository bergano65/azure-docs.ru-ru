---
title: Используйте вычислительные цели для обучения модели
titleSuffix: Azure Machine Learning
description: Настройте среды обучения (целевые объекты вычислений) для обучения моделей машинного обучения. Вы можете с легкостью переключаться между средами обучения. Начните обучение на локальном компьютере. Если вам потребуется увеличить масштаб, переключитесь на облачный целевой объект вычислений.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/13/2020
ms.custom: seodec18
ms.openlocfilehash: 625efcce7305cd7b1dd415a286e6b1e92682cc0a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616848"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>Настройка и использование вычислительных целей для обучения модели 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

С помощью Azure Machine Learning вы можете обучать свою модель различным ресурсам или средам, которые в совокупности называются [__вычислительными целями.__](concept-azure-machine-learning-architecture.md#compute-targets) Они могут быть локальными или облачными, например Вычислительная среда машинного обучения Azure, Azure HDInsight или удаленная виртуальная машина.  Можно также создать целевые объекты вычислений для развертывания моделей, как описано в статье [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md).

Вы можете создать и управлять вычислительной целью с помощью SDK- машинного обучения Azure, студии машинного обучения Azure, расширения Azure CLI или Azure Machine Learning VS Code. Если у вас есть вычислительные цели, созданные через другую службу (например, кластер HDInsight), их можно использовать, прикрепив их к рабочему пространству Azure Machine Learning.
 
В этой статье вы узнаете об использовании разных целевых объектов вычисления для обучения моделей.  Шаги для всех вычислительных целей выполняются в том же рабочем процессе.
1. __Создайте__ цель вычислений, если у вас ее еще нет.
2. __Присоединение__ целевого объекта вычислений в рабочую область.
3. __Настройка__ целевого объекта вычислений так, чтобы он содержал необходимые сценарии зависимости среды и пакет Python.


>[!NOTE]
> Код в этой статье был протестирован с Azure Machine Learning SDK версии 1.0.74.

## <a name="compute-targets-for-training"></a>Целевые объекты вычислений для обучения

Машинное обучение Azure имеет различную поддержку в различных вычислительных целях. Жизненный цикл разработки обычной модели начинается с разработки и экспериментов с небольшим объемом данных. На этом этапе мы рекомендуем использовать локальную среду. Например, ваш локальный компьютер или облачную виртуальную машину. По мере перехода к обучению на больших наборах данных или распределенному обучению мы рекомендуем использовать Вычислительную среду Машинного обучения Azure, чтобы создать одно- или многоузловой кластер, который автоматически масштабируется при каждой отправке запроса на запуск. Можно также подключить собственный вычислительный ресурс, хотя не все сценарии могут поддерживаться. Подробнее об этом рассказывается ниже.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Вычислительную среду Машинного обучения Azure можно создать как постоянный ресурс или создать динамически при запросе запуска. Созданные при запуске целевые объекты вычислений будут удалены после завершения обучающего запуска, поэтому объекты вычислений, созданные таким способом, невозможно использовать повторно.

## <a name="whats-a-run-configuration"></a>Что являет собой конфигурация запуска?

При обучении этот учебный сценарий обычно запускают на локальном компьютере, а затем запускают в другом целевом объекте вычислений. С помощью Машинного обучения Azure вы можете запускать сценарий на различных вычислительных целях без изменения скрипта.

Все, что вам нужно сделать, это определить среду для каждой вычислительной цели в **конфигурации запуска.**  Затем, если вы хотите запустить обучающий эксперимент на другом целевом объекте вычислений, укажите конфигурацию запуска для этого вычисления. Для получения подробной информации об определении среды и связывании ее для выполнения конфигурации см. [Создание и управление средами для обучения и развертывания.](how-to-use-environments.md)

Дополнительные сведения об [отправке экспериментов](#submit) см. в конце этой статьи.

## <a name="whats-an-estimator"></a>Что такое оценщик?

Для облегчения обучения модели с использованием популярных фреймворков, Azure Machine Learning Python SDK предоставляет альтернативную абстракцию более высокого уровня, класс оценщика.  Этот класс позволяет легко создавать конфигурации выполнения. Вы можете создать и использовать общий [оценщик](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) для отправки обучающих скриптов, которые используют любую платформу обучения, которую вы выберете (например, scikit-learn). Мы рекомендуем использовать оценщик для обучения, поскольку он автоматически строит встроенные объекты, такие как среда или объекты RunConfiguration для вас. Если вы хотите иметь больший контроль над созданием этих объектов и указать, какие пакеты установить для выполнения эксперимента, выполните [следующие действия,](#amlcompute) чтобы представить свои учебные эксперименты с помощью объекта RunConfiguration на Azure Machine Learning Compute.

Для задач PyTorch, TensorFlow и Chainer Azure Machine Learning также предоставляет соответствующие оценки [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)и [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) для упрощения использования этих инфраструктур.

Для получения дополнительной информации, см [Поезд ML Модели с оценщиками](how-to-train-ml-models.md).

## <a name="whats-an-ml-pipeline"></a>Что такое ML трубопровод?

С помощью конвейеров ML можно оптимизировать рабочий процесс с помощью простоты, скорости, портативности и повторного использования. При создании конвейеров с помощью Azure Machine Learning вы можете сосредоточиться на своем опыте, машинном обучении, а не на инфраструктуре и автоматизации.

Конвейеры ML построены из нескольких **этапов,** которые являются отдельными вычислительными единицами в конвейере. Каждый шаг может работать независимо и использовать изолированные вычислительные ресурсы. Это позволяет нескольким ученым по обработке данных работать над один и тот же конвейер одновременно без чрезмерного налогообложения вычислительных ресурсов, а также упрощает использование различных типов вычислений/размеров для каждого шага.

> [!TIP]
> ML Pipelines может использовать конфигурацию выполнения или оценщиков при обучая моделях.

В то время как провода ML могут обучать модели, они также могут подготовить данные перед обучением и развертыванием моделей после обучения. Одним из основных случаев использования для трубопроводов является пакетный скоринг. Для получения дополнительной [информации см. Трубопроводы: Оптимизация рабочих процессов машинного обучения.](concept-ml-pipelines.md)

## <a name="set-up-in-python"></a>Настройка в Python

Чтобы настроить эти целевые объекты вычислений, используйте следующие разделы.

* [Локальный компьютер](#local)
* [Вычислительная среда Машинного обучения Azure](#amlcompute)
* [Удаленные виртуальные машины](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>Локальный компьютер

1. **Создайте и прикрепите:** Нет необходимости создавать или прикреплять вычислительную цель для использования локального компьютера в качестве учебной среды.  

1. **Настройка:** При использовании локального компьютера в качестве вычислительной цели обучаемый код работает в [среде разработки.](how-to-configure-environment.md)  Если эта среда уже содержит пакеты Python, необходимо использовать среду, управляемую пользователем.

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

Теперь, когда вы прикрепили вычисление и настроили пробег, следующим шагом будет [отправка обучаемого запуска.](#submit)

### <a name="azure-machine-learning-compute"></a><a id="amlcompute"></a>Вычислительная среда Машинного обучения Azure

Вычислительная среда Машинного обучения Azure — это управляемая вычислительная инфраструктура, которая позволяет пользователю легко создавать одно- и многоузловые вычислительные среды. Она создается в регионе вашей рабочей области и является ресурсом, который можно использовать совместно с другими пользователями в этой рабочей области. Вычислительная среда автоматически масштабируется при отправке задания, а также может быть размещена в виртуальной сети Azure. Вычисление выполняется в контейнерной среде и упаковывает зависимости модели в [контейнер Docker.](https://www.docker.com/why-docker)

Вычислительную среду Машинного обучения Azure можно использовать для распространения процесса обучения в кластере вычислительных узлов на основе ЦП или GPU, размещенном в облаке. Дополнительные сведения о размерах виртуальных машин, которые содержат GPU, см. в статье [Размеры виртуальных машин, оптимизированных для GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Вычислительная среда Машинного обучения Azure имеет ограничения по умолчанию, такие как количество ядер, которые могут быть выделены. Дополнительные сведения см. в статье [Управление квотами для ресурсов Azure и их запрашивание](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).


Можно создать Вычислительную среду Машинного обучения Azure по запросу при запланированном запуске или создать ее в качестве постоянного ресурса.

#### <a name="run-based-creation"></a>Создание при запуске

Вы можете создать Вычислительную среду Машинного обучения Azure в качестве целевого объекта вычислений во время выполнения. Вычислительная среда создается автоматически для запуска. Эта среда автоматически удаляется после завершения выполнения запуска. 

> [!IMPORTANT]
> Функция создания Вычислительной среды Машинного обучения Azure при запуске в настоящее время находится в предварительной версии. Не применяйте создание при запуске, если вы используете автоматическую настройку гиперпараметров или автоматическое машинное обучение. Чтобы использовать настройку гиперпараметров или автоматическое машинное обучение, создайте [постоянный целевой объект вычислений](#persistent) вместо этого.

1.  **Создание, присоединение и настройка:** Создание на основе запуска выполняет все необходимые шаги для создания, крепления и настройки вычислительной цели с конфигурацией выполнения.  

  [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute.py?name=run_temp_compute)]


Теперь, когда вы прикрепили вычисление и настроили пробег, следующим шагом будет [отправка обучаемого запуска.](#submit)

#### <a name="persistent-compute"></a><a id="persistent"></a>Стойкие вычисления

Постоянную Вычислительную среду машинного обучения Azure можно многократно использовать в заданиях. Ее можно использовать совместно с другими пользователями в рабочей области, и она сохраняется после выполнения заданий.

1. **Создайте и прикрепите:** Для создания постоянного ресурса Azure Machine Learning Compute в Python укажите **vm_size** и **max_nodes** свойства. Затем Машинное обучение Azure использует интеллектуальные значения по умолчанию для остальных свойств. Когда вычислительная среда не используется, она автоматически масштабируется до нуля узлов.   Выделенные виртуальные машины создаются для выполнения заданий по мере необходимости.
    
    * **vm_size**: Семейство узлов VM, созданных Azure Machine Learning Compute.
    * **max_nodes**: максимальное количество узлов для автоматического масштабирования до при запуске задания на Azure Machine Learning Compute.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   При создании Вычислительной среды машинного обучения Azure вы можете также настроить несколько дополнительных свойств. Эти свойства позволяют создать постоянный кластер фиксированного размера, который может размещаться в существующей виртуальной сети Azure в вашей подписке.  Дополнительные сведения см. в разделе о [классе AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    ).
    
   Или можно создать и прикрепить постоянный ресурс Azure Machine Learning Compute в [студии Машинного обучения Azure.](#portal-create)

1. **Настройка:** Создайте конфигурацию выполнения для постоянной вычислительной цели.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

Теперь, когда вы прикрепили вычисление и настроили пробег, следующим шагом будет [отправка обучаемого запуска.](#submit)


### <a name="remote-virtual-machines"></a><a id="vm"></a>Удаленные виртуальные машины

Машинное обучение Azure также поддерживает использование собственных вычислительных ресурсов и подключение их к рабочей области. Одним из таких типов ресурсов является произвольный удаленный VM, если он доступен из Azure Machine Learning. Ресурсом может быть виртуальная машина Azure, удаленный или локальный сервер в вашей организации. В частности, указав IP-адрес и учетные данные (имя пользователя и пароль или ключ SSH), можно использовать любую доступную виртуальную машину для удаленного запуска.

Можно использовать системную среду Conda, существующую среду Python или контейнер Docker. Для выполнения контейнера Docker на виртуальной машине должен быть запущен модуль Docker. Эта функция особенно удобна в случаях, когда необходима более гибкая облачная среда разработки и проведения экспериментов, чем локальный компьютер.

Используйте Виртуальную машину для обработки и анализа данных в качестве виртуальной машины Azure. Эта виртуальная машина является предварительно настроенной обработкой и анализом данных и средой разработки искусственного интеллекта в Azure. Виртуальная машина предлагает выбор средств и платформ для разработки машинного обучения в течение всего жизненного цикла. Дополнительных сведения об использовании DSVM со Службой машинного обучения Azure см. в статье [Настройка среды разработки для Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Создайте:** Создайте DSVM перед его использованием для обучения модели. Подробные сведения о создании этого ресурса см. в разделе [Подготовка виртуальной машины Linux (Ubuntu) для обработки и анализа данных](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Машинное обучение Azure поддерживает только виртуальные машины под управлением Ubuntu. Поэтому создаваемая виртуальная или существующая виртуальная машина, которую вы выбираете, должна работать под управлением Ubuntu.

1. **Прикрепить:** Чтобы прикрепить существующую виртуальную машину в качестве вычислительной цели, необходимо предоставить идентификатор ресурса, имя пользователя и пароль для виртуальной машины. Идентификатор ресурса VM может быть построен с помощью идентификатора подписки, имени группы ресурсов и имени VM с использованием следующего формата строки:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # If you authenticate with SSH keys instead, use this code:
   #                                                  ssh_port=22,
   #                                                  username='<username>',
   #                                                  password=None,
   #                                                  private_key_file="<path-to-file>",
   #                                                  private_key_passphrase="<passphrase>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Или вы можете прикрепить DSVM к своему рабочему пространству [с помощью студии машинного обучения Azure.](#portal-reuse)

1. **Настройка**: Создайте конфигурацию выполнения для цели вычислений DSVM. Для создания и настройки среды обучения на DSVM используются Docker и Conda.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


Теперь, когда вы прикрепили вычисление и настроили пробег, следующим шагом будет [отправка обучаемого запуска.](#submit)

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight — это популярная платформа для анализа больших данных. Она предоставляет Apache Spark, который можно использовать для обучения модели.

1. **Создайте**кластер HDInsight перед тем, как использовать его для обучения модели. Инструкции по созданию Spark в кластере HDInsight см. в статье [Краткое руководство. Создание кластера Apache Spark в HDInsight с помощью шаблона](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    При создании кластера необходимо указать имя пользователя SSH и пароль. Запишите эти значения — они вам потребуются при использовании HDInsight в качестве целевого объекта вычислений.
    
    После создания кластера подключите его, указав такое имя узла: \<имя кластера>-ssh.azurehdinsight.net, где \<имя кластера> — это имя, которое вы указали для кластера. 

1. **Прикрепить:** Чтобы прикрепить кластер HDInsight в качестве вычислительной цели, необходимо предоставить идентификатор ресурса, имя пользователя и пароль для кластера HDInsight. Идентификатор ресурсов кластера HDInsight может быть построен с помощью идентификатора подписки, имени группы ресурсов и имени кластера HDInsight с использованием следующего формата строки:`/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

   ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Или вы можете прикрепить кластер HDInsight к рабочему пространству [с помощью студии машинного обучения Azure.](#portal-reuse)

1. **Настройка:** Создайте конфигурацию выполнения для цели вычислений ИРЧП. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Теперь, когда вы прикрепили вычисление и настроили пробег, следующим шагом будет [отправка обучаемого запуска.](#submit)


### <a name="azure-batch"></a><a id="azbatch"></a>Пакет Azure 

Azure Batch используется для эффективного запуска крупномасштабных параллельных и высокопроизводительных вычислительных (HPC) приложений в облаке. AzureBatchStep можно использовать в конвейере машин ногой Azure для отправки заданий в пул машин Azure Batch.

Чтобы прикрепить Azure Batch в качестве вычислительной цели, необходимо использовать SDK для машинного обучения Azure и предоставить следующую информацию:

-    **Вычислить azure Batch**: дружественное имя, которое будет использоваться для вычислений в рабочей области
-    **Имя учетной записи Azure Batch**: Имя учетной записи Azure Batch
-    **Группа ресурсов**: Группа ресурсов, содержащая учетную запись Azure Batch.

Следующий код демонстрирует, как прикрепить Пакет Azure в качестве вычислительной цели:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

## <a name="set-up-in-azure-machine-learning-studio"></a>Настройка в студии машинного обучения Azure

Вы можете получить доступ к вычислительным целям, связанным с рабочим пространством в студии машинного обучения Azure.  Вы можете использовать студию для:

* [Просмотр целевых объектов вычислений](#portal-view), которые присоединены к рабочей области.
* [Создание целевого объекта вычислений](#portal-create) в рабочей области.
* [Присоединение целевого объекта вычислений](#portal-reuse), созданного за пределами рабочей области


После того как целевой объект будет создан и присоединен к рабочему пространству, вы будете использовать его в конфигурации запуска с объектом `ComputeTarget`. 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>Просмотр целевых объектов вычислений


Чтобы просмотреть целевые объекты вычислений для своей рабочей области, сделайте следующее:

1. Перейдите в [студию машинного обучения Azure](https://ml.azure.com).
 
1. В разделе __Приложения__ выберите __Вычисления__.

    [![Просмотр вкладки вычислений](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>Создание целевого объекта вычислений

Выполните указанные выше шаги, чтобы просмотреть список целевых объектов вычислений. Затем выполните следующие действия, чтобы создать целевой объект вычислений. 

1. Щелкните значок плюса (+), чтобы добавить целевой объект вычислений.

    ![Добавление целевого объекта вычислений](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. Введите имя для целевого объекта вычислений. 

1. Выберите **Вычислительная среда Машинного обучения** в качестве типа вычислительной среды для среды __обучения__. 

    >[!NOTE]
    >Azure Machine Learning Compute — единственный управляемый вычислительный ресурс, который можно создать в студии Машинного обучения Azure.  После создания все вычислительные ресурсы можно присоединить.

1. Заполните форму. Введите значения для обязательных свойств, в частности, укажите **семейство виртуальных машин** и **максимальное количество узлов**, до которого можно развернуть вычислительный объект.  

1. Нажмите кнопку __создания__.


1. Чтобы увидеть состояние операции создания, выберите целевой объект вычислений из списка.

    ![Выбор целевого объекта вычислений, чтобы просмотреть состояние операции создания](./media/how-to-set-up-training-targets/View_list.png)

1. Ниже приведены сведения о выбранном целевом объекте вычислений. 

    ![Просмотр сведений о целевом объекте вычислений](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>Присоединение целевых объектов вычислений

Для использования вычислительных целей, созданных за пределами рабочего пространства Azure Machine Learning, необходимо прикрепить их. Присоединение целевого объекта вычислений сделает его доступным для рабочей области.

Выполните описанные ранее шаги, чтобы просмотреть список целевых объектов вычислений. Выполните следующие действия, чтобы присоединить целевой объект вычислений. 

1. Щелкните значок плюса (+), чтобы добавить целевой объект вычислений. 
1. Введите имя для целевого объекта вычислений. 
1. Выберите тип вычислений для прикрепления для __обучения:__

    > [!IMPORTANT]
    > Не все типы вычислений могут быть прикреплены из студии Машинного обучения Azure. Типы вычислительных сред, которые в настоящее время можно подключить для обучения:
    >
    > * Удаленная виртуальная машина.
    > * Azure Databricks (для использования в конвейерах машинного обучения).
    > * Azure Data Lake Analytics (для использования в конвейерах машинного обучения).
    > * Azure HDInsight

1. Заполните форму и укажите значения для обязательных свойств.

    > [!NOTE]
    > Корпорация Майкрософт рекомендует использовать ключи SSH, так как они безопаснее, чем пароли. Пароли подвержены атакам методом подбора. Ключи SSH задействуют криптографические подписи. Сведения о создании ключей SSH для использования на Виртуальных машинах Azure см. в следующих документах:
    >
    > * [Создание и использование ключей SSH в Linux или macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Создание и использование ключей SSH в Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Выберите __Подключить__. 
1. Чтобы просмотреть состояние операции подключения, выберите целевой объект вычислений из списка.

## <a name="set-up-with-cli"></a>Настройка с ПОМОЩЬю CLI

Вы можете получить доступ к вычислительным целям, связанным с рабочим пространством, с помощью [расширения CLI](reference-azure-machine-learning-cli.md) для машинного обучения Azure.  Вы можете использовать Azure CLI для таких целей.

* Создание управляемого целевого объекта вычислений.
* Обновление управляемого целевого объекта вычислений.
* Присоединение управляемого целевого объекта вычислений.

Дополнительные сведения см. в разделе [Управление ресурсами](reference-azure-machine-learning-cli.md#resource-management).

## <a name="set-up-with-vs-code"></a>Настройка с кодом VS

Вы можете получить доступ, создать и управлять вычислительными целями, связанными с рабочим пространством, с помощью [расширения VS Code](tutorial-train-deploy-image-classification-model-vscode.md#configure-compute-targets) для машинного обучения Azure.

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Отправить учебный запуск с помощью Azure машинного обучения SDK

После создания конфигурации запуска, используйте ее для выполнения эксперимента.  Шаблон кода для отправки запуска на выполнение обучения одинаковый для всех типов целевых объектов вычислений.

1. Создание эксперимента для запуска
1. Выполните прогон.
1. Дождитесь завершения прогона.

> [!IMPORTANT]
> При отправке обучаемого запуска создается и отправляется снимок каталога, содержащего скрипты обучения, и отправляется в цель вычислений. Он также хранится в рамках эксперимента в рабочей области. Если вы измените файлы и отправите запуск снова, будут загружены только измененные файлы.
>
> Чтобы предотвратить включение файлов в моментальный снимок, создайте в каталоге [файл .gitignore](https://git-scm.com/docs/gitignore) или `.amlignore` файл и добавьте файлы в него. Файл `.amlignore` использует тот же синтаксис и шаблоны, что и файл [.gitignore.](https://git-scm.com/docs/gitignore) Если оба файла `.amlignore` существуют, файл имеет приоритет.
> 
> Дополнительные сведения см. в разделе о [моментальных снимках](concept-azure-machine-learning-architecture.md#snapshots).

### <a name="create-an-experiment"></a>Создание эксперимента

Сначала создайте эксперимент в рабочей области.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>Отправка эксперимента

Отправка эксперимента с помощью объекта `ScriptRunConfig`.  Этот объект включает в себя следующее.

* **source_directory**: Исходный каталог, содержащий сценарий обучения
* **скрипт**: Определить сценарий обучения
* **run_config**: Конфигурация запуска, которая, в свою очередь, определяет, где будет происходить обучение.

Например, чтобы использовать [локальную конфигурацию](#local) целевого объекта, выполните следующее.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

Переключите тот же эксперимент для запуска в другом целевом объекте вычислений, используя другую конфигурацию запуска, такую ​​как [amlcompute target](#amlcompute).

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> Этот пример по умолчанию использует только один узлы вычислительной цели для обучения. Чтобы использовать несколько узлов, `node_count` установите конфигурацию запуска на нужное количество узлов. Например, в следующем коде количество узлов, используемых для обучения, составляет четыре:
>
> ```python
> src.run_config.node_count = 4
> ```

Также вы можете:

* отправить эксперимент с объектом `Estimator`, как показано в разделе [Обучение моделей машинного обучения с использованием средства оценки](how-to-train-ml-models.md);
* Отправить HyperDrive запустить для [гиперпараметра настройки.](how-to-tune-hyperparameters.md)
* Отправить эксперимент через [расширение VS код](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

Для получения дополнительной информации смотрите документацию [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) и [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Создайте конфигурацию выполнения и отправьте запуск с помощью CLI машинного обучения Azure

Вы можете использовать расширение [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и [машинного обучения CLI](reference-azure-machine-learning-cli.md) для создания конфигураций выполнения и отправки запусков по различным вычислительным целям. Следующие примеры предполагают, что у вас есть существующее рабочее пространство `az login` для обучения машин Azure, и вы вошли в Azure с помощью команды CLI. 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>Создание конфигурации запуска

Самый простой способ создать конфигурацию запуска — это перейти в папку, содержащую скрипты машинного обучения Python, и использовать команду CLI

```azurecli
az ml folder attach
```

Эта команда создает субфолдер, `.azureml` содержащий файлы конфигурации выполнения шаблонов для различных вычислительных целей. Вы можете скопировать и отсеить эти файлы, чтобы настроить конфигурацию, например, для добавления пакетов Python или изменения настроек Docker.  

### <a name="structure-of-run-configuration-file"></a>Структура файла конфигурации выполнения

Файл конфигурации запуска yAML отформатирован со следующими разделами
 * Сценарий для запуска и его аргументы
 * Вычислите целевое имя, либо "местное" или имя вычисления под рабочим пространством.
 * Параметры для выполнения запуска: фреймворк, коммуникатор для распределенных запусков, максимальная продолжительность и количество вычислительных узлов.
 * Раздел окружающей среды. Смотрите [Создание и управление средами для обучения и развертывания](how-to-use-environments.md) для сведения о полях в этом разделе.
   * Чтобы указать пакеты Python для установки для запуска, создайте [файл среды conda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)и установите поле __condaDependenciesFile.__
 * Запустите сведения об истории, чтобы указать папку файлов журнала, а также включить или отключить сбор выходных данных и выполнить снимки истории.
 * Детали конфигурации, специфичные для выбранного фреймворка.
 * Справочник данных и сведения о хранилище данных.
 * Детали конфигурации, специфичные для machine Learning Compute для создания нового кластера.

Смотрите пример [файла JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) для полной схемы runconfig.

### <a name="create-an-experiment"></a>Создание эксперимента

Во-первых, создать эксперимент для ваших трасс

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>Запуск сценария

Чтобы отправить запуск сценария, выполните команду

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>ГиперДрайв

Для выполнения трасс настройки параметров можно использовать HyperDrive с Azure CLI. Во-первых, создайте файл конфигурации HyperDrive в следующем формате. [См. Hyperparameters для вашей модели](how-to-tune-hyperparameters.md) статьи для получения подробной информации о параметрах настройки гиперпарамта.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Добавьте этот файл вместе с файлами конфигурации выполнения. Затем отправьте запуск HyperDrive с помощью:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Обратите внимание на раздел *аргументов* в runconfig и *параметр пространства* в HyperDrive конфигурации. Они содержат аргументы командной строки, которые должны быть переданы в сценарий обучения. Значение в runconfig остается неизменным для каждой итерации, в то время как диапазон в конфигурации HyperDrive итерируется более. Не указывая один и тот же аргумент в обоих файлах.

Для получения более ```az ml``` подробной информации об этих командах CLI и полном наборе аргументов см. [the reference documentation](reference-azure-machine-learning-cli.md)

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Отслеживание и интеграция Git

При запуске обучаемого запуска, в котором исходный каталог является локальным репозиторием Git, информация о репозитории хранится в истории выполнения. Для получения дополнительной информации см. [интеграция Git для машинного обучения Azure.](concept-train-model-git-integration.md)

## <a name="notebook-examples"></a>Примеры записных книжек

Посмотрите примеры обучения различных целевых объектов вычислений в следующих записных книжках.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Учебник: Поезд модель](tutorial-train-models-with-aml.md) использует управляемую цель вычислений для обучения модели.
* Узнайте, как [эффективно настроить гиперпараметры](how-to-tune-hyperparameters.md) для построения лучших моделей.
* После обучения модели узнайте о [способах и расположениях развертывания моделей](how-to-deploy-and-where.md).
* Обзор справочника по пакету SDK [класса RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py).
* [Используйте машинное обучение Azure с помощью виртуальных сетей Azure](how-to-enable-virtual-network.md)
