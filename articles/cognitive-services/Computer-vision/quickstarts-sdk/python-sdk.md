---
title: Краткое руководство. Пакет SDK для Python
titleSuffix: Azure Cognitive Services
description: Подробные сведения об использовании пакета SDK для Python для распространенных задач.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/15/2019
ms.author: pafarley
ms.openlocfilehash: 3043067f326f782c51be38382070ae0db0e90f4d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314178"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>Пакет SDK Компьютерного зрения Cognitive Services Azure для Python

Служба API компьютерного зрения предоставляет разработчикам доступ к расширенным алгоритмам обработки изображений и возврата данных. Алгоритмы Компьютерного зрения анализируют содержимое изображений по-разному, в зависимости от интересующих вас визуальных компонентов. Например, Компьютерное зрение может определить, имеется ли на изображении содержимое для взрослых или непристойного характера, а также найти все лица на изображении или получить рукописный или печатный текст. Эта служба работает с популярными форматами изображений, такими как JPEG и PNG. 

Компьютерное зрение в приложении может выполнять такие функции.

- Анализ изображений для получения полезных сведений
- Извлечение текста из изображений
- Создание эскизов

Ищете дополнительную документацию?

* [Справочная документация по пакету SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Документация по Компьютерному зрению](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись][azure_sub].
* [Ресурс Компьютерного зрения][computervision_resource] Azure
* [Python 3.6+][python]

При необходимости можно создать учетную запись API Компьютерного зрения с помощью следующей команды [Azure CLI][azure_cli]:

```Bash
RES_REGION=westeurope 
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind ComputerVision \
    --sku S1 \
    --yes
```

## <a name="installation"></a>Установка

Установите пакет SDK Компьютерного зрения Cognitive Services Azure с помощью [pip][pip], дополнительно это можно установить в [виртуальной среде][venv].

### <a name="configure-a-virtual-environment-optional"></a>Настройка виртуальной среды (необязательно)

Хотя это и не требуется, можно сохранять базовую систему и среды пакета SDK для Azure изолированными друг от друга при использовании [виртуальной среды][virtualenv]. Выполните следующие команды для настройки и введите расположение виртуальной среды с постфиксом [venv][venv], например`cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```

### <a name="install-the-sdk"></a>Установка пакета SDK

