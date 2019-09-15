---
title: Развертывание моделей на виртуальных машинах записных книжек
titleSuffix: Azure Machine Learning
description: Узнайте, как развернуть модели Машинное обучение Azure в качестве веб-службы с помощью виртуальных машин записной книжки.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 08/08/2019
ms.openlocfilehash: 046f998038c47a48a8528bf36d87ac836395eec2
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002830"
---
# <a name="deploy-a-model-to-notebook-vms"></a>Развертывание модели на виртуальных машинах записных книжек

Узнайте, как использовать Машинное обучение Azure для развертывания модели в качестве веб-службы на виртуальной машине записной книжки. Используйте виртуальные машины записной книжки, если выполняется одно из следующих условий.

- вам важно быстро выполнять развертывание и проверку модели.
- вы тестируете модель, которая находится в стадии разработки.

> [!TIP]
> Развертывание модели из Jupyter Notebook на виртуальной машине записной книжки в веб-службу на той же виртуальной машине является _локальным развертыванием_. В этом случае локальным компьютером является виртуальная машина записной книжки. Дополнительные сведения о развертываниях см. в разделе [Развертывание моделей с помощью машинное обучение Azure](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Предварительные требования

- Рабочая область Машинное обучение Azure с виртуальной машиной записной книжки. Дополнительные сведения см. в статье [Настройка среды и рабочей области](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-notebook-vms"></a>Развертывание на виртуальных машинах записной книжки

Пример записной книжки, демонстрирующий локальные развертывания, включен на виртуальную машину записной книжки. Чтобы загрузить записную книжку и развернуть ее в качестве веб-службы на виртуальной машине, выполните следующие действия.

1. В [портал Azure](https://portal.azure.com)выберите виртуальные машины машинное обучение Azure записных книжек.

1. Откройте подкаталог, а затем откройте `how-to-use-azureml/deploy-to-local/register-model-deploy-local.ipynb`. `samples-*` После открытия запустите записную книжку.

    ![Снимок экрана: выполняемая локальная служба в записной книжке](media/how-to-deploy-local-container-notebookvm/deploy-local-service.png)

1. В записной книжке отображается URL-адрес и порт, на котором запущена служба. Например, `https://localhost:6789`. Можно также выполнить ячейку, содержащую `print('Local service port: {}'.format(local_service.port))` порт, чтобы отобразить его.

    ![Снимок экрана с портом локальной службы](media/how-to-deploy-local-container-notebookvm/deploy-local-service-port.png)

1. Чтобы протестировать службу с виртуальной машины записной книжки, используйте `https://localhost:<local_service.port>` URL-адрес. Чтобы проверить удаленный клиент, получите общедоступный URL-адрес службы, работающей на виртуальной машине записной книжки общедоступный URL-адрес можно определить, используя следующую формулу. `https://<notebookvm_name>-<local_service_port>.<azure_region_of_notebook>.notebooks.azureml.net/score`. Например, `https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score`.

## <a name="test-the-service"></a>Тестирование службы

Чтобы отправить демонстрационные данные в работающую службу, используйте следующий код. Замените значение `service_url` на URL-адрес из предыдущего шага:

```python
import requests
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')
access_token = "your bearer token"
headers = {'Content-Type':'application/json', 'Authorization': 'Bearer ' + access_token}
service_url = "https://mynotebookvm-6789.eastus2.notebooks.azureml.net/score"
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Следующие шаги

* [Развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)