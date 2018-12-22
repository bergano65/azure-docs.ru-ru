---
title: Краткое руководство.  Начало работы в среде Python
titleSuffix: Azure Machine Learning service
description: Начало работы со Службой машинного обучения Azure в среде Python. Используя пакет SDK для Python, создайте рабочую область, которая является основным блоком в облаке для экспериментов, обучения и развертывания моделей машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 3ab55cec4b8483cf254ec3d9fe68521baca9cdf5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53268522"
---
# <a name="quickstart-use-python-sdk-to-get-started-with-azure-machine-learning"></a>Краткое руководство по началу работы со службой "Машинное обучение Azure" с использованием пакета SDK для Python

В рамках этого краткого руководства будет использоваться пакет SDK для Машинного обучения Azure для Python для создания и применения [рабочей области](concept-azure-machine-learning-architecture.md) Службы машинного обучения. Эта рабочая область — основной блок в облаке для экспериментов, обучения и развертывания моделей машинного обучения с помощью Машинного обучения Azure. В этом кратком руководстве вы начнете с настройки собственного окружения Python и сервера Jupyter Notebook. Сведения о запуске без установки см. в статье [Краткое руководство. Начало работы со Службой машинного обучения Azure с помощью портала Azure](quickstart-get-started.md).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

При работе с этим руководством вы установите пакет SDK для Python и:

* Создавать рабочую область в своей подписке Azure.
* создадите файл конфигурации для той рабочей области, которая будет использоваться позже в других записных книжках и скриптах;
* запишите код, который будет записывать в журнал значения внутри рабочей области;
* просмотрите записанные значения в рабочей области.

В рамках этого краткого руководства вы создадите рабочую область и файл конфигурации. Вы можете их использовать в качестве необходимых компонентов для других руководств и статей с инструкциями, посвященных Машинному обучению. Как в случае с другими службами Azure, со службой "Машинное обучение Azure" связаны ограничения и квоты. [Узнайте больше о квотах и способах отправки запросов на получение дополнительных квот.](how-to-manage-quotas.md)

Следующие ресурсы Azure добавляются автоматически в рабочую область, если они доступны в вашем регионе:
 
