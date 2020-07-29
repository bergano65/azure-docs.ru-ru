---
title: Развертывание моделей в экземплярах вычислений
titleSuffix: Azure Machine Learning
description: Узнайте, как развертывать модели Машинное обучение Azure в качестве веб-службы с помощью экземпляров вычислений.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: e4a62b9892af661f74df5b49ff4a451fb673b4e1
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325769"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Развертывание модели для Машинное обучение Azureных экземпляров вычислений

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как использовать Машинное обучение Azure для развертывания модели в качестве веб-службы на Машинное обучение Azure вычислительного экземпляра. Используйте COMPUTE Instances, если выполняется одно из следующих условий.

- вам важно быстро выполнять развертывание и проверку модели.
- вы тестируете модель, которая находится в стадии разработки.

> [!TIP]
> Развертывание модели из Jupyter Notebook на вычислительном экземпляре в веб-службу на той же виртуальной машине является _локальным развертыванием_. В этом случае "локальный" компьютер является вычислительным экземпляром. Дополнительные сведения о развертываниях см. в разделе [Развертывание моделей с помощью машинное обучение Azure](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Предварительные требования

- Рабочая область Машинное обучение Azure с выполняющимся вычислительным экземпляром. Дополнительные сведения см. в статье [Настройка среды и рабочей области](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Развертывание в экземплярах вычислений

Пример записной книжки, демонстрирующий локальные развертывания, включается в вычислительный экземпляр. Чтобы загрузить записную книжку и развернуть ее в качестве веб-службы на виртуальной машине, выполните следующие действия.

1. В [машинное обучение Azure Studio](https://ml.azure.com)выберите машинное обучение Azureные экземпляры вычислений.

1. Откройте `samples-*` подкаталог, а затем откройте `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb` . После открытия запустите записную книжку.

    ![Снимок экрана: выполняемая локальная служба в записной книжке](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)

1. В записной книжке отображается URL-адрес и порт, на котором запущена служба. Например, `https://localhost:6789`. Можно также выполнить ячейку, содержащую `print('Local service port: {}'.format(local_service.port))` порт, чтобы отобразить его.

    ![Снимок экрана с портом локальной службы](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Чтобы протестировать службу из вычислительного экземпляра, используйте `https://localhost:<local_service.port>` URL-адрес. Для проверки с удаленного клиента получите общедоступный URL-адрес службы, выполняющейся на вычислительном экземпляре. Можно определить общедоступный URL-адрес, используя следующую формулу. 
    * Виртуальная машина записной книжки: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * Вычислительный экземпляр: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    например следующие. 
    * Виртуальная машина записной книжки:`https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Вычислительный экземпляр:`https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Тестирование службы

Чтобы отправить демонстрационные данные в работающую службу, используйте следующий код. Замените значение `service_url` на URL-адрес из предыдущего шага:

> [!NOTE]
> При проверке подлинности в развертывании на вычислительном экземпляре выполняется проверка подлинности с помощью Azure Active Directory. При вызове `interactive_auth.get_authentication_header()` в примере кода выполняется проверка подлинности с помощью AAD и возвращается заголовок, который затем можно использовать для проверки подлинности в службе на вычислительном экземпляре. Дополнительные сведения см. в статье [Настройка проверки подлинности ресурсов и рабочих процессов Машинного обучения Azure](how-to-setup-authentication.md#interactive-authentication).
>
> При проверке подлинности в развертывании в службе Kubernetes Azure или экземплярах контейнеров Azure используется другой метод проверки подлинности. Дополнительные сведения о см. в разделе [Настройка проверки подлинности для машинное обучение Azure ресурсов и рабочих процессов](how-to-setup-authentication.md#web-service-authentication).

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

* [Развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Использование TLS для защиты веб-службы с помощью Машинного обучения Azure](how-to-secure-web-service.md).
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей Машинное обучение Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
