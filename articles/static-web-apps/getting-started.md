---
title: Краткое руководство. Создание первого статического веб-приложения с помощью службы "Статические веб-приложения Azure"
description: Узнайте, как создать веб-сайт в службе "Статические веб-приложения Azure".
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: db3836e6171d187539b8615efcb5ab782c368020
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752398"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Краткое руководство. Создание первого статического веб-приложения

Служба "Статические веб-приложения Azure" публикует веб-сайты в рабочей среде, создавая приложения из репозитория GitHub. В этом кратком руководстве рассказывается о том, как развернуть веб-приложение в службе "Статические веб-приложения Azure" с помощью расширения Visual Studio Code.

Если у вас еще нет подписки Azure, создайте [бесплатную пробную учетную запись](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Предварительные требования

- [GitHub](https://github.com) .
- Учетная запись [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Расширение "Статические веб-приложения"](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) для Visual Studio Code

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Затем откройте Visual Studio Code и последовательно выберите **Файл > Открыть папку**, чтобы открыть репозиторий, который вы только что клонировали на компьютер в редакторе.

## <a name="create-a-static-web-app"></a>Создание статического веб-приложения

1. В Visual Studio Code на панели действий выберите логотип Azure, чтобы открыть окно расширений Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Логотип Azure":::

    > [!NOTE]
    > Необходимо войти в Azure и на GitHub. Если вы еще не вошли в Azure и на GitHub из Visual Studio Code, расширение предложит вам сделать это в процессе создания.

1. Наведите указатель мыши на надпись _Статические веб-приложения_ и выберите **знак плюс**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Имя приложения":::

1. В верхней части редактора откроется палитра команд и будет предложено присвоить имя приложению.

    Введите **my-first-static-web-app** и нажмите клавишу **ВВОД**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Создание Статического веб-приложения":::

1. Выберите ветвь **master** и нажмите клавишу **ВВОД**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Имя ветви":::

1. Выберите **/** в качестве расположения кода приложения и нажмите клавишу **ВВОД**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Расположение кода приложения":::

1. Расширение выполнит поиск расположение API в приложении. В этой статье не описывается реализация API.

    Выберите **Пропустить** и нажмите клавишу **ВВОД**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Расположение API":::

1. Выберите расположение, в котором в приложении будут создаваться файлы для рабочей среды.

    # <a name="no-framework"></a>[Без платформы](#tab/vanilla-javascript)

    Снимите флажок и нажмите клавишу **ВВОД**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Путь к файлам приложения":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Введите **dist/angular-basic** и нажмите клавишу **ВВОД**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Путь к файлам приложения Angular":::

    # <a name="react"></a>[React](#tab/react)

    Введите **build** и нажмите клавишу **ВВОД**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Путь к файлам приложения React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Введите **dist** и нажмите клавишу **ВВОД**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Путь к файлам приложения Vue":::

    ---

1. Выберите ближайшее расположение и нажмите клавишу **ВВОД**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Расположение ресурса":::

1. После создания приложения в Visual Studio Code отобразится уведомление с подтверждением.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Подтверждение создания":::

1. В окне обозревателя Visual Studio Code вернитесь в раздел _Статические веб-приложения_, щелкните правой кнопкой мыши **Рабочая среда** и выберите **Открыть на портале**, чтобы просмотреть приложение на портале Azure.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Пункт "Открыть портал"":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, вы можете удалить экземпляр службы "Статические веб-приложения Azure" с помощью расширения.

В окне обозревателя Visual Studio Code вернитесь в раздел _Статические веб-приложения_, щелкните правой кнопкой мыши **my-first-static-web-app** и выберите **Удалить**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Удаление приложения":::

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Добавление API](add-api.md)
