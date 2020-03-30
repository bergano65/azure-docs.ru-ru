---
title: Используйте Visual Studio Code для создания шаблонов
description: Узнайте, как установить и использовать visual Studio Code и расширение инструментов управления ресурсами Azure.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273635"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Используйте Visual Studio Code для создания шаблонов менеджера ресурсов Azure

Visual Studio Code — это легкий, многоплатформенный редактор с открытым исходным кодом. Расширение Azure Resource Manager Template Tools — это плагин для разработки шаблонов Ресурсного менеджера. Расширение добавляет языковую поддержку шаблонов, чтобы дать вам intellisense, выделение синтаксиса, помощь в строке и многие другие функции языка. Вместе они предоставляют рекомендуемый опыт разработки шаблонов.

## <a name="install-visual-studio-code"></a>Установка Visual Studio Code

Visual studio Code поддерживает MacOS, Windows и Linux.  Он может быть установлен из [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Установка расширения для средств Resource Manager

1. Откройте Visual Studio Code.
1. Выберите **расширения** из левого меню. Или из меню **View** выберите **расширения,** чтобы открыть панель расширений.

    ![Установка visual Studio Code Resource Manager Tools](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Поиск **менеджера ресурсов**.
1. Выберите **Установку** под **инструментами управления ресурсами Azure.**

## <a name="the-extension-features"></a>Функции расширения

### <a name="colorization-for-template-language-expressions"></a>Раскраска для выражений языка шаблонов

Параметры, переменные, функции, имена и выражения закодированы цветом, как показано на следующем скриншоте:

![Visual Studio Code Resource Manager инструменты расширения окраски](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

Представление набросков шаблонов позволяет легко перемещаться по большим шаблонам.

### <a name="intellisense"></a>Intellisense

Расширение шаблона менеджера ресурсов знает возможные завершения для имен функций, параметров, переменных и ссылок. Предложения IntelliSense всплывают при вводе. Если вы продолжаете печатать символы, список участников (переменные, методы и т.д.) фильтруется, чтобы включить только символы, содержащие набранные символы. Нажатие **вкладки** или **ввода** вставляет выбранный участник.

- Встроенные имена функций

    ![Visual Studio Code Resource Manager инструменты расширения функций intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Ссылки на параметры

    ![Visual Studio Code Resource Manager инструменты расширения intellisense параметры](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Переменные ссылки

    ![Visual Studio Code Resource Manager инструменты расширения intellisense переменных](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- ресурсгруппа () свойства

    ![Visual Studio Code Resource Manager инструменты расширения функций intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Кроме того, intellisense также работает с свойствами подписки () и свойствами ссылок на переменные, которые являются объектами.

### <a name="signature-help-for-function-parameters"></a>Справка по подписи для параметров функции

При нависке над именами функций расширение показывает справку подписи для параметров функции.

![Visual Studio Code Resource Manager инструменты расширения функции подписи](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Перейти к определению для переменных и параметрных ссылок

Вы можете перейти к определению с **помощью Ctrl-Click**, или ![с помощью контекстного меню, как показано на скриншоте: Visual Studio Code Resource Manager инструменты расширения перейти к определению](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

Вы можете открыть определение в сторону с **помощью Ctrl-Alt-Нажмите**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Подглядывание для определения переменных и параметров

Чтобы открыть заглянунный редактор, используйте контекстное меню, как показано на предыдущем скриншоте.

На следующем скриншоте показан редактор peek:

![Visual Studio Code Resource Manager инструменты расширения заглянуть редактор](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Найти все ссылки на переменные и параметры

Чтобы найти все ссылки, используйте контекстное меню, как показано на предыдущем скриншоте.

На следующем скриншоте показано, как высвещаются ссылки:

![Визуальный Studio Code Ресурс Менеджер инструменты расширения найти все ссылки](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Переименуй все ссылки для переменных и параметров

Чтобы переименовать все ссылки для переменных и параметров, используйте контекстное меню, как показано на предыдущем скриншоте.

### <a name="hover-for-parameter-description"></a>Нависшие для описания параметров

![Visual Studio Code Resource Manager инструменты расширений нависшие определение](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Соответствие скобок

Соответствующие скобки подчеркиваются, как только курсор находится рядом с одним из них. При нажатии скобки, соответствующие скобки также выделены, как показано на следующем скриншоте:

![Visual Studio Code Resource Manager инструменты расширения скобки соответствия](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Отображение ошибок и предупреждений

Ошибки, выявленные расширением, включают:

- Неопределенные ссылки параметра
- Неопределенные ссылки переменной
- Непризнанные имена функций
- использование функций в переменном определении
- Неправильное количество аргументов в функциях

Предупреждения включают в себя:

- Неиспользованные параметры
- Неиспользованные переменные

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать о создании шаблонов [Tutorial: Create and deploy your first Azure Resource Manager template](template-tutorial-create-first-template.md)управления ресурсами Azure, см.
- Чтобы пройти быстрый запуск с помощью Visual Studio Code, [см.](quickstart-create-templates-use-visual-studio-code.md)
