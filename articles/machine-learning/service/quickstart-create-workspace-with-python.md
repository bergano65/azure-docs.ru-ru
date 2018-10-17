---
title: Краткое руководство по использованию пакета SDK Python для создания рабочей области Службы машинного обучения. Машинное обучение Azure
description: Начало работы со средством "Машинное обучение Azure".  Установите пакет SDK для Python и используйте его при создании рабочей области. Указанная рабочая область — это основной блок облака для экспериментов, обучения и развертывания моделей машинного обучения с помощью службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: quickstart
ms.reviewer: sgilley
author: hning86
ms.author: haining
ms.date: 09/24/2018
ms.openlocfilehash: ee24c1797d0f52d2529ed583a0cfe90cc9e27035
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067769"
---
# <a name="quickstart-use-python-to-get-started-with-azure-machine-learning"></a>Краткое руководство. Использование Python для начала работы со службой "Машинное обучение Azure"

В рамках этого краткого руководства будет использоваться пакет SDK Машинного обучения Azure для Python с целью создания и применения [рабочей области](concept-azure-machine-learning-architecture.md) Службы машинного обучения. Эта рабочая область — это основной блок для экспериментов, обучения и развертывания моделей машинного обучения с помощью службы машинного обучения Azure.

При работе с этим руководством вы установите пакет SDK для Python и:
* Создавать рабочую область в своей подписке Azure
* Создадите файл конфигурации для той рабочей области, которая будет использоваться позже в других записных книжках и скриптах.
* Запишите код, который будет записывать в журнал значения внутри рабочей области.
* Просмотрите записанные значения в рабочей области

Рабочую область и ее файл конфигурации, созданные в рамках этого краткого руководства, можно использовать как необходимые компоненты для работы с другими руководствами и статьями с инструкциями по Машинному обучению Azure. Как и в других службах Azure, существуют ограничения и квоты, связанные со службой "Машинное обучение Azure". [Узнайте больше о квотах и способах отправки запросов на получение дополнительных квот.](how-to-manage-quotas.md)

