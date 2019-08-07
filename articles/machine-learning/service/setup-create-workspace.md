---
title: Создать рабочую область
titleSuffix: Azure Machine Learning service
description: Используйте портал Azure, пакет SDK, шаблон или интерфейс командной строки для создания рабочей области службы Машинное обучение Azure. Эта Рабочая область предоставляет централизованное место для работы со всеми артефактами, создаваемыми при использовании службы Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 4d689b51a53a27a0e85a52724752d959c4c2506d
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775064"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Создание рабочей области службы Машинное обучение Azure

Для использования службы Машинное обучение Azure требуется [**Рабочая область машинное обучение Azure службы**](concept-workspace.md).  Эта Рабочая область является ресурсом верхнего уровня для службы и предоставляет централизованное расположение для работы со всеми создаваемыми артефактами. 

В этой статье вы узнаете, как создать рабочую область с помощью любого из следующих методов: 
* Интерфейс [портал Azure](#portal)
* [Пакет SDK для машинное обучение Azure для Python](#sdk)
* Шаблон Azure Resource Manager
* Интерфейс [командной строки машинное обучение Azure](#cli)

Рабочую область, созданную с помощью описанных здесь действий, можно использовать в качестве обязательного компонента для других руководств и инструкций.

Если вы хотите использовать сценарий для настройки автоматизированного машинного обучения в локальной среде Python, обратитесь к [Azure/Мачинелеарнингнотебукс GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) для получения инструкций.  

При создании рабочей области следующие ресурсы Azure добавляются автоматически (если они находятся в автономном регионе):
 
- [Реестр контейнеров Azure.](https://azure.microsoft.com/services/container-registry/) Чтобы сократить затраты, запись контроля **доступа выполняется с отложенной** загрузкой до создания образов развертывания.
- [Хранилище Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Хранилище ключей Azure](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>Как в случае с другими службами Azure, со службой "Машинное обучение Azure" связаны определенные ограничения и квоты. [Узнайте больше о квотах и способах отправки запросов на получение дополнительных квот.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>Предварительные требования
Для создания рабочей области понадобится подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

## <a name="portal"></a>портал Azure

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

Независимо от того, как он был создан, рабочую область можно просмотреть в [портал Azure](https://portal.azure.com/).  Дополнительные сведения см. [в разделе Просмотр рабочей области](how-to-manage-workspace.md#view) .

## <a name="sdk"></a>Пакет SDK для Python

Создайте рабочую область с помощью пакета SDK для Python. Сначала необходимо установить пакет SDK.

> [!IMPORTANT]
> Пропуск установки пакета SDK, если вы используете виртуальную машину Azure для обработки и анализа данных или Azure Databricks.
> * Виртуальные машины для обработки и анализа данных Azure, созданные после 27 сентября 2018 г., поставляются с уже установленным пакетом SDK для Python. Пропустите установку и начните с [создания рабочей области с помощью пакета SDK](#sdk-create).
> * В среде Azure Databricks выполните вместо этого [шаги установки Databricks](how-to-configure-environment.md#azure-databricks).

>[!NOTE]
> Используйте эти инструкции для установки и использования пакета SDK с локального компьютера. Чтобы использовать Jupyter на удаленной виртуальной машине, настройте сеанс терминала удаленного рабочего стола или X.

Прежде чем устанавливать пакет SDK, сначала рекомендуется создать изолированную среду Python. Хотя в этой статье используется [Miniconda](https://docs.conda.io/en/latest/miniconda.html), также можно использовать полностью установленное средство [Anaconda](https://www.anaconda.com/) или [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

В инструкциях, приведенных в этой статье, будут установлены все пакеты, необходимые для запуска учебника и блокнотов.  Другие примеры записных книжек могут потребовать установки дополнительных компонентов.  Дополнительные сведения об этих компонентах см. в статье об [установке пакета SDK для Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

### <a name="install-miniconda"></a>Установка Miniconda

[Скачайте и установите Miniconda](https://docs.conda.io/en/latest/miniconda.html). Выберите для установки Python версии 3.7 или более поздней. Не выбирайте версию Python 2.x.  

### <a name="create-an-isolated-python-environment"></a>Создание изолированной среды Python

1. Откройте запрос Anaconda, затем создайте новую среду conda с именем *MyENV* и установите Python 3.6.5. Пакет SDK для Машинного обучения Azure будет работать с Python 3.5.2 или более поздней версии, но автоматические компоненты машинного обучения не полностью функциональны на Python 3.7.  Скачивание компонентов и пакетов и последующее создание среды займет несколько минут. 

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

### <a name='sdk-create'></a>Создание рабочей области с помощью пакета SDK

Создайте рабочую область в Jupyter Notebook с помощью пакета SDK для Python.

1. Создайте каталог, который вы хотите использовать для краткого руководства и учебников, или перейдите к нему.

1. Чтобы запустить Jupyter Notebook, введите эту команду:

    ```shell
    jupyter notebook
    ```

1. В окне браузера создайте записную книжку с использованием стандартного ядра `Python 3`. 

1. Чтобы просмотреть версию пакета SDK, введите следующий код Python в ячейку записной книжки и выполните его.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. Найдите значение для параметра `<azure-subscription-id>` в [списке подписок на портале Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Используйте любую подписку, в которой вам предоставлена роль владельца или участника. Дополнительные сведения о ролях см. в статье [Управление доступом к машинное обучение Azure рабочей области](how-to-assign-roles.md) .

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

Сохраните сведения рабочей области в файле конфигурации в текущем каталоге. Этот файл называется *. azureml/config. JSON*.  

Этот файл конфигурации рабочей области упрощает дальнейшую загрузку этой же рабочей области. Вы можете загрузить его с другими записными книжками и скриптами в том же каталоге или в подкаталоге, используя код `ws=Workspace.from_config()` . 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

Этот вызов API `write_config()` позволяет создать файл конфигурации в текущем каталоге. Файл *. azureml/config. JSON* содержит следующие сведения:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Чтобы использовать рабочую область в сценариях Python или записных книжках Jupyter, расположенных в других каталогах, скопируйте этот файл в этот каталог. Файл может находиться в том же каталоге, в подкаталоге с именем *. azureml*или в родительском каталоге.

## <a name="resource-manager-template"></a>Шаблон Resource Manager

Сведения о создании рабочей области с шаблоном см. в статье [Создание рабочей области службы машинное обучение Azure с помощью шаблона](how-to-create-workspace-template.md) .

<a name="cli"></a>
## <a name="command-line-interface"></a>Интерфейс командной строки

Сведения о создании рабочей области с помощью интерфейса командной строки см. в статье [Использование расширения CLI для службы машинное обучение Azure](reference-azure-machine-learning-cli.md).

## <a name="clean-up-resources"></a>Очистка ресурсов 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Следующие шаги

* Независимо от того, как он был создан, рабочую область можно просмотреть в [портал Azure](https://portal.azure.com/).  Дополнительные сведения см. [в разделе Просмотр рабочей области](how-to-manage-workspace.md#view) .

* Ознакомьтесь с этими учебниками в рабочей области.

    * Учебник из двух частей: [Настройте среду и рабочую область](tutorial-1st-experiment-sdk-setup.md) и обучите [первую модель](tutorial-1st-experiment-sdk-train.md).
    * Учебник из двух частей: [Обучение](tutorial-train-models-with-aml.md) и [развертывание](tutorial-deploy-models-with-aml.md) режима классификации изображений.
    * Учебник из двух частей: [Подготовка данных](tutorial-data-prep.md) и [Использование автоматизированного машинного обучения](tutorial-auto-train-models.md) для создания модели регрессии.

* Дополнительные сведения о [настройке среды разработки](how-to-configure-environment.md).

* Дополнительные сведения о [пакете SDK для машинное обучение Azure для Python](https://aka.ms/aml-sdk).
