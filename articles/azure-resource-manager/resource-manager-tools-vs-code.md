---
title: Использование Visual Studio Code для создания шаблонов Azure Resource Manager
description: Узнайте, как установить и использовать Visual Studio Code и расширение средств Azure Resource Manager.
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 4c0fd0968ce52e50a9171eecb3dfaebd1e2a4c46
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075016"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Использование Visual Studio Code для создания шаблонов Azure Resource Manager

Visual Studio Code — это упрощенный, многоплатформенный редактор с открытым исходным кодом. Расширение "средства шаблонов Azure Resource Manager" — это подключаемый модуль для разработки шаблонов диспетчер ресурсов. Расширение добавляет поддержку языка для шаблонов, чтобы обеспечить IntelliSense, выделение синтаксиса, встроенную справку и многие другие языковые функции. Вместе они предоставляют Рекомендуемые возможности для разработки шаблонов.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code

Visual Studio Code поддерживает MacOS, Windows и Linux.  Его можно установить из [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Установка расширения для средств Resource Manager

1. Откройте Visual Studio Code.
1. В меню слева выберите **расширения** . В меню **вид** выберите **расширения** , чтобы открыть панель расширения.

    ![Установить расширение средств диспетчер ресурсов Visual Studio Code](./media/resource-manager-tools-vs-code/resource-manager-visual-studio-code-tools-extension.png)
1. Выполните поиск по запросу **Диспетчер ресурсов**.
1. Выберите пункт **установить** в разделе **Azure Resource Manager инструменты**.

## <a name="the-extension-features"></a>Функции расширения

### <a name="colorization-for-template-language-expressions"></a>Выделение цветом для выражений языка шаблона

Параметры, переменные, функции, имена и выражения имеют цветовую кодировку, как показано на следующем снимке экрана:

![Раскраска расширений средств диспетчер ресурсов Visual Studio Code](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-colorization.png)

Представление структуры шаблонов упрощает навигацию по большим шаблонам.

### <a name="intellisense"></a>IntelliSense

Расширение шаблона диспетчер ресурсов известно возможное завершение имен функций, параметров, переменных и ссылок. При вводе появляется всплывающее предложение IntelliSense. Если вы продолжите вводить символы, список членов (переменных, методов и т. д.) фильтруется, добавляя только те элементы, которые содержат введенные символы. Нажатие клавиши **Tab** или **Ввод** вставляет выбранный элемент.

- Имена встроенных функций

    ![Функции IntelliSense Visual Studio Code расширения средств диспетчер ресурсов](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-functions.png)

- Ссылки на параметры

    ![Visual Studio Code параметры IntelliSense расширения средств диспетчер ресурсов](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-parameters.png)

- Ссылки на переменные

    ![Visual Studio Code переменные IntelliSense расширения средств диспетчер ресурсов](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-variables.png)

- свойства resourceGroup ()

    ![Функции IntelliSense Visual Studio Code расширения средств диспетчер ресурсов](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-resourcegroup.png)

Кроме того, IntelliSense также работает с свойствами подписки () и свойствами ссылок на переменные, являющиеся объектами.

### <a name="signature-help-for-function-parameters"></a>Справка по сигнатуре для параметров функции

При наведении указателя мыши на имена функций в расширении отображается справка по сигнатуре для параметров функции.

![Функция подписи расширений Visual Studio Code диспетчер ресурсов Tools](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Переход к определению для ссылок на переменные и параметры

Вы можете перейти к определению с помощью **сочетания клавиш CTRL + щелчок**или в контекстном меню, как показано на снимке экрана: ![Visual Studio Code диспетчер ресурсов Tools Extensions Go to Definition](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-context-menu.png)

Вы можете открыть определение рядом с помощью **клавиш Ctrl + Alt + щелчок**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Просмотр определений переменных и параметров

Чтобы открыть просматриваемый редактор, используйте контекстное меню, как показано на предыдущем снимке экрана.

На следующем снимке экрана показан редактор просмотра:

![Редактор просмотра Visual Studio Code инструментов диспетчер ресурсов Tools](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Найти все ссылки для переменных и параметров

Чтобы найти все ссылки, используйте контекстное меню, как показано на предыдущем снимке экрана.

На следующем снимке экрана показано, как будут выделены ссылки:

![Расширения средств диспетчер ресурсов инструментов Visual Studio Code найти все ссылки](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Переименование всех ссылок для переменных и параметров

Чтобы переименовать все ссылки для переменных и параметров, используйте контекстное меню, как показано на предыдущем снимке экрана.

### <a name="hover-for-parameter-description"></a>Наведите указатель мыши на описание параметра

![Определение Visual Studio Code расширений диспетчер ресурсов инструментов](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Парные фигурные скобки

Парные скобки выделяются сразу же после того, как курсор находится рядом с одним из них. При нажатии фигурной скобки также выделяется парная фигурная скобка, как показано на следующем снимке экрана:

![Visual Studio Code расширения средств диспетчер ресурсов средства сопоставления фигурных скобок](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Показывать ошибки и предупреждения

К ошибкам, идентифицируемым расширением, относятся:

- Ссылки на неопределенные параметры
- Неопределенные ссылки на переменные
- Нераспознанные имена функций
- Использование функции Reference () в определении переменной
- Неверное число аргументов в функциях

В число предупреждений входят следующие:

- Неиспользуемые параметры
- Неиспользуемые переменные

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о создании шаблонов Azure Resource Manager см. в разделе [учебник. Создание и развертывание первого шаблона Azure Resource Manager](template-tutorial-create-first-template.md).
- Чтобы выполнить быстрый запуск с помощью Visual Studio Code, см [. раздел Краткое руководство. Создание шаблонов Azure Resource Manager с помощью Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
