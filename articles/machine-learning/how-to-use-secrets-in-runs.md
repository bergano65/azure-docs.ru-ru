---
title: Используйте секреты в тренировочных забегах
titleSuffix: Azure Machine Learning
description: Передайте секреты обучения работает в безопасной моды с помощью Workspace Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942276"
---
# <a name="use-secrets-in-training-runs"></a>Используйте секреты в тренировочных забегах
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как использовать секреты в обучении работает безопасно. Информация о подлинности, такая как имя пользователя и пароль, является секретом. Например, если вы подключитесь к внешней базе данных для запроса обучаемых данных, необходимо будет передать имя пользователя и пароль в контекст удаленного запуска. Кодирование таких значений в обучающих скриптов в понятном тексте является небезопасным, поскольку это разоблачило бы секрет. 

Вместо этого в рабочем пространстве Машинного обучения Azure есть связанный ресурс под названием [Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-overview) Используйте этот Key Vault для безопасного передачи секретов удаленным запускам через набор AIS в SDK Azure Machine Learning Python.

Основной поток для использования секретов:
 1. На локальном компьютере войдите в Azure и подключитесь к рабочему пространству.
 2. На локальном компьютере установите секрет в Хранилище ключей Workspace.
 3. Отправить удаленный запуск.
 4. В удаленном запуске, получить секрет от Key Vault и использовать его.

## <a name="set-secrets"></a>Установить секреты

В классе Машинного обучения Azure класс [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) содержит методы настройки секретов. Во время локальной сессии Python сначала получите ссылку на [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) рабочее пространство Key Vault, а затем используйте метод для создания секрета по имени и значению. Метод __set_secret__ обновляет секретное значение, если имя уже существует.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Не вкладывайте секретное значение в код Python, так как хранить его в файле в виде четкого текста небезопасно. Вместо этого получите секретное значение из переменной среды, например, Azure DevOps построить секрет, или от интерактивного пользовательского ввода.

Вы можете перечислить [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) секретные имена, используя метод, и есть также пакетная версия,[set_secrets(),](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) которая позволяет установить несколько секретов одновременно.

## <a name="get-secrets"></a>получение секретов;

В локальном коде вы[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) можете использовать метод, чтобы получить секретное значение по имени.

Для запусков, представленных, [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) используйте [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) метод с классом. [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) Поскольку представленный запуск знает о своем рабочем пространстве, этот метод сокращает мгновенное воспроизведение рабочего пространства и возвращает секретное значение напрямую.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Будьте осторожны, чтобы не разоблачить секретное значение, написав или распечатать его.

Существует также пакетная версия, [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) для доступа к нескольким секретам одновременно.

## <a name="next-steps"></a>Дальнейшие действия

 * [Просмотр примера ноутбука](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Узнайте о безопасности предприятия с помощью машинного обучения Azure](concept-enterprise-security.md)
