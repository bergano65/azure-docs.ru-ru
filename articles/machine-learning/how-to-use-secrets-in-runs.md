---
title: Секреты проверки подлинности в обучении
titleSuffix: Azure Machine Learning
description: Безопасная передача секретов в обучающий запуск с помощью рабочей области Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e984c0c43dcc47c3e11a36f3d5c32bf2ddb9973a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902334"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Использование секретов учетных данных для проверки подлинности в Машинное обучение Azure учебных запусков


Из этой статьи вы узнаете, как безопасно использовать секреты в учебных запусках. Сведения о проверке подлинности, такие как имя пользователя и пароль, являются секретными. Например, при подключении к внешней базе данных для запроса обучающих данных необходимо передать имя пользователя и пароль в контекст удаленного запуска. Написание таких значений в сценариях обучения в виде открытого текста не является безопасным, так как оно будет предоставлять секрет. 

Вместо этого в рабочей области Машинное обучение Azure есть связанный ресурс, называемый [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Используйте эту Key Vault для безопасной передачи секретов в удаленное выполнение с помощью набора API-интерфейсов в пакете SDK для Машинное обучение Azure Python.

Стандартный поток для использования секретов:
 1. Войдите в Azure на локальном компьютере и подключитесь к рабочей области.
 2. На локальном компьютере задайте секрет в рабочей области Key Vault.
 3. Отправка удаленного запуска.
 4. В удаленном запуске получите секрет от Key Vault и используйте его.

## <a name="set-secrets"></a>Задать секреты

В Машинное обучение Azure класс [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) содержит методы для настройки секретов. В локальном сеансе Python сначала получите ссылку на рабочую область Key Vault, а затем используйте [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secret-name--value-) метод, чтобы задать секрет по имени и значению. Метод __set_secret__ обновляет значение секрета, если такое имя уже существует.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Не следует помещайте значение секрета в код Python, так как оно небезопасно для сохранения в файле в виде открытого текста. Вместо этого получите значение секрета из переменной среды, например, для секрета сборки Azure DevOps или из интерактивных входных данных пользователя.

Вы можете получить список имен секретов с помощью [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=truelist-secrets--) метода, а также версию пакета,[set_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueset-secrets-secrets-batch-) , которая позволяет одновременно задать несколько секретов.

## <a name="get-secrets"></a>получение секретов;

В локальном коде можно использовать [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#&preserve-view=trueget-secret-name-) метод для получения значения секрета по имени.

Для запусков, отправленных [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-)  , используйте [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secret-name-) метод с [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true) классом. Так как отправленный запуск осведомлен о своей рабочей области, этот метод назначит создание экземпляра рабочей области и возвращает значение секрета напрямую.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Будьте внимательны, чтобы не предоставлять секретное значение, написав или выполнив печать.

Существует также Пакетная версия [get_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#&preserve-view=trueget-secrets-secrets-) для доступа к нескольким секретам одновременно.

## <a name="next-steps"></a>Дальнейшие действия

 * [Просмотреть пример записной книжки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Сведения о безопасности в корпоративной среде с помощью Машинное обучение Azure](concept-enterprise-security.md)
