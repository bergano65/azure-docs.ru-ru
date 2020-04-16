---
title: IntelliSense в инструментах аналитики потоков Azure для визуального кода studio
description: В этой статье описывается, как использовать функции IntelliSense в инструментах Azure Stream Analytics для Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394401"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense в инструментах аналитики потоков Azure для визуального кода studio

IntelliSense доступен для [языка потоковой аналитики в](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) [инструментах azure Stream Analytics для VS Code.](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview) IntelliSense — это вспомогательное средство для написания кода, включающее несколько возможностей: "Список членов", "Сведения о параметрах", "Краткие сведения" и "Завершить слово". Функции IntelliSense иногда называются другими именами, такими как «завершение кода», «помощь контенту» и «подсказка кода».

![Демонстрация IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>функции IntelliSense

Функции IntelliSense в инструментах Stream Analytics для VS Code питаются от языковой службы. Языковая служба анализирует исходный код и обеспечивает интеллектуальные завершения кода на основе семантики языка. Если языковая служба знает о возможных завершениях, предложения IntelliSense всплывают при вводе. При продолжении ввода список участников, таких как переменные и методы, фильтруется, чтобы включить только символы, содержащие набранные символы. При нажатии `Tab` `Enter` клавиш или клавиш IntelliSense вставляет выбранного вами участника.

Вы можете вызвать IntelliSense в любом окне редактора, `.`введя символ триггера, например, символ точки.

![intellisense автозавершение](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Предложения виджет поддерживает CamelCase фильтрации. Вы можете ввести буквы, которые uppercased в названии метода, чтобы ограничить предложения. Например, "cra" быстро будет воспитывать "создатьприложение".

### <a name="types-of-completions"></a>Типы завершений

Инструменты Stream Analytics для VS Code IntelliSense предлагают различные типы завершений, включая предложения языкового сервера, фрагменты и простые текстовые завершения на основе слов.

|       |         |       |
| ----- | ------- | ----- |
| Keywords | `keyword`
| Функции | `build-in function`, `user defined function`  |
| Имя набора данных| `input`, `output`, `intermediate result set`|
| Название столбца набора данных|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Завершение имен

Помимо автоматического завершения ключевых слов, инструменты Stream Analytics для VS Code считывают список входных и выходных имен, а также имена столбцов в источниках данных при их настройке. Расширение запоминает эту информацию, чтобы предоставить возможности завершения имен, которые полезны для ввода инструкций с несколькими нажатиями клавиш:

При кодировании не нужно покидать редактор для выполнения поиска по именам ввода рабочих мест, именам выходных данных и именам столбцов. Вы можете сохранить свой контекст, найти нудную информацию, вставить элементы непосредственно в свой код и заставить IntelliSense завершить ввод для вас.

Обратите внимание, что для использования завершения имен необходимо настроить локальный входной или живой ввод и сохранить файл конфигурации.

![завершение имен](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Сведения о параметрах

Опция IntelliSense **Parameter Info** открывает список параметров, который предоставляет информацию о количестве, именах и типах параметров, требуемых функцией. Параметр жирным шрифтом указывает следующий параметр, который требуется при вводе функции.

Список параметров отображается также и для вложенных функций. Если функция вводится в качестве параметра другой функции, в списке параметров перечисляются параметры для этой вложенной функции. Затем, когда список параметров вложенной функции заполнен, в списке продолжается отображение параметров внешней функции.

![информация о параметре](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Краткие сведения

В качестве предоставленного языковой службой в коде можно просмотреть **быструю информацию** для каждого идентификатора. Некоторые примеры идентификаторов: вход, выход, промежуточный набор результатов или функция. При перемещении указателя мыши по идентификатору ее декларация отображается в всплывающем окне. Отображаются свойства и схемы данных для входных данных, если настроены, и промежуточный набор данных.

![быстрая информация](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Устранение неполадок IntelliSense

Эта проблема вызвана отсутствием конфигурации ввода, которая предоставляет данные. Можно проверить, правильно ли настроен [локальный вход](visual-studio-code-local-run.md#define-a-local-input) или [живой вход.](visual-studio-code-local-run-live-input.md#define-a-live-stream-input)

## <a name="next-steps"></a>Дальнейшие действия

* [Быстрый запуск: Создайте работу по аналитике потоков Azure в коде Visual Studio](quick-create-vs-code.md)
* [Тестовая stream Analytics запрашивает локально с выборочными данными с помощью Visual Studio Code](visual-studio-code-local-run.md)
* [Тестовая аналитика потоков локально запрашивает против ввода потоковой передачи с помощью Visual Studio Code](visual-studio-code-local-run-live-input.md)
