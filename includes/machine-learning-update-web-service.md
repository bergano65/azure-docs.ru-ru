---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: d36bf2db05113656a77e76ff900d95910f313c73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477261"
---
Чтобы обновить веб-службу, используйте `update` метод. Можно обновить веб-службу, чтобы использовать новую модель, новый скрипт записи или новые зависимости, которые можно указать в конфигурации вывода. Дополнительные сведения см. в документации по [WebService. Update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-).

> [!IMPORTANT]
> При создании новой версии модели необходимо вручную обновить каждую службу, которая будет использовать ее.
>
> Пакет SDK нельзя использовать для обновления веб-службы, опубликованной из конструктора Машинное обучение Azure.

**Использование пакета SDK**

В следующем коде показано, как использовать пакет SDK для обновления сценария модели, среды и входа для веб-службы.

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

Вы также можете обновить веб-службу с помощью интерфейса командной строки ML. В следующем примере демонстрируется регистрация новой модели и обновление веб-службы для использования новой модели.

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> В этом примере документ JSON используется для передачи сведений о модели из команды регистрации в команду Update.
>
> Чтобы обновить службу для использования нового скрипта записи или среды, создайте [файл конфигурации вывода](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) и укажите его с помощью `ic` параметра.

Дополнительные сведения см. в документации по [AZ ML Service Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) .