- [Реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/)
- [Хранилище Azure](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Хранилище ключей Azure](https://azure.microsoft.com/services/key-vault/)

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](http://aka.ms/AMLFree).

## <a name="install-the-sdk"></a>Установка пакета SDK

>[!NOTE]
> Для выполнения кода из этой статьи требуется пакет SDK для службы "Машинное обучение Azure" версии 1.0.2. 

*Пропустите этот раздел, если вы используете виртуальную машину для обработки и анализа данных, созданную после 27 сентября 2018 г.* Эти виртуальные машины для обработки и анализа поставляются с уже установленным пакетом SDK для Python.

Прежде чем устанавливать пакет SDK, сначала рекомендуется создать изолированную среду Python. Хотя в этом кратком руководстве используется [Miniconda](https://conda.io/docs/user-guide/install/index.html), также можно использовать полностью установленное средство [Anaconda](https://www.anaconda.com/) или [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Установка Miniconda


[Скачайте](https://conda.io/miniconda.html) и установите Miniconda. Выберите Python 3.7 или более позднюю версию. Не выбирайте версию Python 2.x.

### <a name="create-an-isolated-python-environment"></a>Создание изолированной среды Python 

Откройте окно командной строки. Затем создайте среду conda с именем `myenv`, используя Python 3.6.

```shell
conda create -n myenv -y Python=3.6
```

Активируйте среду.

  ```shell
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Установка пакета SDK

В активированной среде conda установите пакет SDK. Этот код устанавливает основные компоненты пакета SDK для Машинного обучения. Он также устанавливает сервер Jupyter Notebook в среду conda. Установка занимает несколько минут в зависимости от конфигурации компьютера.

```sh
# install Jupyter
conda install nb_conda

# install the base SDK and Jupyter Notebook
pip install azureml-sdk[notebooks]

```

Для установки дополнительных компонентов пакета SDK можно использовать дополнительные ключевые слова.

```sh
# install the base SDK and auto ml components
pip install azureml-sdk[automl]

# install the base SDK and model explainability component
pip install azureml-sdk[explain]

# install the base SDK and experimental components
pip install azureml-sdk[contrib]
```

В среде Databricks используйте эту установку.

```
# install the base SDK and automl components in Azure Databricks environment
# read more at: https://github.com/Azure/MachineLearningNotebooks/tree/master/databricks
pip install azureml-sdk[databricks]
```


## <a name="create-a-workspace"></a>Создание рабочей области

Чтобы запустить Jupyter Notebook, введите эту команду.
```shell
jupyter notebook
```

В окне браузера создайте записную книжку с использованием стандартного ядра `Python 3`. 

Чтобы просмотреть версию пакета SDK, введите следующий код Python в ячейку записной книжки и выполните его.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

Создайте группу ресурсов Azure и рабочую область.

Найдите значение `<azure-subscription-id>` в [списке подписок на портале Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Используйте любую подписку, в которой вам предоставлена роль владельца или участника.

```python
from azureml.core import Workspace
ws = Workspace.create(name='myworkspace',
                      subscription_id='<azure-subscription-id>',    
                      resource_group='myresourcegroup',
                      create_resource_group=True,
                      location='eastus2' # or other supported Azure region  
                     )
```

Выполнение приведенного выше кода может запустить новое окно браузера для входа в учетную запись Azure. Когда вы войдете в систему, маркер проверки подлинности будет кэширован локально.

Чтобы просмотреть сведения о рабочей области, например связанное хранилище, реестр контейнеров и хранилище ключей, введите следующий код.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


## <a name="write-a-configuration-file"></a>Запись файла конфигурации

Сохраните сведения рабочей области в файле конфигурации в текущем каталоге. Этот файл называется aml_config\config.json.  

Этот файл конфигурации рабочей области упрощает дальнейшую загрузку этой же рабочей области. Вы можете загрузить ее с помощью других записных книжек и скриптов в том же каталоге или подкаталоге. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]


Вызов API `write_config()` позволяет создать файл конфигурации в текущем каталоге. Файл `config.json` содержит следующий скрипт.

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Используйте рабочую область

Напишите код, использующий базовые интерфейсы API пакета SDK для отслеживания нескольких экспериментальных запусков.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]


## <a name="view-logged-results"></a>Просмотр зарегистрированных результатов
После завершения выполнения сведения об экспериментальном запуске можно просмотреть на портале Azure. Используйте следующий код для вывода URL-адреса в результатах последнего запуска.

```python
print(run.get_portal_url())
```

Используйте ссылку, чтобы просмотреть в браузере зарегистрированные в журнале значения на портале Azure.

![зарегистрированные в журнале значения на портале](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Очистка ресурсов 
>[!IMPORTANT]
>Созданные ресурсы могут использоваться в качестве необходимых компонентов при работе с другими руководствами по Машинному обучению Azure.

Если вы не планируете использовать созданные здесь ресурсы, удалите их, чтобы с вас не взималась плата.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]


## <a name="next-steps"></a>Дополнительная информация

Вы создали необходимые ресурсы для экспериментирования и развертывания моделей. Вы также запустили код в записной книжке и изучили журнал выполнения из этого кода в вашей рабочей области в облаке.

Для работы с руководствами по Машинному обучению Azure вам понадобится несколько пакетов в среде.

1. Закройте записную книжку в браузере.
1. В окне командной строки используйте комбинацию клавиш `Ctrl`+`C`, чтобы остановить работу сервера записной книжки.
1. Установите дополнительные пакеты.

    ```shell
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```


После установки этих пакетов следуйте инструкциям в руководствах по обучению и развертыванию моделей. 

> [!div class="nextstepaction"]
> [Руководство. Обучение модели классификации изображений](tutorial-train-models-with-aml.md)

Вы также можете изучить [более сложные примеры на GitHub](https://aka.ms/aml-notebooks).