Установите [пакет][pypi_computervision] SDK Компьютерного зрения Cognitive Services Azure для Python с помощью [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

После создания ресурса Компьютерного зрения вам потребуется его **регион** и один из его **ключей учетной записи**, чтобы создать экземпляр клиентского объекта.

Используйте эти значения при создании экземпляра клиентского объекта [ComputerVisionAPI][ref_computervisionclient]. 

### <a name="get-credentials"></a>Получение учетных данных

Используйте фрагмент приведенной ниже команды [Azure CLI][cloud_shell], чтобы заполнить две переменные среды **регионом** учетной записи Компьютерного зрения и одним из ее **ключей** (эти значения также можно найти на [портале Azure][azure_portal]). Фрагмент кода отформатирован для оболочки Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_REGION=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query location \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```

### <a name="create-client"></a>Создание клиента

После заполнения переменных среды `ACCOUNT_REGION` и `ACCOUNT_KEY` можно создать клиентский объект [ComputerVisionAPI][ref_computervisionclient].

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionAPI
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

import os
region = os.environ['ACCOUNT_REGION']
key = os.environ['ACCOUNT_KEY']

credentials = CognitiveServicesCredentials(key)
client = ComputerVisionAPI(region, credentials)
```

## <a name="usage"></a>Использование

После инициализации клиентского объекта [ComputerVisionAPI][ref_computervisionclient] вы можете следующее.

* Анализировать изображение. Это можно сделать по определенным особенностям, таким как лица, цвета, теги.   
* Создавать эскизы. Создайте пользовательские изображения JPEG, чтобы использовать их в качестве эскиза исходного изображения.
* Получать описание изображений. Получите описания изображений на основе предметной области. 

Дополнительные сведения об этой службе см. в статье [Что собой представляет компьютерное зрение][computervision_docs].

## <a name="examples"></a>Примеры

В следующих разделах приведено несколько фрагментов кода, охватывающих наиболее распространенные задачи Компьютерного зрения.

* [Анализ изображения](#analyze-an-image)
* [Получение списка предметных областей](#get-subject-domain-list).
* [Анализ изображений по области](#analyze-an-image-by-domain).
* [Получение текстового описания изображений](#get-text-description-of-an-image).
* [Получение рукописных текстов изображений](#get-text-from-image).
* [Создание эскизов](#generate-thumbnail).

### <a name="analyze-an-image"></a>Анализ изображения

Анализировать изображение на определенные особенности можно с помощью функции [`analyze_image`][ref_computervisionclient_analyze_image]. Используйте свойство [`visual_features`][ref_computervision_model_visualfeatures], чтобы задать типы для анализа изображений. Обычными значениями являются `VisualFeatureTypes.tags` и `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Получение списка предметных областей

Просмотрите предметные области, используемые для анализа изображений, с помощью функции [`list_models`][ref_computervisionclient_list_models]. Эти имена областей используются при [анализе изображения по области](#analyze-an-image-by-domain). Примером области является `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Анализ изображений по области

Анализировать изображение по предметной области можно с помощью функции [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]. Получите [список поддерживаемых предметных областей](#get-subject-domain-list), чтобы использовать правильные имена областей.  

```Python
domain = "landmarks"
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Получение текстового описания изображений

Получить текстовое описание изображения на основе языка можно с помощью функции [`describe_image`][ref_computervisionclient_describe_image]. Запросите несколько описаний с помощью свойства `max_description`, если нужно выполнить анализ текста на ключевые слова, связанные с изображением. Примеры текстовых описаний для следующих изображений: `a train crossing a bridge over a body of water`, `a large bridge over a body of water` и `a train crossing a bridge over a large body of water`.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Получение текста из изображения

Из изображения можно получить любой текст: рукописный или печатный. Для этого нужно вызвать два разных метода пакета SDK: [`recognize_text`][ref_computervisionclient_recognize_text] и [`get_text_operation_result`][ref_computervisionclient_get_text_operation_result]. Метод recognize_text выполняется асинхронно. Прежде чем извлекать текстовые данные, необходимо проверить результаты вызова get_text_operation_result на то, завершил ли выполнение [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes]. Результаты включают текст, а также координаты ограничительной рамки для текста. 

```Python
url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
mode = TextRecognitionMode.handwritten
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# SDK call
rawHttpResponse = client.recognize_text(url, mode, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
result = client.get_text_operation_result(operationId)

# Get data
if result.status == TextOperationStatusCodes.succeeded:

    for line in result.recognition_result.lines:
        print(line.text)
        print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Создание эскизов

Создать эскиз изображения (в формате JPG) можно с помощью [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]. Необязательно создавать эскиз в тех же пропорциях, что и исходное изображение. 

В этом примере используется пакет [Pillow][pypi_pillow] для локального сохранения нового эскиза изображения.

```Python
from PIL import Image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="general"></a>Общие сведения

При взаимодействии с клиентским объектом [ComputerVisionAPI][ref_computervisionclient] с помощью пакета SDK для Python используется класс [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] для возврата ошибок. Ошибки, возвращаемые службой, соответствуют тем же кодам состояния HTTP, которые возвращаются для запросов REST API.

Например, при попытке проанализировать изображение с неверным ключом будет возвращена ошибка `401`. В следующем фрагменте кода [ошибка][ref_httpfailure] корректно обрабатывается путем перехвата исключения и отображения дополнительной информации об этой ошибке.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and region are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Обработка временных ошибок с помощью повторных вызовов

При работе с клиентом [ComputerVisionAPI][ref_computervisionclient] вы можете столкнуться с временными сбоями, вызванными [ограничениями скорости][computervision_request_units], применяемыми службой, или другими временными проблемами, такими как отказ сети. Для получения дополнительной информации об обработке этих типов сбоев ознакомьтесь со статьей [Шаблон повторов][azure_pattern_retry] в руководстве по конструктивным шаблонам облачных решений и соответствующим [шаблоном автоматического выключения][azure_pattern_circuit_breaker].

## <a name="next-steps"></a>Дополнительная информация

### <a name="more-sample-code"></a>Больше примеров кода

Несколько примеров кода пакета SDK Компьютерного зрения для Python доступны в репозитории GitHub. Это примеры кода для дополнительных сценариев, обычно встречающихся при работе с Компьютерным зрением:

* [recognize_text][recognize-text].

### <a name="additional-documentation"></a>Дополнительная документация

Более подробную документацию по службе "Компьютерное зрение" см. [здесь][computervision_docs] на сайте docs.microsoft.com.

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python


[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-url--visual-features-none--details-none--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#list-models-custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#analyze-image-by-domain-model--url--language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#describe-image-url--max-candidates--1---language--en---custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_recognize_text]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#recognize-text-url--mode--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#get-text-operation-result-operation-id--custom-headers-none--raw-false----operation-config-
[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionapi?view=azure-python#generate-thumbnail-width--height--url--smart-cropping-false--custom-headers-none--raw-false--callback-none----operation-config-


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/

[recognize-text]:https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/vision/computer_vision_samples.py

