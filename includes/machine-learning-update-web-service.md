---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477261"
---
Для обновления веб-службы `update` используйте метод. Можно обновить веб-службу, чтобы использовать новую модель, новый скрипт входа или новые зависимости, которые могут быть указаны в конфигурации выводов. Для получения дополнительной информации смотрите документацию для [Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> При создании новой версии модели необходимо вручную обновлять каждую службу, которую вы хотите использовать.
>
> Вы не можете использовать SDK для обновления веб-сервиса, опубликованного дизайнером машинного обучения Azure.

**Использование пакета SDK**

Следующий код показывает, как использовать SDK для обновления модели, среды и скрипта входа для веб-службы:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Использование интерфейса командной строки**

Вы также можете обновить веб-сервис с помощью ML CLI. Следующий пример демонстрирует регистрацию новой модели, а затем обновление веб-сервиса для использования новой модели:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> В этом примере документ JSON используется для передачи информации о модели из команды регистрации в команду обновления.
>
> Чтобы обновить службу, чтобы использовать новый скрипт входа или среду, `ic` создайте [файл конфигурации выводов](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) и укажите его с параметром.

Для получения дополнительной [az ml service update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) информации см.