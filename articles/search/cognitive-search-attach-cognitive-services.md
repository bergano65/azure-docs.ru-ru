---
title: Подключение Cognitive Services к набору навыков в Поиск Azure
description: Инструкции по подключению подписки всех служб Cognitive Services к когнитивному набору навыков
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317242"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>Связывание ресурса Cognitive Services с набором навыков 

> [!NOTE]
> С 21 декабря 2018 г. вы сможете связывать ресурсы Cognitive Services с набором навыков службы "Поиск Azure". Благодаря этому мы сможем начать начислять плату за выполнение набора навыков. С этой даты мы также начнем начислять плату за извлечение изображений при открытии документов. Извлечение текста из документов будет выполняться бесплатно, как и прежде.
>
> За операции с применением встроенных навыков взимается [плата по мере использования по существующим тарифам для служб Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). За извлечение изображений взимается плата по тарифам для предварительной версии, как описано на странице [цен на службу "Поиск Azure"](https://go.microsoft.com/fwlink/?linkid=2042400).


Когнитивный поиск извлекает и обогащает данные, позволяя находить их в службе "Поиск Azure". *Когнитивные навыки* – это шаги по извлечению и обогащению. Набор навыков, который вызывается во время индексирования содержимого, определяется в *наборе навыков*. Набор навыков может включать [предопределенные](cognitive-search-predefined-skills.md) или настраиваемые навыки (см. [пример создания настраиваемого навыка](cognitive-search-create-custom-skill-example.md)).

В этой статье вы узнаете, как связать ресурс [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) с когнитивным набором навыков.

Выбранный ресурс Cognitive Services обеспечит работу встроенных когнитивных навыков. Этот ресурс также будет использоваться для выставления счетов. Всем выполняемым усовершенствованиям с помощью встроенных когнитивных навыков будут выставляться счета согласно с выбранным ресурсом Cognitive Services. Выставление счетов будет происходить по тарифу, как если бы вы выполнили ту же задачу используя ресурс Cognitive Services. См. на странице [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Ограничения, когда не выбирается ресурс Cognitive Services
Начиная с 1 февраля 2019 г., если не связать подписку Cognitive Services с имеющимся набором навыков, бесплатно можно будет расширить только небольшое количество документов (20 документов в день). 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>Связывание ресурса Cognitive Services с новым набором навыков

1. Как часть среды *импорта данных*, после подключения к источнику данных, произойдет переход на дополнительный шаг *Добавить когнитивный поиск*. 

1. Разверните раздел *Подключение Cognitive Services*. Вы увидите все ресурсы Cognitive Service, которые находятся в тех же регионах, что и Служба поиска. 
![Развертывание подключения Cognitive Service](./media/cognitive-search-attach-cognitive-services/attach1.png "Развертывание подключения Cognitive Services")

1. Выберите существующий ресурс Cognitive Services или *Создать новый ресурс Cognitive Services*. При выборе *Бесплатных (ограниченные обогащения данных) ресурсов*, можно бесплатно добавить только небольшое количество документов (20 документов в день). Щелкнув *Создать новый ресурс Cognitive Services*, откроется новая вкладка, которая позволит создать ресурс Cognitive Services. 

1. Создав новый ресурс, щелкните *Обновить*, чтобы обновить список ресурсов Cognitive Services, и выберите ресурс. 
![Выбранный ресурс Cognitive Service](./media/cognitive-search-attach-cognitive-services/attach2.png "Выбранный ресурс Cognitive Service")

1. После этого, можно развернуть раздел *Добавить обогащение*, чтобы выбрать конкретные когнитивные навыки, которые необходимо использовать для своих данных, и продолжить работу с оставшимся потоком.

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>Связывание ресурса Cognitive Services с существующим набором навыков

1. На странице "Общие сведения о службе" выберите вкладку *Наборы навыков*. ![Вкладка наборов навыков](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Вкладка наборов навыков")

1. *Щелкните* набор навыков, который необходимо изменить. Благодаря этому откроется колонка, которая позволит вам изменить набор навыков.

1. Выберите существующий ресурс Cognitive Services или *Создать новый ресурс Cognitive Services*. При выборе *Бесплатных (ограниченные обогащения данных) ресурсов*, можно бесплатно добавить только небольшое количество документов (20 документов в день). Щелкнув *Создать новый ресурс Cognitive Services*, откроется новая вкладка, которая позволит создать ресурс Cognitive Services. <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Создав новый ресурс, щелкните *Обновить*, чтобы обновить список ресурсов Cognitive Services, и выберите ресурс.
1. Для подтверждения изменений нажмите кнопку *ОК*.

## <a name="associating-a-cognitive-services-resource-programmatically"></a>Связывание ресурса Cognitive Services программным образом

При определении набора навыков программным образом, добавьте раздел `cognitiveServices`. Раздел должен содержать ключ ресурса Cognitive Services, который вы хотите связать с навыками, а также @odata.type, которому следует назначить "#Microsoft.Azure.Search.CognitiveServicesByKey". Этот шаблон показан в следующем примере.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>Пример: оценка стоимости открытия и обогащения документов
Можно оценить сколько стоит добавление данного типа документа. Упражнение ниже приводится только в качестве примера, который будет полезным для вас.

Допустим, у нас есть 1000 PDF-документов и, по нашим оценкам, в среднем в каждом документе 6 страниц. Предположим, что каждый из них имеет одно изображение на одной странице для этого упражнения. Предположим также, что в среднем на одной странице около 3000 символов. 

Теперь предположим, что необходимо выполнить следующие действия в рамках конвейера обогащения.
1. Как часть открытие документа, извлеките содержимое и изображения из документа.
1. В рамках обогащения, распознайте текст каждой извлеченной страницы, объедините текст для всех страниц, а затем извлеките каждую из организаций в сводный текст для всех изображений.

Оцените сколько будет стоить принять эти документы шаг за шагом.

Для 1000 документов:

1. Открывая документы, мы можем извлечь 6000 изображений. При условии, что каждые извлеченные 1000 изображений будут стоить 1 долл., в общем будет 6.00 долл.

2. Из каждых 6000 изображений текст будет извлечен. На английском языке когнитивные навыки OCR используют наилучший алгоритм (DescribeText). При условии, что 1000 изображений для анализа будут стоять 2,50 долл., мы выплатим 15,00 долл. для этого шага.

3. Для извлечения сущности нам нужно всего 3 текстовые записи на одной странице (каждая запись – 1000 символов). 3 текстовые записи на страницу * 6000 страниц = 18 000 текстовых записей. При условии, что 2 долл / 1000 текстовых записей это будет стоить нам 36 долл.

Всего мы заплатим 57 долларов, чтобы принять 1000 PDF-документов такого характера с описанным набором навыков.  В этом упражнении предполагалась самая дорогая цена за одну транзакцию, благодаря изучению цен их можно снизить. См. [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Дополнительная информация
+ [Цены на Поиск Azure](https://azure.microsoft.com/pricing/details/search/)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Создание набора навыков (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Сопоставление обогащенных полей](cognitive-search-output-field-mapping.md)
