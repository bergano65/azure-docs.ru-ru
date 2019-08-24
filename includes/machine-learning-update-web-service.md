---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: f1eee95cf35b831fc2a213044d700fd5afbdfc96
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997957"
---
Чтобы обновить веб-службу, используйте метод `update`. Веб-службу можно обновить для использования новой модели, скрипта входа или зависимостей, которые можно указать с помощью конфигурации вывода. Дополнительные сведения см. в справочнике по [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> При создании новой версии модели необходимо вручную обновить каждую службу, которая будет использовать ее.

**Использование пакета SDK**

В следующем коде показано, как использовать пакет SDK для обновления сценария модели, среды и входа для веб-службы.

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# register new model
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s)
service.update(models=[new_model], inference_config=inference_config)
print(service.state)
print(service.get_logs())
```

**Использование интерфейса командной строки**

Вы также можете обновить веб-службу с помощью интерфейса командной строки ML. В следующем примере показано, как зарегистрировать новую модель, а затем обновить веб-службу для использования новой модели:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> В этом примере документ JSON используется для передачи сведений о модели из команды регистрации в команду Update.
>
> Чтобы обновить службу для использования нового скрипта записи или среды, создайте [файл конфигурации вывода](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) и укажите его с `ic` помощью параметра.

Дополнительные сведения см. в справочнике по [обновлению службы (AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) ).