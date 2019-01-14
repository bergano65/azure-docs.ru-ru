---
title: Связывание ресурса Cognitive Services с набором навыков в службе "Поиск Azure"
description: Инструкции по подключению подписки всех служб Cognitive Services к когнитивному набору навыков в службе "Поиск Azure"
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 52efa685bba330879365f56e547881d62a52a185
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000161"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>Связывание ресурса Cognitive Services с набором навыков в службе "Поиск Azure" 

Когнитивный поиск извлекает и обогащает данные, позволяя находить их в службе "Поиск Azure". *Когнитивные навыки* – это шаги по извлечению и обогащению. Набор навыков, который вызывается во время индексирования содержимого, определяется в *наборе навыков*. Набор навыков может использовать [стандартные навыки](cognitive-search-predefined-skills.md) или пользовательские навыки. Дополнительные сведения см. в статье [Пример создания пользовательского навыка с помощью API перевода текста](cognitive-search-create-custom-skill-example.md).

В этой статье вы узнаете, как связать ресурс [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) с когнитивным набором навыков.

Выбранный ресурс Cognitive Services обеспечит работу встроенных когнитивных навыков. Этот ресурс также будет использоваться для выставления счетов. По всем выполняемым обогащениям с помощью встроенных когнитивных навыков будут выставляться счета за выбранный ресурс Cognitive Services. Выставление счетов будет происходить по тарифу, как если бы вы выполнили ту же задачу, используя ресурс Cognitive Services. См. на странице [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

> [!NOTE]
> С 21 декабря 2018 г. можно связывать ресурсы Cognitive Services с набором навыков службы "Поиск Azure". Это позволяет нам взимать плату за выполнение набора навыков. С этого момента мы также начали начислять плату за извлечение изображений при открытии документов. Извлечение текста из документов будет выполняться бесплатно, как и прежде.
>
> За операции с применением встроенных навыков взимается [плата по мере использования по тарифам для служб Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). За извлечение изображений взимается плата по тарифам для предварительной версии, как описано на странице [цен на службу "Поиск Azure"](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>Ограничения, когда не выбирается ресурс Cognitive Services
Начиная с 1 февраля 2019 г., если не связать подписку Cognitive Services с имеющимся набором навыков, бесплатно можно будет расширить только небольшое количество документов (20 документов в день). 

## <a name="associate-a-cognitive-services-resource-with-a-new-skillset"></a>Связывание ресурса Cognitive Services с новым набором навыков

1. В ходе работы с мастером [импорта данных](search-import-data-portal.md) после подключения к источнику данных перейдите к дополнительному шагу **Добавить когнитивный поиск**. Это второй шаг в мастере.

1. Разверните раздел **Подключение Cognitive Services**. Здесь отображаются все ресурсы Cognitive Services, которые находятся в тех же регионах, что и служба поиска Azure.

   ![Открыт раздел подключения Cognitive Services](./media/cognitive-search-attach-cognitive-services/attach1.png "Expanded Attach Cognitive Services").

1. Выберите существующий ресурс Cognitive Services или щелкните **Создать ресурс Cognitive Services**. Если выбрать вариант **Бесплатно (ограниченные обогащения данных)**, можно бесплатно добавить только небольшое количество документов (20 документов в день). При выборе варианта **Создать ресурс Cognitive Services** откроется новая вкладка, где можно создать ресурс. 

1. Создав новый ресурс, щелкните **Обновить**, чтобы обновить список ресурсов Cognitive Services, и выберите ресурс. 

   ![Выбранный ресурс Cognitive Service](./media/cognitive-search-attach-cognitive-services/attach2.png "Selected Cognitive Service Resource")

1. Разверните раздел **Добавить обогащение**, чтобы выбрать конкретные когнитивные навыки, которые необходимо использовать для своих данных, и продолжить работу с оставшимся потоком.

## <a name="associate-a-cognitive-services-resource-with-an-existing-skillset"></a>Связывание ресурса Cognitive Services с существующим набором навыков

1. На странице **Общие сведения о службе** выберите вкладку **Наборы навыков**.

   ![Вкладка наборов навыков](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "Вкладка наборов навыков")

1. Выберите набор навыков, который хотите изменить. Откроется колонка, в которой можно изменить набор навыков.

1. Выберите существующий ресурс Cognitive Services или щелкните **Создать ресурс Cognitive Services**. Если выбрать вариант **Бесплатно (ограниченные обогащения данных)**, можно бесплатно добавить только небольшое количество документов (20 документов в день). При выборе варианта **Создать ресурс Cognitive Services** откроется новая вкладка, где можно создать ресурс.

   <n/> 
   <img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. Создав новый ресурс, щелкните **Обновить**, чтобы обновить список ресурсов Cognitive Services, и выберите ресурс.

1. Для подтверждения изменений нажмите кнопку **OK**.

## <a name="associate-a-cognitive-services-resource-programmatically"></a>Связывание ресурса Cognitive Services программным образом

При определении набора навыков программным образом добавьте раздел `cognitiveServices`. В разделе включите ключ ресурса Cognitive Services, который хотите связать с набором навыков. Также включите `@odata.type` и присвойте ему значение `#Microsoft.Azure.Search.CognitiveServicesByKey`. Этот шаблон приведен в примере ниже.

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimate-the-cost-of-document-cracking-and-enrichment"></a>Пример: оценка стоимости открытия и обогащения документов
Можно оценить, сколько стоит обогащение определенного типа документа. Следующее упражнение приводится только в качестве примера, который может быть полезным для вас.

Представьте, что у вас есть 1000 документов PDF. Вы оцениваете, что в среднем каждый из этих документов имеет 6 страниц. На каждой странице есть одно изображение. В среднем на одной странице около 3000 символов. 

Теперь предположим, что необходимо выполнить следующие действия в рамках конвейера обогащения.
1. Как часть открытие документа, извлеките содержимое и изображения из документа.
1. В рамках обогащения распознайте текст каждой извлеченной страницы, объедините текст для всех страниц, а затем извлеките каждую из организаций в сводный текст для всех изображений.

Оценим стоимость принятия этих 1000 документов шаг за шагом.

1. Открывая документы, вы можете извлечь 6000 изображений. При условии что каждая извлеченная 1000 изображений будет стоить 1 долл., общие затраты составят 6,00 долл.

2. Из всех 6000 изображений будет извлечен текст. На английском языке когнитивные навыки OCR используют наилучший алгоритм (DescribeText). При условии что 1000 изображений для анализа будет стоить 2,50 долл., вы выплатите 15,00 долл. за этот шаг.

3. Для извлечения сущности нужно всего 3 текстовые записи на страницу (каждая запись — 1000 символов). Три текстовые записи на страницу * 6000 страниц = 18 000 текстовых записей. При тарифе 2,00 долл. за 1000 текстовых записей этот шаг будет стоить 36,00 долл.

Всего вы заплатите 57 долларов, чтобы принять 1000 PDF-документов такого характера с описанным набором навыков. В этом упражнении мы взяли самый дорогой тариф за одну транзакцию. Таким образом, стоимость могла быть ниже. См. [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services).



## <a name="next-steps"></a>Дополнительная информация
+ [Цены на "Поиск Azure"](https://azure.microsoft.com/pricing/details/search/)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Создание набора навыков (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Сопоставление обогащенных полей](cognitive-search-output-field-mapping.md)
