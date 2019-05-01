---
title: Создание и развертывание моделей ONNX с возможностью взаимодействия
titleSuffix: Azure Machine Learning service
description: Дополнительные сведения об ONNX и использовании машинного обучения Azure для создания и развертывания моделей ONNX
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: 716286fdfb684a277c7147936f162089a2cdefd8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692698"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX и Машинное обучение Azure: создание и развертывание моделей ИИ с возможностью взаимодействия

Формат [Open Neural Network Exchange](https://onnx.ai) (ONNX) — это открытый стандарт для представления моделей машинного обучения. ONNX поддерживается [сообществом партнеров](https://onnx.ai/supported-tools), включая корпорацию Майкрософт, которое создает совместимые платформы и средства. Корпорация Майкрософт стремится к созданию открытого, совместимого AI, чтобы ученые и разработчики могли делать следующее:

+ использовать для создания и обучения моделей платформу по своему выбору;
+ развертывать кроссплатформенные модели с минимальной работой над интеграцией.

Чтобы помочь вам добиться этих целей, корпорация Майкрософт реализовала поддержку ONNX во всех своих продуктах, включая Azure и Windows.  

## <a name="why-choose-onnx"></a>Для чего нужен ONNX?

ONNX дает совместимость, которая позволяет быстрее запускать хорошие идеи в производство. Благодаря ONNX специалисты по обработке и анализу данных могут выбирать для своей работы предпочитаемую платформу. А разработчикам он позволяет тратить меньше времени на подготовку моделей к производству и развертывание в облаке и Edge.  

Можно создавать модели ONNX из многих платформ, включая PyTorch, Chainer, MXNet, ML.Net, TensorFlow, Keras, SciKit-Learn, Microsoft Cognitive Toolkit и многое другое.

Также имеется экосистема средств для визуализации и ускорения моделей ONNX. Для распространенных сценариев доступен также ряд предварительно обученных моделей ONNX.

[Модели ONNX можно развертывать](#deploy) в облако с помощью Машинного обучения Azure и среды выполнения ONNX. С помощью [Windows ML](https://docs.microsoft.com/windows/ai/) их можно также развертывать на устройствах Windows 10. В сообществе ONNX существуют также конвертеры для развертывания на другие платформы. 

[![ONNX блок-схема обучения, преобразователи типов и развертывания](media/concept-onnx/onnx.png)](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Получение моделей ONNX

Модели ONNX можно получить различными способами:
+ Получение предварительно обученной модели ONNX из [коллекции моделей ONNX](https://github.com/onnx/models) (см. пример в нижней части этой статьи).
+ Создание настраиваемых моделей ONNX в [пользовательской службе визуального распознавания Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/). 
+ Преобразование существующей модели из другого формата в ONNX (см. пример в нижней части этой статьи). 
+ Обучение новой модели в службе машинного обучения Azure (см. пример в нижней части этой статьи).

## <a name="saveconvert-your-models-to-onnx"></a>Сохранение/преобразование моделей в ONNX

Можно преобразовать существующие модели ONNX или сохранить их в формате ONNX в конце обучения.

|Платформа модели|Пример или средство преобразования|
|-----|-------|
|PyTorch|[Записная книжка Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|TensorFlow|[Преобразователь tensorflow-onnx](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Записная книжка Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Записная книжка Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit-Learn, CoreML<br/>XGBoost и libSVM|[WinMLTools](/windows/ai/windows-ml/convert-model-winmltools)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit|[Записная книжка Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|

Актуальный список поддерживаемых платформ и преобразователей см. на [сайте с руководствами ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Развертывание моделей ONNX в Azure

С помощью службы машинного обучения Azure можно развертывать модели ONNX, управлять ими и осуществлять мониторинг. Используя стандартный [рабочий процесс развертывания](concept-model-management-and-deployment.md) и среду выполнения ONNX, можно создать конечную точку REST, размещенную в облаке. Полный пример записной книжки Jupyter см. в конце этой статьи — опробуйте его на практике. 

### <a name="install-and-configure-onnx-runtime"></a>Установка и настройка среды выполнения ONNX

Среда выполнения ONNX — это высокоэффективная подсистема вывода с открытым кодом, предназначенная для логической обработки моделей ONNX. Она обеспечивает аппаратное ускорение на ЦП и GPU с помощью интерфейсов API, предоставляемых для Python, C# и C. Среда выполнения ONNX поддерживает модели ONNX 1.2 и более поздних версий и может работать на платформах Linux, Windows и Mac. Пакеты Python доступны на сайте [PyPi.org](https://pypi.org) ([ЦП](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)), а [пакет C#](https://www.nuget.org/packages/Microsoft.ML.OnnxRuntime/) доступен на сайте [Nuget.org](https://www.nuget.org). Дополнительные сведения о проекте можно найти на сайте [GitHub](https://github.com/Microsoft/onnxruntime). См. в статье [требования к системе](https://github.com/Microsoft/onnxruntime#system-requirements) перед установкой.

Чтобы установить среду выполнения ONNX для Python, используйте следующую команду.
```python
pip install onnxruntime
```

Для вызова среды выполнения ONNX в сценарии Python используйте следующую команду.
```python
import onnxruntime

session = onnxruntime.InferenceSession("path to model")
```

Входные и выходные данные для использования модели см. в документации к соответствующей модели. Для просмотра модели можно также использовать такое средство визуализации, как [Netron](https://github.com/lutzroeder/Netron). Среда выполнения ONNX также позволяет запрашивать метаданные модели и ее входные и выходные данные:
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Для вывода модели добавьте параметр `run` и передайте список выходных данных, которые хотите получить (не заполняйте, если вам нужны все), а также карту входных значений. Результатом будет список выходных данных.
```python
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Полный справочник по API Python см. в [документации по среде выполнения ONNX](https://aka.ms/onnxruntime-python).

### <a name="example-deployment-steps"></a>Пример этапов развертывания

Приведем пример развертывания модели ONNX:

1. Инициализируйте рабочую область службы машинного обучения Azure. Если у вас его еще, узнайте, как [создать рабочую область](setup-create-workspace.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Зарегистрируйте модель в машинном обучении Azure.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Создание образа с моделью и зависимыми компонентами.

   ```python
   from azureml.core.image import ContainerImage

   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   > [!TIP]
   > В предыдущем примере используется образ по умолчанию, предоставляемых службой машинного обучения Azure. Также можно использовать пользовательский образ. Дополнительные сведения см. в разделах о настройке и регистрации образа в статье [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md#configureimage).

   Файл `score.py` содержит логику оценки и должен быть добавлен в образ. Этот файл используется для запуска модели в образе. Инструкции по созданию сценария оценки см. в этом [руководстве](tutorial-deploy-models-with-aml.md#create-scoring-script). Пример файла для модели ONNX см. ниже:

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global session
       model = Model.get_model_path(model_name = 'MyONNXModel')
       session = onnxruntime.InferenceSession(model)

   def preprocess(input_data_json):
       # convert the JSON data into the tensor input
       return np.array(json.loads(input_data_json)['data']).astype('float32')

   def postprocess(result):
       return np.array(result).tolist()

   def run(input_data_json):
       try:
           start = time.time()   # start timer
           input_data = preprocess(input_data_json)
           input_name = session.get_inputs()[0].name  # get the id of the first input of the model   
           result = session.run([], {input_name: input_data})
           end = time.time()     # stop timer
           return {"result": postprocess(result),
                   "time": end - start}
       except Exception as e:
           result = str(e)
           return {"error": result}
   ```

   Файл `myenv.yml` описывает необходимые для образа зависимости. Инструкции по созданию файла среды, такого как в следующем примере, см. в [этом руководстве](tutorial-deploy-models-with-aml.md#create-environment-file).

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies.create(pip_packages=["numpy","onnxruntime","azureml-core"])

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Развертывание модели описывается в документе [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md).


## <a name="examples"></a>Примеры

[how-to-use-azureml/deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx): примеры записных книжек, создающих и развертывающих модели ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>Подробнее

Узнайте больше об ONNX или поддержите проект:
+ [Веб-сайт проекта ONNX](https://onnx.ai)

+ [Код ONNX в GitHub](https://github.com/onnx/onnx)

Узнайте больше о среде выполнения ONNX или поддержите проект:
+ [Репозиторий GitHub для среды выполнения ONNX](https://github.com/Microsoft/onnxruntime)


