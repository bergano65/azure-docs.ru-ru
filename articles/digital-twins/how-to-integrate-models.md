---
title: Интеграция моделей, стандартных для отрасли
titleSuffix: Azure Digital Twins
description: Узнайте, как интегрировать стандартные отраслевые модели в ДТДЛ для Azure Digital двойников, используя специальное ДТДЛ онтологиес или преобразуя существующие онтологиес
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1866e9b233a1379474c061779ada09fd6d119107
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338403"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Интегрируйте отраслевые модели с ДТДЛ для Azure Digital двойников

Использование моделей, основанных на отраслевых стандартах, или использование стандартного представления онтологи, например RDF или OWL, обеспечивает обширную отправную точку при проектировании моделей цифровых двойников Azure. Использование отраслевых моделей также помогает при стандартизации и совместном использовании информации.

Для использования с Azure Digital двойников модель должна быть представлена на [**языке определения цифровых двойников**](concepts-models.md)на основе JSON-LD (дтдл). Поэтому в этой статье описывается, как представить стандартные отраслевые модели в ДТДЛ, интегрируя существующие отраслевые концепции с семантикой ДТДЛ, чтобы в Azure Digital двойников их можно было использовать. Затем модель ДТДЛ выступает в качестве источника истинности модели в Azure Digital двойников.

Существует три возможных пути интеграции моделей отрасли с ДТДЛ:
* **Внедрение**. Вы можете запустить решение с помощью дтдл онтологи с открытым кодом, который был создан на основе широко принятых отраслевых стандартов. 
* **Convert**. Если у вас уже есть модели, их необходимо преобразовать в дтдл.
* **Автор**. Вы всегда можете разрабатывать собственные пользовательские модели дтдл с нуля, как описано в разделе Практические руководства. [*Управление пользовательскими моделями*](how-to-manage-model.md).

## <a name="adopt-an-open-source-dtdl-ontology"></a>Внедрение ДТДЛ онтологи с открытым исходным кодом

Часто бывает проще начать с ДТДЛ онтологи с открытым кодом, чем с пустой страницы. 

Например, решения интеллектуальных зданий могут использовать [**реалестатекоре онтологи на основе дтдл**](https://github.com/Azure/opendigitaltwins-building)с открытым исходным кодом, который предоставляет общий Земля для моделирования интеллектуальных зданий, одновременно используя отраслевые стандарты для предотвращения реинвентион. 

Эти ДТДЛ онтологиес с открытым исходным кодом также предоставляют рекомендации по использованию и правильному расширению моделей. 

## <a name="convert-existing-models-to-dtdl"></a>Преобразование существующих моделей в ДТДЛ

Большинство отраслевых моделей (также называемых **онтологиес** ) основаны на семантических веб-стандартах, таких как [OWL](https://www.w3.org/OWL/), [RDF](https://www.w3.org/2001/sw/wiki/RDF)и [рдфс](https://www.w3.org/2001/sw/wiki/RDFS). 

Чтобы использовать модель с цифровым двойников Azure, она должна быть в формате ДТДЛ. В этом разделе описаны общие рекомендации по проектированию в форме **шаблона преобразования** для преобразования моделей на основе RDF в дтдл, чтобы их можно было использовать с цифровым двойников Azure. 

Он также содержит [ **пример кода преобразователя** для преобразователя RDF](#sample-converter-application), который был проверен для схемы [модуля](https://brickschema.org/ontology/) и может быть расширен для других схем в отрасли здания.

### <a name="conversion-pattern"></a>Шаблон преобразования

Существует несколько сторонних библиотек, которые можно использовать при преобразовании моделей на основе RDF в ДТДЛ. Некоторые из этих библиотек позволяют загрузить файл модели в граф. Вы можете выполнить цикл по графу для поиска конкретных конструкций РДФС и OWL и преобразовать их в ДТДЛ.   

В следующей таблице показано, как можно сопоставить конструкции РДФС и OWL с ДТДЛ. 

| Концепция РДФС и OWL | Конструкция РДФС/OWL | Концепция ДТДЛ | Конструкция ДТДЛ |
| --- | --- | --- | --- |
| Классы | `owl:Class`<br>Суффикс IRI<br>``rdfs:label``<br>``rdfs:comment`` | Интерфейс | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| используются подклассы ; | `owl:Class`<br>Суффикс IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Интерфейс | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Свойства DataType | `owl:DatatypeProperty`<br>`rdfs:label` или `INode`<br>`rdfs:label`<br>`rdfs:range` | Свойства интерфейса | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Свойства объекта | `owl:ObjectProperty`<br>`rdfs:label` или `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Связь | `type:Relationship`<br>`name`<br>`target` (или пропущено, если нет `rdfs:range` )<br>`comment`<br>`displayName`<br>

В следующем фрагменте кода C# показано, как файл модели RDF загружается в граф и преобразуется в ДТДЛ с помощью библиотеки [**дотнетрдф**](https://www.dotnetrdf.org/) . 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

### <a name="sample-converter-application"></a>Пример приложения преобразователя 

Существует пример приложения, которое преобразует файл модели на основе RDF в [дтдл (версия 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) для использования службой Digital двойников Azure. Она была проверена для схемы [модуля](https://brickschema.org/ontology/) и может быть расширена для других схем в здании отрасли (например, для [создания ТОПОЛОГИИ онтологи (Bot)](https://w3c-lbd-cg.github.io/bot/), [сети семантического датчика](https://www.w3.org/TR/vocab-ssn/)или [классов буилдингсмарт Industry Foundation (ИФК)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

Пример — это приложение командной строки .NET Core с именем **рдфтодтдлконвертер**.

Пример можно получить здесь: [**рдфтодтдлконвертер**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Чтобы загрузить код на компьютер, нажмите кнопку *скачать ZIP-файл* под заголовком на целевой странице примера. Будет загружен *ZIP* -файл с именем *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* , который затем можно распаковать и исследовать.

Этот пример можно использовать для просмотра шаблонов преобразования в контексте и для использования в качестве стандартного блока для собственных приложений, выполняющих преобразования модели в соответствии с конкретными потребностями.

## <a name="validate-and-upload-dtdl-models"></a>Проверка и отправка моделей ДТДЛ

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

После преобразования и проверки модели **ее можно передать в свой экземпляр Digital двойников для Azure**. Дополнительные сведения об этом процессе см. в разделе " [*Передача моделей*](how-to-manage-model.md#upload-models) " *руководства по управлению пользовательскими моделями*.

## <a name="next-steps"></a>Дальнейшие шаги 

Узнайте больше о проектировании моделей цифровых двойника и управлении ими:
 
* [*Основные понятия. Настраиваемые модели*](concepts-models.md).
* [*Практическое руководство. Управление настраиваемыми моделями*](how-to-manage-model.md).
* [*Пошаговое руководство. анализ и проверка моделей*](how-to-parse-models.md)
