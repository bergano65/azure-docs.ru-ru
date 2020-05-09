---
title: Чтение входных данных в любом формате с помощью пользовательских десериализаторов .NET в Azure Stream Analytics
description: В этой статье описывается формат сериализации и интерфейсы, определяющие пользовательские десериализаторы .NET для Azure Stream Analytics облачных и граничных заданий.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 5cde80bf3205557884dfe8f2b8f5e79031bbca69
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612067"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Чтение входных данных в любом формате с помощью пользовательских десериализаторов .NET

Пользовательские десериализаторы .NET позволяют задаче Azure Stream Analytics считывать данные из форматов, которые выходят за рамки трех [встроенных форматов данных](stream-analytics-parsing-json.md). В этой статье объясняется формат сериализации и интерфейсы, определяющие пользовательские десериализаторы .NET для Azure Stream Analytics облачных и граничных заданий. Также существуют примеры десериализаторов для буфера протокола и формата CSV.

## <a name="net-custom-deserializer"></a>Пользовательский десериализатор .NET

Ниже приведены примеры кода, которые определяют пользовательский десериализатор и реализуют `StreamDeserializer<T>`его.

`UserDefinedOperator`является базовым классом для всех операторов настраиваемой потоковой передачи. Он инициализирует `StreamingContext`, который предоставляет контекст, который включает механизм для публикации диагностики, для которого необходимо выполнить отладку любых проблем с десериализатором.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

В следующем фрагменте кода показана десериализация для потоковой передачи данных. 

Пропущенные ошибки должны выдаваться с `IStreamingDiagnostics` помощью `UserDefinedOperator`метода Initialize. Все исключения будут рассматриваться как ошибки, и десериализатор будет создан повторно. После определенного числа ошибок задание перейдет в состояние сбоя.

`StreamDeserializer<T>`Десериализует поток в объект типа `T`. Должны быть выполнены следующие условия.

1. T является классом или структурой.
1. Все открытые поля в T являются либо
    1. Один из [SByte, Byte, Short, ushort, int, uint, Long, DateTime, String, float, Double] или их эквиваленты, допускающие значение null.
    1. Другая структура или класс, следующие за теми же правилами.
    1. Массив типа `T2` , который соответствует тем же правилам.
    1. IList`T2` , где T2, следуют тем же правилам.
    1. Не имеет рекурсивных типов.

Параметр `stream` — это поток, содержащий сериализованный объект. `Deserialize`Возвращает коллекцию `T` экземпляров.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`предоставляет контекст, который включает механизм для публикации диагностики для оператора пользователя.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`— Это диагностика для определяемых пользователем операторов, включая сериализатор, десериализатор и определяемые пользователем функции.

`WriteError`Записывает сообщение об ошибке в журналы ресурсов и отправляет ошибку диагностике.

`briefMessage`— краткое сообщение об ошибке. Это сообщение отображается в диагностике и используется группой разработки продукта для отладки. Не включайте конфиденциальную информацию и не оставляйте сообщение короче 200 символов.

`detailedMessage`— Это подробное сообщение об ошибке, которое добавляется только в журналы ресурсов в хранилище. Это сообщение должно содержать менее 2000 символов.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Примеры десериализатора

В этом разделе показано, как создавать пользовательские десериализаторы для protobuf и CSV. Дополнительные примеры, например формат AVRO для записи концентратора событий, см. [на Azure Stream Analytics на сайте GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Формат буфера протокола (protobuf)

Это пример использования формата буфера протокола.

Предположим, что определено следующее определение буфера протокола.

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

Выполнение `protoc.exe` из **Google. protobuf. Tools** NuGet создает CS-файл с определением. Созданный файл не показан здесь.

Следующий фрагмент кода является реализацией десериализатора при условии, что созданный файл включен в проект. Эта реализация является просто тонкой оболочкой для созданного файла.

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

Следующий фрагмент кода является простым десериализатором CSV, который также демонстрирует распространение ошибок.

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

## <a name="serialization-format-for-rest-apis"></a>Формат сериализации для интерфейсов API RESTFUL

Каждый Stream Analytics вход имеет **Формат сериализации**. Дополнительные сведения о параметрах ввода см. в документации по [входным REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) .

Следующий код JavaScript является примером формата сериализации десериализатора .NET при использовании REST API:

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

`serializationClassName`должен быть классом, реализующим интерфейс `StreamDeserializer<T>`. Это описано в следующем разделе.

## <a name="region-support"></a>Поддержка регионов

Эта функция доступна в следующих регионах:

* центрально-западная часть США
* Северная Европа
* Восточная часть США
* западная часть США
* восточная часть США 2
* Западная Европа

Вы можете [запросить поддержку](https://aka.ms/ccodereqregion) для дополнительных регионов.

## <a name="frequently-asked-questions"></a>Вопросы и ответы

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Когда эта функция будет доступна во всех регионах Azure?

Эта функция доступна в [6 регионах](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support). Если вы заинтересованы в использовании этой функции в другом регионе, можно [Отправить запрос](https://aka.ms/ccodereqregion). Поддержка всех регионов Azure включена в план.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Можно ли получить доступ к Метадатапропертивалуе из входных данных, похожих на функцию Жетметадатапропертивалуе?

Эта функциональность не поддерживается. Если вам нужна эта возможность, вы можете проголосовать за этот запрос на [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Можно ли поделиться реализацией десериализатора с сообществом, чтобы другие пользователи могли воспользоваться преимуществами?

После реализации десериализатора вы можете помочь другим пользователям, чтобы поделиться им с сообществом. Отправьте код в [репозиторий Azure Stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitation-of-using-custom-deserializers-in-stream-analytics"></a>Каковы другие ограничения использования пользовательских десериализаторов в Stream Analytics?

Если входные данные имеют формат protobuf с схемой, содержащей тип Мапфиелд, то реализовать пользовательский десериализатор будет невозможно. Мы работаем над тем, чтобы поддерживать этот тип вперед.

## <a name="next-steps"></a>Next Steps

* [Пользовательские десериализаторы .NET для Azure Stream Analytics облачных заданий](custom-deserializer.md)
