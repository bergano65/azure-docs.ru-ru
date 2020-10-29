---
title: Преобразование стандартных отраслевых моделей
titleSuffix: Azure Digital Twins
description: Изучите модель преобразования стандартных отраслевых моделей (RDF/OWL) в ДТДЛ
author: baanders
ms.author: baanders
ms.date: 10/28/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b9e298e55f06501bbbb4271f0643012c3cb6c5fe
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92915416"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Преобразование стандартных отраслевых моделей в ДТДЛ для Azure Digital двойников

Модели в Azure Digital двойников представлены на [**языке определения цифровых двойников**](concepts-models.md)на основе JSON-LD (дтдл). Если у вас есть модели, не входящие в Azure Digital двойников, основанные на отраслевых стандартах, таких как RDF или OWL, **их необходимо преобразовать в дтдл** , чтобы использовать их в Azure Digital двойников. Версия ДТДЛ станет источником истинности модели в Azure Digital двойников.

В этой статье описывается шаблон преобразования отрасли на основе RDF или пользовательских моделей в ДТДЛ. Сюда входят:
* **Руководство по уровню стратегии** , которое можно применять к различным стандартам и типам моделей
* [**Пример кода** для преобразователя, относящегося к RDF](#sample-converter-application)

## <a name="industry-models"></a>Отраслевые модели  

Использование отраслевых моделей предоставляет обширную отправную точку при проектировании модели цифровых двойников Azure. Использование отраслевых моделей также помогает при стандартизации и совместном использовании информации. 

Некоторые распространенные отраслевые модели включают:  

| Вертикальная отрасль | Модель |
| --- | --- | 
| Здания и управление ссудами | [реалестатекоре](https://www.realestatecore.io/)<br>[Схема модуля](https://brickschema.org/ontology/1.1/)<br>[Создание топологии Онтологи (BOT)](https://w3c-lbd-cg.github.io/bot/)<br>[Сеть семантического датчика](https://www.w3.org/TR/vocab-ssn/)<br>[Классы Буилдингсмарт Industry Foundation (ИФК)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Интеллектуальные города | [ЕТСИ НГСИ-LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Справочник по интеллектуальным приложениям (САРЕФ)](https://saref.etsi.org/)<br>[фиваре](https://www.fiware.org/)<br>[Открытие & гибких смарт-городов (ОАСК)](https://oascities.org/) |
| Сетка энергии | [Модель CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968)<br>[Модели адрм](http://www.adrm.com/) для торговли энергии и товара |
| Автомобилестроение | [Спецификация сигнала автомобиля](https://github.com/GENIVI/vehicle_signal_specification/tree/master/spec) |

В зависимости от потребностей можно также использовать ДТДЛ для настройки или расширения отраслевых моделей или разработки собственной настраиваемой модели с нуля. 

## <a name="create-and-edit-models"></a>Создание и изменение моделей

Первым шагом в моделировании является создание моделей. Вы можете создать и завершить редактирование стандартных отраслевых моделей перед их преобразованием в ДТДЛ. также можно преобразовать их в ДТДЛ на раннем этапе и продолжить их редактирование в виде ДТДЛ документов.

### <a name="with-industry-standards"></a>С отраслевыми стандартами

Большинство отраслевых моделей (также называемых **онтологиес** ) основаны на семантических веб-стандартах, таких как [OWL](https://www.w3.org/OWL/[), [RDF](https://www.w3.org/2001/sw/wiki/RDF)и [рдфс](https://www.w3.org/2001/sw/wiki/RDFS). 

В некоторых случаях может потребоваться создать или изменить модель с помощью средств модели на основе OWL. Для проектирования модели на основе OWL можно использовать любое количество средств разработки модели, включая следующие.
* [Вебпротéгé](https://protege.stanford.edu/products.php#web-protege) и [протéгé Desktop](https://protege.stanford.edu/products.php#desktop-protege) являются популярными примерами. Вы можете импортировать отраслевые модели в нескольких форматах, редактировать или расширять модель, а также экспортировать модель. 
* [Вебвовл](http://www.visualdataweb.de/webvowl/) — это еще одно популярное средство для визуализации моделей. 

Если вы создаете модель, используя какой-либо промышленный стандарт, который не ДТДЛ, вам потребуется преобразовать его в ДТДЛ и передать в двойников Azure Digital. 

#### <a name="common-model-file-formats"></a>Общие форматы файлов модели 

RDF, OWL и РДФС являются основными стандартными блоками семантического веб-сайта. 

**RDF** предоставляет концептуальную структуру для описания вещи в виде **триадов** . Тройные элементы состоят из трех частей: **Тема** , **предикат** и **объект** . Объекты могут состоять из URI. 

Ниже приведено несколько примеров RDF.

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

Эти примеры являются допустимыми RDF, но последняя инструкция семантически недействительна. В этом случае спецификация OWL входит в изображение. **OWL** определяет, что можно записать с помощью RDF, чтобы иметь допустимый онтологи.

Ниже приведен пример использования OWL: 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**Рдфс** предоставляет дополнительную семантику словаря, помогающую определять и описывать классы. Например, один из таких классов `rdfs:subClassOf` :

```
<Equipment> <subClassOf> <Asset>
```

Модели можно сохранять, импортировать и экспортировать во многих форматах файлов, в том числе:  
* RDF/XML 
* Черепаха (TTL) 
* OWL/XML 

### <a name="with-dtdl"></a>С ДТДЛ

В Azure Digital двойников для моделирования используется **язык определения цифрового двойника** на основе JSON-LD (дтдл). Любая модель, которая будет использоваться с Azure Digital двойников, должна быть либо первоначально написана, либо преобразована в ДТДЛ.

При работе с моделями в ДТДЛ можно использовать [**расширение дтдл**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   , доступное для  [Visual Studio Code](https://code.visualstudio.com/), которое обеспечивает проверку синтаксиса и другие функции для упрощения написания моделей дтдл.

Дополнительные сведения о моделях цифровых двойников Azure и их компонентах см. в статье [*пользовательские модели*](concepts-models.md) и [*практические руководства. Управление пользовательскими моделями*](how-to-manage-model.md).

## <a name="convert-models-to-dtdl"></a>Преобразование моделей в ДТДЛ

Чтобы использовать модель с цифровым двойников Azure, она должна быть в формате ДТДЛ. В этом разделе описано, как преобразовать модели на основе RDF в ДТДЛ, чтобы их можно было использовать с цифровым двойников Azure.

Существует несколько сторонних библиотек, которые можно использовать при преобразовании моделей на основе RDF в ДТДЛ. Некоторые из этих библиотек позволяют загрузить файл модели в граф. Вы можете выполнить цикл по графу для поиска конкретных конструкций РДФС и OWL и преобразовать их в ДТДЛ.   

В следующей таблице показано, как можно сопоставить конструкции РДФС и OWL с ДТДЛ. 

| Концепция РДФС и OWL | Конструкция РДФС/OWL | Концепция ДТДЛ | Конструкция ДТДЛ |
| --- | --- | --- | --- |
| Классы | `owl:Class`<br>Суффикс IRI<br>``rdfs:label``<br>``rdfs:comment`` | Интерфейс | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| используются подклассы ; | `owl:Class`<br>Суффикс IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Интерфейс | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Свойства DataType | `owl:DatatypeProperty`<br>`rdfs:label` или `INode`<br>`rdfs:label`<br>`rdfs:range` | Свойства интерфейса | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Свойства объекта | `owl:ObjectProperty`<br>`rdfs:label` или `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relationship | `type:Relationship`<br>`name`<br>`target` (или пропущено, если нет `rdfs:range` )<br>`comment`<br>`displayName`<br>

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

## <a name="validate-and-upload-dtdl-models"></a>Проверка и отправка моделей ДТДЛ

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

После преобразования и проверки модели **ее можно передать в свой экземпляр Digital двойников для Azure** . Дополнительные сведения об этом процессе см. в разделе " [*Передача моделей*](how-to-manage-model.md#upload-models) " *руководства по управлению пользовательскими моделями* .

## <a name="sample-converter-application"></a>Пример приложения преобразователя 

Существует пример приложения, которое преобразует файл модели на основе RDF в [дтдл (версия 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) для использования службой Digital двойников Azure. Пример — это приложение командной строки .NET Core с именем **рдфтодтдлконвертер** .

Пример можно получить здесь: [**рдфтодтдлконвертер**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Чтобы загрузить код на компьютер, нажмите кнопку *скачать ZIP-файл* под заголовком на целевой странице примера. Будет загружен *ZIP* -файл с именем *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip* , который затем можно распаковать и исследовать.

Этот пример можно использовать для просмотра шаблонов преобразования в контексте и для использования в качестве стандартного блока для собственных приложений, выполняющих преобразования модели в соответствии с конкретными потребностями.

## <a name="next-steps"></a>Дальнейшие действия 

Узнайте больше о проектировании моделей цифровых двойника и управлении ими:
 
* [*Основные понятия. Настраиваемые модели*](concepts-models.md).
* [*Практическое руководство. Управление настраиваемыми моделями*](how-to-manage-model.md).
* [*Пошаговое руководство. анализ и проверка моделей*](how-to-parse-models.md)