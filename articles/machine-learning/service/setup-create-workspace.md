---
title: Создание рабочей области
titleSuffix: Azure Machine Learning service
description: Использование портала Azure, пакет SDK, шаблона или интерфейса командной строки для создания рабочей области службы машинного обучения Azure. Эта рабочая область предоставляет централизованное место для работы с все артефакты, созданные при использовании службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 04/19/2019
ms.openlocfilehash: ca43a6cff6a32a30d93e42f6a6624439b2fe83a7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696524"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Создание рабочей области службы машинного обучения Azure

Чтобы использовать службы машинного обучения Azure, необходимо сначала [ **рабочей области машинного обучения Azure службы**](concept-azure-machine-learning-architecture.md#workspace).  Эта рабочая область — это ресурс верхнего уровня для службы и предоставляет централизованное расположение для работы с все артефакты, созданные. 

В этой статье вы узнаете, как создать рабочую область с помощью любого из этих методов: 
* [Портала Azure](#portal) интерфейса
* [Машинного обучения Azure SDK для Python](#sdk)
* Шаблон диспетчера ресурсов Azure
* [Машинном обучении Azure CLI](#cli)

Можно использовать рабочей области, созданные с использованием действия, описанные здесь в качестве необходимого компонента на другие учебники и статьи с инструкциями.

Если вы хотите использовать скрипт для настройки автоматических машинного обучения в локальной среде Python обратитесь к [GitHub Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) инструкции.  

При создании рабочей области добавляются следующие ресурсы Azure, автоматически (если они географически доступны):
 
- [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/)
- [Хранилище Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Хранилище ключей Azure](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Как в случае с другими службами Azure, со службой "Машинное обучение Azure" связаны определенные ограничения и квоты. [Узнайте больше о квотах и способах отправки запросов на получение дополнительных квот.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Технические условия
Для создания рабочей области понадобится подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

## <a name="portal"></a> Портал Azure

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Независимо от того, как он был создан, можно просмотреть рабочую область в [портала Azure](https://portal.azure.com/).  См. в разделе [просмотреть рабочую область](how-to-manage-workspace.md#view) подробные сведения.

## <a name="sdk"></a> Пакет SDK для Python

Создайте рабочую область с помощью пакета SDK для Python. Сначала необходимо установить пакет SDK.

> [!IMPORTANT]
> Установка пакета SDK следует пропустить, если вы используете виртуальную машину для обработки данных Azure или Azure Databricks.
> * Виртуальные машины для обработки и анализа данных Azure, созданные после 27 сентября 2018 г., поставляются с уже установленным пакетом SDK для Python. Пропустить установку и начать с [создать рабочую область с пакетом SDK](#sdk-create).
> * В среде Azure Databricks выполните вместо этого [шаги установки Databricks](how-to-configure-environment.md#azure-databricks).

>[!NOTE]
> Используйте эти инструкции для установки и использования пакета SDK с локального компьютера. Чтобы использовать Jupyter на удаленной виртуальной машине, настройте удаленного рабочего стола или X сеанс терминала.

Прежде чем устанавливать пакет SDK, сначала рекомендуется создать изолированную среду Python. Хотя в этой статье используется [Miniconda](https://docs.conda.io/en/latest/miniconda.html), также можно использовать полностью установленное средство [Anaconda](https://www.anaconda.com/) или [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

Инструкции в этой статье будут установлены все пакеты, необходимо запустить записные книжки краткое руководство и руководство.  Другие примеры записных книжек могут потребовать установки дополнительных компонентов.  Дополнительные сведения об этих компонентах см. в статье об [установке пакета SDK для Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Установка Miniconda

[Скачайте и установите Miniconda](https://docs.conda.io/en/latest/miniconda.html). Выберите для установки Python версии 3.7 или более поздней. Не выбирайте версию Python 2.x.  

### <a name="create-an-isolated-python-environment"></a>Создание изолированной среды Python

1. Откройте командную строку Prompt Anaconda, а затем создать новую среду conda с именем *myenv* и установите Python 3.6.5. Пакет SDK для Машинного обучения Azure будет работать с Python 3.5.2 или более поздней версии, но автоматические компоненты машинного обучения не полностью функциональны на Python 3.7.  Скачивание компонентов и пакетов и последующее создание среды займет несколько минут. 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. Активируйте среду.

    ```shell
    conda activate myenv
    ```

1. Включите ядра ipython для своей среды:

    ```shell
    conda install notebook ipykernel
    ```

    Затем создайте ядро:

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>Установка пакета SDK

1. В активированной среде conda установите основные компоненты пакета SDK для Машинного обучения Azure с возможностями записной книжки Jupyter. Установка занимает несколько минут в зависимости от конфигурации компьютера.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. Чтобы использовать эту среду для учебников по Машинному обучению Azure, установите следующие пакеты.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. Чтобы использовать эту среду для учебников по Машинному обучению Azure, установите автоматические компоненты машинного обучения.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> В некоторых программах командной строки может потребоваться добавить кавычки в следующим образом:
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> Создание рабочей области с помощью пакета SDK

Создайте рабочую область в Jupyter Notebook с помощью пакета SDK для Python.

1. Создайте каталог, который вы хотите использовать для краткого руководства и учебников, или перейдите к нему.

1. Чтобы запустить Jupyter Notebook, введите эту команду:

    ```shell
    jupyter notebook
    ```

1. В окне браузера создайте записную книжку с использованием стандартного ядра `Python 3`. 

1. Чтобы просмотреть версию пакета SDK, введите следующий код Python в ячейку записной книжки и выполните его.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Найдите значение для параметра `<azure-subscription-id>` в [списке подписок на портале Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Используйте любую подписку, в которой вам предоставлена роль владельца или участника. Дополнительные сведения о ролях см. в разделе [управление доступом к рабочей области машинного обучения Azure](how-to-assign-roles.md) статьи.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   При выполнении кода вам может быть предложено войти в учетную запись Azure. Когда вы войдете в систему, маркер проверки подлинности будет кэширован локально.

1. Чтобы просмотреть сведения о рабочей области, например связанное хранилище, реестр контейнеров и хранилище ключей, введите следующий код.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>Запись файла конфигурации

Сохраните сведения рабочей области в файле конфигурации в текущем каталоге. Этот файл называется *.azureml/config.json*.  

Этот файл конфигурации рабочей области упрощает дальнейшую загрузку этой же рабочей области. Вы можете загрузить с помощью других записных книжек и скриптов в том же каталоге или подкаталоге, используя код `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Этот вызов API `write_config()` позволяет создать файл конфигурации в текущем каталоге. Файл *config.json* содержит следующее:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Чтобы использовать рабочую область в сценарии Python или записных книжек Jupyter, расположенных в других каталогах, скопируйте этот файл в этот каталог. Файл может быть в том же каталоге, подкаталог с именем *.azureml*, или в родительском каталоге.

## <a name="resource-manager-template"></a>Шаблон Resource manager

Чтобы создать рабочую область с помощью шаблона, см. в разделе [создать рабочую область службы машинного обучения Azure с помощью шаблона](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>Интерфейс командной строки

Чтобы создать рабочую область с помощью интерфейса командной строки, см. в разделе [использовать расширение CLI для службы машинного обучения Azure](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Очистка ресурсов 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

* Независимо от того, как он был создан, можно просмотреть рабочую область в [портала Azure](https://portal.azure.com/).  См. в разделе [просмотреть рабочую область](how-to-manage-workspace.md#view) подробные сведения.

* Испытайте рабочей области с помощью этих материалов и кратких руководств.

    * Краткое руководство. [Запуск записной книжки Jupyter в облаке](quickstart-run-cloud-notebook.md).
    * Краткое руководство. [Запустите записную книжку Jupyter на собственном сервере](quickstart-run-local-notebook.md).
    * Учебник двух частей. [Обучение](tutorial-train-models-with-aml.md) и [развертывание](tutorial-deploy-models-with-aml.md) режим классификации изображений.
    * Учебник двух частей. [Подготовка данных](tutorial-data-prep.md) и [использование автоматических машинного обучения](tutorial-auto-train-models.md) для создания модели регрессии.

* Дополнительные сведения о том, как [Настройка среды разработки](how-to-configure-environment.md).

* Дополнительные сведения о [Azure Machine Learning и пакет SDK для Python](https://aka.ms/aml-sdk).
