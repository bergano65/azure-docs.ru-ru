---
title: Основные операции ввода-вывода | Карты Microsoft Azure
description: Узнайте, как эффективно считывать и записывать данные XML и разделители с помощью основных библиотек из модуля пространственных операций ввода-вывода.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d2a82fd5d9ba958fd6490a83ecbbe0a4bdf820a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370938"
---
# <a name="core-io-operations"></a>Основные операции ввода-вывода

Помимо предоставления средств для чтения файлов пространственных данных, модуль пространственных операций ввода-вывода предоставляет базовые базовые библиотеки для чтения и записи XML и данных с разделителями быстро и эффективно.

Пространство имен `atlas.io.core` содержит два класса низкого уровня, которые позволяют быстро считывать и записывать данные в формате CSV и XML. Эти базовые классы являются мощными модулями чтения и записи пространственных данных в модуле пространственного ввода-вывода. Вы можете использовать их для добавления дополнительных функций чтения и записи для файлов CSV или XML.
 
## <a name="read-delimited-files"></a>Чтение файлов с разделителями

Класс `atlas.io.core.CsvReader` считывает строки, содержащие наборы данных с разделителями. Этот класс предоставляет два метода для чтения данных:

- Функция `read` считывает полный набор данных и возвращает двумерный массив строк, представляющий все ячейки набора данных с разделителями.
- Функция `getNextRow` считывает каждую строку текста в наборе данных с разделителями и возвращает массив строк, представляющий все ячейки в этой строке набора данных. Пользователь может обработать строку и освободить все ненужные памяти из этой строки перед обработкой следующей строки. Таким образом, функция является более эффективной в памяти.

По умолчанию модуль чтения будет использовать в качестве разделителя символ запятой. Однако разделитель можно изменить на любой отдельный символ или задать для `'auto'`. Если задано значение `'auto'`, средство чтения проанализирует первую строку текста в строке. Затем он выбирает наиболее распространенный символ из приведенной ниже таблицы для использования в качестве разделителя.

| | |
| :-- | :-- |
| Запятая | `,` |
| Вкладка | `\t` |
| Pipe | `|` |

Этот модуль чтения также поддерживает текстовые квалификаторы, используемые для обработки ячеек, содержащих символ разделителя. Символ quote (`'"'`) является ограничителем текста по умолчанию, но его можно заменить на любой отдельный символ.

## <a name="write-delimited-files"></a>Запись файлов с разделителями

`atlas.io.core.CsvWriter` записывает массив объектов в виде строки с разделителями. Любой отдельный символ может использоваться как разделитель или как ограничитель текста. По умолчанию разделителем является запятая (`','`), а в качестве ограничителя текста по умолчанию используется символ кавычки (`'"'`).

Чтобы использовать этот класс, выполните следующие действия:

- Создайте экземпляр класса и при необходимости задайте настраиваемый разделитель или ограничитель текста.
- Запишите данные в класс с помощью функции `write` или функции `writeRow`. Для функции `write` передайте двухмерный массив объектов, представляющий несколько строк и ячеек. Чтобы использовать функцию `writeRow`, передайте массив объектов, представляющий строку данных с несколькими столбцами.
- Вызовите функцию `toString`, чтобы получить строку с разделителями. 
- При необходимости вызовите метод `clear`, чтобы модуль записи можно было повторно использовать и сократить его выделение ресурсов, или вызвать метод `delete` для удаления экземпляра модуля записи.

> [!Note]
> Число записанных столбцов будет ограничено количеством ячеек в первой строке данных, передаваемых модулю записи.

## <a name="read-xml-files"></a>Чтение XML-файлов

`atlas.io.core.SimpleXmlReader` класс быстрее при анализе XML-файлов, чем `DOMParser`. Однако класс `atlas.io.core.SimpleXmlReader` требует, чтобы XML-файлы были правильно отформатированы. XML-файлы, которые не были правильно отформатированы, например отсутствующие закрывающие теги, скорее всего, приведут к ошибке.

В следующем коде показано, как использовать класс `SimpleXmlReader` для анализа XML-строки в объект JSON и сериализации его в нужный формат.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Запись XML-файлов

Класс `atlas.io.core.SimpleXmlWriter` записывает хорошо отформатированный XML-код в память в удобном виде.

В следующем коде показано, как использовать класс `SimpleXmlWriter` для создания XML-строки с правильным форматом.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

Созданный код XML из приведенного выше кода будет выглядеть следующим образом.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о классах и методах, которые используются в этой статье:

> [!div class="nextstepaction"]
> [ксвреадер](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [ксввритер](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [симплексмлреадер](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [симплексмлвритер](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Сведения о поддерживаемых форматах данных](spatial-io-supported-data-format-details.md)