Для удобства следующие ресурсы Azure автоматически добавляются в рабочую область, если они доступны в вашем регионе: [реестр контейнеров](https://azure.microsoft.com/services/container-registry/), [хранилище](https://azure.microsoft.com/services/storage/), [аналитические сведения о приложении](https://azure.microsoft.com/services/application-insights/) и [хранилище ключей](https://azure.microsoft.com/services/key-vault/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.


##  <a name="install-the-sdk"></a>Установка пакета SDK

**Пропустите этот раздел, если вы используете** Виртуальную машину для обработки и анализа данных (DSVM), созданную после 27 сентября 2018 г., так как эти DSVM поставляются с предустановленным пакетом SDK для Python.

Прежде чем устанавливать пакет SDK, сначала рекомендуется создать изолированную среду Python. Хотя в этом кратком руководстве используется [Miniconda](https://conda.io/docs/user-guide/install/index.html), также можно использовать полностью установленное средство [Anaconda](https://www.anaconda.com/) или [Python virtualenv](https://virtualenv.pypa.io/en/stable/).

### <a name="install-miniconda"></a>Установка Miniconda


[Скачайте](https://conda.io/miniconda.html) и установите Miniconda. Выберите Python 3.7 или более позднюю версию. Не выбирайте версию Python 2.x.

### <a name="create-an-isolated-python-environment"></a>Создание изолированной среды Python 

Запустите окно командной строки и создайте среду conda с именем `myenv`, используя Python 3.6.

```sh
conda create -n myenv -y Python=3.6
```

Активируйте среду.

  ```sh
  conda activate myenv
  ```

### <a name="install-the-sdk"></a>Установка пакета SDK

В активированной среде conda установите пакет SDK. С помощью этого кода можно установить основные компоненты пакета SDK для Машинного обучения Azure, а также сервер Jupyter Notebook в среде conda `myenv`.  Процедура установки занимает **около 4 минут**.

```sh
pip install azureml-sdk[notebooks]
```

## <a name="create-a-workspace"></a>Создание рабочей области

Запустите Jupyter Notebook, используя следующую команду.
```sh
jupyter notebook
```

В окне браузера создайте записную книжку с использованием стандартного ядра `Python 3`. 

Просмотрите версию пакета SDK, введя следующий код Python в ячейку записной книжки и выполнив его.

```python
import azureml.core
print(azureml.core.VERSION)
```

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

Выполнение приведенного выше кода может запустить новое окно браузера для входа в учетную запись Azure. После входа в систему маркер проверки подлинности будет кэширован локально.

Чтобы просмотреть сведения о рабочей области, включая связанное хранилище, реестр контейнеров и хранилище ключей, введите:

```python
ws.get_details()
```

## <a name="write-a-configuration-file"></a>Запись файла конфигурации

Сохраните сведения рабочей области в файле конфигурации в текущем каталоге. Этот файл называется aml_config\config.json.  

Файл конфигурации рабочей области упрощает дальнейшую загрузку этой же рабочей области с помощью других записных книжек и скриптов в том же каталоге или подкаталоге. 

```python
# Create the configuration file.
ws.write_config()

# Use this code to load the workspace from 
# other scripts and notebooks in this directory.
# ws = Workspace.from_config()
```

Вызов API `write_config()` позволяет создать файл конфигурации в текущем каталоге. Файл `config.json` содержит следующее:

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

## <a name="use-the-workspace"></a>Используйте рабочую область

Напишите код, использующий базовые интерфейсы API пакета SDK для отслеживания нескольких экспериментальных запусков.

```python
from azureml.core import Experiment

# create a new experiemnt
exp = Experiment(workspace=ws, name='myexp')

# start a run
run = exp.start_logging()

# log a number
run.log('my magic number', 42)

# log a list (Fibonacci numbers)
run.log_list('my list', [1, 1, 2, 3, 5, 8, 13, 21, 34, 55]) 

# finish the run
run.complete()
```

## <a name="view-logged-results"></a>Просмотр зарегистрированных результатов
После завершения запуска сведения об экспериментальном запуске можно просмотреть на портале Azure. Используйте следующий код для вывода URL-адреса в результатах последнего запуска.

```python
print(run.get_portal_url())
```

Используйте ссылку, чтобы просмотреть в браузере зарегистрированные в журнале значения на портале Azure.

![зарегистрированные в журнале значения на портале](./media/quickstart-create-workspace-with-python/logged-values.png)

## <a name="clean-up-resources"></a>Очистка ресурсов 
>[!IMPORTANT]
>Созданные ресурсы могут использоваться в качестве необходимых компонентов при работе с другими руководствами по Машинному обучению Azure.

Если вы не собираетесь использовать ресурсы, созданные при работе с этим кратким руководством, удалите их, чтобы не платить за их использование.

```python
ws.delete(delete_dependent_resources=True)
```

## <a name="next-steps"></a>Дополнительная информация

Вы создали ресурсы, необходимые для начала экспериментов и развертывания моделей. Кроме того, вы запускали код в записной книжке и изучали журнал выполнения из этого кода в вашей рабочей области в облаке.

Для работы с руководствами по Машинному обучению Azure вам понадобится несколько пакетов в среде.

1. Закройте записную книжку в браузере.
1. В окне командной строки используйте комбинацию клавиш `Ctrl`+`C`, чтобы остановить работу сервера записной книжки.
1. Установите дополнительные пакеты.

    ```sh
    conda install -y cython matplotlib scikit-learn pandas numpy
    pip install azureml-sdk[automl]
    ```

После установки этих пакетов следуйте инструкциям в руководствах по обучению и развертыванию моделей.  

> [!div class="nextstepaction"]
> [Руководство. Обучение модели классификации изображений](tutorial-train-models-with-aml.md)

Вы также можете изучить [более сложные примеры на GitHub](https://aka.ms/aml-notebooks).
