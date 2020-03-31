---
title: Читайте ввод в любом формате с помощью пользовательских десериализаторов .NET в Azure Stream Analytics
description: В этой статье объясняется формат сериализации и интерфейсы, определяющие пользовательские deserializers .NET для облачных и краевых заданий Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845262"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Читайте ввод в любом формате с помощью пользовательских десериализаторов .NET

Пользовательские десериализаторы .NET позволяют вашей работе Azure Stream Analytics считывать данные из форматов за пределами трех [встроенных форматов данных.](stream-analytics-parsing-json.md) В этой статье объясняется формат сериализации и интерфейсы, определяющие пользовательские десеризаторы .NET для облачных и краевых заданий Azure Stream Analytics. Есть также примеры deserializers для протокола буфера и CSV формата.

## <a name="net-custom-deserializer"></a>пользовательский десериализатор .NET

Ниже образцы кода являются интерфейсы, которые определяют `StreamDeserializer<T>`пользовательский десериализатор и реализации .

`UserDefinedOperator`является базовым классом для всех пользовательских операторов потоковой передачи. Он инициализирует `StreamingContext`, который обеспечивает контекст, который включает в себя механизм публикации диагностики, для которых вам нужно будет отладить любые проблемы с десериализатором.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Следующий фрагмент кода — это десериализация для потоковых данных. 

Пропущенные ошибки следует `IStreamingDiagnostics` испускать с помощью пройденный метод `UserDefinedOperator`инициализации. Все исключения будут рассматриваться как ошибки и десериализатор будет воссоздан. После определенного количества ошибок задание будет переходить в невыполненный статус.

`StreamDeserializer<T>`десериализирует поток в объект `T`типа. Должны быть выполнены следующие условия.

1. T - это класс или структура.
1. Все публичные поля в T являются либо
    1. Один из «sbyte, байт, короткий, ushort, int, uint, длинный, DateTime, строка, поплавок, двойной» или их недействительные эквиваленты.
    1. Другой структурировать или класс, следуя тем же правилам.
    1. Массив типа, `T2` который следует тем же правилам.
    1. IList,`T2` где T2 следует тем же правилам.
    1. Не имеет рекурсивных типов.

Параметром `stream` является поток, содержащий сериализованный объект. `Deserialize`возвращает коллекцию `T` экземпляров.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`предоставляет контекст, который включает механизм публикации диагностики для оператора пользователя.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`является диагностикой для пользовательских операторов, включая serializer, deserializer и пользовательские функции.

`WriteError`записывает сообщение об ошибке в диагностические журналы и отправляет ошибку в диагностику.

`briefMessage`— краткое сообщение об ошибке. Это сообщение отображается в диагностике и используется группой разработчиков для отладки. Не включайте конфиденциальную информацию и храните в сообщении менее 200 символов

`detailedMessage`— это подробное сообщение об ошибке, которое добавляется только в диагностические журналы в хранилище. Это сообщение должно быть менее 2000 символов.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Примеры десериализатора

В этом разделе показано, как писать пользовательские десериализаторы для Protobuf и CSV. Дополнительные примеры, такие как формат AVRO для захвата концентратора событий, посетите [аналитику Azure Stream Analytics на GitHub.](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)

### <a name="protocol-buffer-protobuf-format"></a>Протокол буфера (Protobuf) формат

Это пример с использованием формата буфера протокола.

Предположим следующее определение буфера протокола.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

Запуск `protoc.exe` из **Google.Protobuf.Tools** NuGet генерирует файл .cs с определением. Генерируемый файл здесь не отображается.

Следующий фрагмент кода — это реализация deserializer при условии включения генерируемого файла в проект. Эта реализация представляет собой лишь тонкую обертку над генерируемым файлом.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Следующий фрагмент кода представляет собой простой deserializer CSV, который также демонстрирует ошибки, распространяющиеся на размножение.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Формат сериализации для АПИ REST

Каждый вход Stream Analytics имеет **формат сериализации.** Для получения дополнительной информации о [Input REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) вариантах ввода см.

Следующий код Javascript является примером формата сериализации deserializer .NET при использовании REST API:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName`должен быть класс, `StreamDeserializer<T>`который реализует . Это описано в следующем разделе.

## <a name="region-support"></a>Поддержка регионов

Эта функция доступна в следующих регионах:

* центрально-западная часть США
* Северная Европа
* Восточная часть США
* западная часть США
* восточная часть США 2
* Западная Европа

Вы можете [запросить поддержку](https://aka.ms/ccodereqregion) для дополнительных регионов.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Когда эта функция будет доступна во всех регионах Azure?

Эта функция доступна в [6 регионах.](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support) Если вы заинтересованы в использовании этой функциональности в другом регионе, вы можете [отправить запрос.](https://aka.ms/ccodereqregion) Поддержка всех регионов Azure находится в дорожной карте.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Могу ли я получить доступ к MetadataPropertyValue из моих входных данных, аналогичных функции GetMetadataPropertyValue?

Эта функциональность не поддерживается. Если вам нужна эта возможность, вы можете проголосовать за этот запрос на [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Могу ли я поделиться реализацией deserializer с сообществом, чтобы другие могли извлечь выгоду?

После того как вы внедрили свой десериализатор, вы можете помочь другим, поделившись им с сообществом. Отправьте свой код в [репо Analytics SitHub Azure Stream.](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)

## <a name="next-steps"></a>Next Steps

* [пользовательские десеризаторы .NET для облачных заданий Azure Stream Analytics](custom-deserializer.md)
