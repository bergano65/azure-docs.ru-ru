---
title: Анализ и проверка моделей
titleSuffix: Azure Digital Twins
description: Узнайте, как использовать библиотеку синтаксического анализатора для анализа моделей ДТДЛ.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 69b52be3a3eca2ab48ed09f6401780ea033f223c
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723985"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Анализ и проверка моделей с помощью библиотеки анализатора ДТДЛ

[Модели](concepts-models.md) в Azure Digital двойников определяются с помощью языка определения цифровых двойников на основе JSON-LD (дтдл). **Рекомендуется проверить модели в автономном режиме перед их отправкой в свой экземпляр Azure Digital двойников.**

Для этого в NuGet: [**Microsoft. Azure. дигиталтвинс. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/)предоставляется библиотека синтаксического анализа дтдл на стороне клиента .NET. 

Библиотеку синтаксического анализатора можно использовать непосредственно в коде C# или с помощью проекта с образцом кода независимо от языка, созданного на основе библиотеки анализатора: [**Пример средства проверки дтдл**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>Использование примера проверяющего элемента управления ДТДЛ

[**Проверяющий элемент управления дтдл**](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) — это пример проекта, который может проверить документы модели, чтобы убедиться, что дтдл является допустимым. Он основан на библиотеке анализатора .NET и не зависит от языка. Его можно получить с помощью кнопки *скачать ZIP-файл* на образце ссылки.

В исходном коде приведены примеры использования библиотеки анализатора. Пример проверяющего элемента управления можно использовать в качестве программы командной строки для проверки дерева каталогов файлов ДТДЛ. Он также предоставляет интерактивный режим.

В папке для примера проверяющего элемента управления ДТДЛ см. инструкции о *том, как* упаковать пример в автономный исполняемый файл.

После сборки автономного пакета и добавления исполняемого файла в путь можно запустить средство проверки с помощью этой команды в консоли на компьютере:

```cmd/sh
DTDLValidator
```

При использовании параметров по умолчанию образец будет искать `*.json` файлы в текущем каталоге и во всех подкаталогах. Можно также добавить следующий параметр, чтобы образец поиска в указанном каталоге и всех подкаталогах для файлов с расширением *. дтдл*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Можно добавить `-i` параметр для примера, чтобы перейти в интерактивный режим:

```cmd/sh
DTDLValidator -i
```

Дополнительные сведения об этом примере см. в разделе исходный код или запуск `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>Использование библиотеки анализатора .NET 

Библиотека [**Microsoft. Azure. дигиталтвинс. Parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) предоставляет доступ к ОПРЕДЕЛЕНИЯм дтдл с помощью модели, которая действует как эквивалент отражения C# для дтдл. Эту библиотеку можно использовать независимо от любого [пакета SDK Azure Digital двойников](how-to-use-apis-sdks.md), особенно для проверки дтдл в визуальном или текстовом редакторе. Прежде чем пытаться передать их в службу, полезно убедиться, что файлы определения модели действительны.

Чтобы использовать библиотеку анализатора, вы предоставляете ему набор документов ДТДЛ. Как правило, эти документы модели извлекаются из службы, но они также могут быть доступны локально, если клиент был ответственным за их загрузку в службу в первую очередь. 

Ниже приведен общий рабочий процесс для использования средства синтаксического анализа:
1. Извлечение некоторых или всех документов ДТДЛ из службы.
2. Передайте в средство синтаксического анализа возвращенные документы ДТДЛ в памяти.
3. Средство синтаксического анализа проверяет набор документов, переданных в него, и возвращает подробные сведения об ошибке. Эта возможность полезна в сценариях редактора.
4. Используйте API-интерфейсы средства синтаксического анализа, чтобы продолжить анализ моделей, входящих в набор документов. 

Ниже перечислены возможности средства синтаксического анализа.
* Получить все реализованные интерфейсы модели (содержимое `extends` раздела интерфейса).
* Возвращает все свойства, данные телеметрии, команды, компоненты и связи, объявленные в модели. Эта команда также получает все метаданные, содержащиеся в этих определениях, и учитывает наследование ( `extends` разделы).
* Получение всех определений сложных моделей.
* Определите, может ли модель быть назначена другой модели.

> [!NOTE]
> Устройства [IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) используют небольшой вариант синтаксиса для описания их функциональности. Этот вариант синтаксиса является семантически совместимым подмножеством ДТДЛ, которое используется в Azure Digital двойников. При использовании библиотеки анализатора нет необходимости знать, какой вариант синтаксиса использовался для создания ДТДЛ для цифрового двойника. Синтаксический анализатор всегда по умолчанию возвращает ту же модель для синтаксиса PnP и Azure Digital двойников.

### <a name="code-with-the-parser-library"></a>Код с библиотекой анализатора

Библиотеку анализатора можно использовать напрямую, например, для проверки моделей в собственном приложении или для создания динамических, управляемых моделями пользовательского интерфейса, панелей мониторинга и отчетов.

Для поддержки приведенного ниже примера кода синтаксического анализатора рассмотрим несколько моделей, определенных в экземпляре Azure Digital двойников.

> [!TIP] 
> `dtmi:com:contoso:coffeeMaker`Модель использует синтаксис *модели возможностей* , который подразумевает, что он был установлен в службе путем подключения устройства PnP, предоставляющего эту модель.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

В следующем коде показан пример использования библиотеки синтаксического анализатора для отражения этих определений в C#:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

Завершив написание моделей, см. статью как передать их (и выполнить другие операции управления) с помощью API-интерфейсов Дигиталтвинсмоделс:
* [*Практическое руководство. Управление настраиваемыми моделями*](how-to-manage-model.md).