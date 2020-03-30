---
title: Основные операции IO Карты Microsoft Azure
description: Узнайте, как эффективно читать и писать XML и делимитировать данные с помощью основных библиотек из модуля пространственного IO.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371440"
---
# <a name="core-io-operations"></a>Основные операции IO

В дополнение к предоставлению инструментов для чтения пространственных файлов данных, модуль пространственного IO предоставляет основные базовые библиотеки для чтения и записи XML и делегированных данных быстро и эффективно.

Пространство `atlas.io.core` имен содержит два низкоуровневых класса, которые могут быстро читать и писать данные CSV и XML. Эти базовые классы питаются читателями пространственных данных и авторами в модуле Spatial IO. Не стесняйтесь использовать их, чтобы добавить дополнительную поддержку чтения и письма для ФАЙЛОВ CSV или XML.
 
## <a name="read-delimited-files"></a>Читать делимитированные файлы

Класс `atlas.io.core.CsvReader` считывает строки, содержащие делимитированные наборы данных. Этот класс предоставляет два метода для чтения данных:

- Функция `read` считывает полный набор данных и возвращает двухмерный массив строк, представляющих все ячейки набора делимитированных данных.
- Функция `getNextRow` считывает каждую строку текста в наборе делимитированных данных и возвращает массив строки, представляющий все ячейки в этой строке набора данных. Пользователь может обработать строку и избавиться от любой ненужной памяти из этой строки перед обработкой следующего ряда. Таким образом, функция является более эффективной памяти.

По умолчанию читатель будет использовать символ запятой в качестве делимитера. Тем не менее, делимитер может быть изменен `'auto'`на любой отдельный символ или установлен на . При установке на `'auto'`, читатель будет анализировать первую строку текста в строке. Затем он выберет наиболее распространенный символ из приведенной ниже таблицы для использования в качестве делимитеда.

| | |
| :-- | :-- |
| Запятая | `,` |
| Вкладка | `\t` |
| Pipe | `|` |

Этот читатель также поддерживает квалификаторы текста, которые используются для обработки ячеек, содержащих символ делимитера. Символ цитаты ()`'"'`является квалификатором текста по умолчанию, но его можно изменить на любой отдельный символ.

## <a name="write-delimited-files"></a>Написание делимитированных файлов

Записывает `atlas.io.core.CsvWriter` массив объектов в виде разграниченной строки. Любой символ может быть использован в качестве делимитера или текстового квалификатора. Делимитед по умолчанию`','`является запятой ()`'"'`и квалификатортекста текста по умолчанию является цитатой () символом.

Чтобы использовать этот класс, выполните ниже приведенные ниже действия:

- Создайте экземпляр класса и дополнительно установите пользовательский квалификатор или текстовый квалификатор.
- Напишите данные в `write` класс, `writeRow` используя функцию или функцию. Для `write` функции передайте двумерный массив объектов, представляющих несколько рядов и ячеек. Чтобы использовать `writeRow` функцию, передайте массив объектов, представляющих ряд данных, несколькими столбцов.
- Вызов `toString` функции для извлечения разграниченной строки. 
- Дополнительно, вызов `clear` метода, чтобы сделать писателя многоразовым и `delete` уменьшить его распределение ресурсов, или вызвать метод, чтобы избавиться от экземпляра автором.

> [!Note]
> Количество написанных столбцов будет ограничено числом ячеек в первом ряду данных, передаваемых писателю.

## <a name="read-xml-files"></a>Читать файлы XML

Класс `atlas.io.core.SimpleXmlReader` быстрее при разборе файлов XML, чем `DOMParser`. Тем не `atlas.io.core.SimpleXmlReader` менее, класс требует, чтобы файлы XML были хорошо отформатированы. XML-файлы, которые не отформатированы хорошо, например, отсутствующие теги закрытия, скорее всего, приведут к ошибке.

Следующий код демонстрирует, как `SimpleXmlReader` использовать класс для разбора строки XML в объект JSON и сериализации его в желаемый формат.

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

## <a name="write-xml-files"></a>Запись файлов XML

Класс `atlas.io.core.SimpleXmlWriter` пишет хорошо отформатированный XML эффективным способом памяти.

Следующий код демонстрирует, как `SimpleXmlWriter` использовать класс для создания хорошо отформатированной строки XML.

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

Генерируемый XML из приведенного выше кода будет выглядеть следующим образом.

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
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Дополнительные примеры кода для добавления в карты см. в следующих статьях:

> [!div class="nextstepaction"]
> [Детали формата поддерживаемых данных](spatial-io-supported-data-format-details.md)