---
title: Как развернуть модели для вычислений экземпляров
titleSuffix: Azure Machine Learning
description: Узнайте, как развернуть модели машинного обучения Azure в качестве веб-сервиса с помощью вычисляемых экземпляров.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: 09164580b8bdb249fc12d14e827ad799d51cab34
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756578"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Развертывание модели для вычислений машинного обучения Azure

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как использовать Машинное обучение Azure для развертывания модели в качестве веб-службы в экземпляре вычислений Azure Machine Learning. Используйте вычислить экземпляры, если одно из следующих условий верно:

- вам важно быстро выполнять развертывание и проверку модели.
- вы тестируете модель, которая находится в стадии разработки.

> [!TIP]
> Развертывание модели из ноутбука Jupyter на вычисленивном экземпляре в веб-службу на том же VM — это _локальное развертывание._ В этом случае «локальным» компьютером является экземпляр вычислений. Для получения дополнительной информации о развертываниях см. [Модели развертываний с помощью машинного обучения Azure.](how-to-deploy-and-where.md)

## <a name="prerequisites"></a>Предварительные требования

- Рабочее пространство Для машин Azure Machine Learning с запуском экземпляра вычислений. Для получения дополнительной [Setup environment and workspace](tutorial-1st-experiment-sdk-setup.md)информации см.

## <a name="deploy-to-the-compute-instances"></a>Развертывание в вычислительных экземплярах

Пример ный блокнот, демонстрируя локальные развертывания, включен в экземпляр вычислений. Используйте следующие шаги для загрузки ноутбука и развертывания модели в качестве веб-сервиса на VM:

1. В [студии машинного обучения Azure](https://ml.azure.com)выберите экземпляры Azure Machine Learning.

1. Откройте `samples-*` субдиректор, а `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`затем откройте. После открытия запустите блокнот.

    ![Скриншот работая местного сервиса на ноутбуке](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. Ноутбук отображает URL и порт, на который работает служба. Например, `https://localhost:6789`. Вы также можете запустить `print('Local service port: {}'.format(local_service.port))` ячейку, содержащую для отображения порта.

    ![Скриншот работая местного порта обслуживания](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Чтобы протестировать службу с помощью `https://localhost:<local_service.port>` вычисляемого экземпляра, используйте URL- Чтобы проверить от удаленного клиента, получите общедоступный URL службы, работающий на экземпляре вычислений. Общедоступный URL можно определить по следующей формуле; 
    * Ноутбук VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Вычислить `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`экземпляр: . 

    Например, 
    * Ноутбук VM:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Вычислить экземпляр:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Тестирование службы

Чтобы отправить выборочные данные в запущенную службу, используйте следующий код. Замените значение `service_url` URL-адреса предыдущего шага:

> [!NOTE]
> При проверке подлинности развертывания в экземпляре вычислений аутентификация производится с помощью каталога Azure Active. Вызов `interactive_auth.get_authentication_header()` в примере кода проверяет подлинность с помощью AAD и возвращает заголовок, который затем может быть использован для проверки подлинности службы в экземпляре вычислений. Для получения дополнительной информации [см. Настройка аутентификации для ресурсов машинного обучения Azure и рабочих процессов.](how-to-setup-authentication.md#interactive-authentication)
>
> При проверке подлинности развертывания в службе Azure Kubernetes или в контейнерных инстанциях Azure используется другой метод проверки подлинности. Для получения дополнительной информации можно ознакомиться с информацией о настройках [проверки подлинности для ресурсов и рабочих процессов Azure Machine Learning.](how-to-setup-authentication.md#web-service-authentication)

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Дальнейшие действия

* [Как развернуть модель с помощью пользовательского изображения Docker](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Используйте TLS для обеспечения безопасности веб-службы через Машинное обучение Azure](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения Azure с помощью приложений Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)