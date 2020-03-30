---
title: Кнопка "Развертывание в Azure"
description: Используйте кнопку для развертывания шаблонов Управления ресурсами Azure из репозитория GitHub.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109049"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Используйте кнопку развертывания для развертывания шаблонов из репозитория GitHub

В этой статье описывается, как использовать кнопку **«Развертывание в Azure»** для развертывания шаблонов из репозитория GitHub. Кнопку можно добавить непосредственно в README.md файл в репозитории GitHub или на веб-страницу, которая ссылается на репозиторий.

## <a name="use-common-image"></a>Использование общего изображения

Чтобы добавить кнопку на веб-страницу или репозиторий, используйте следующее изображение:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Изображение отображается как:

![Кнопка "Развертывание в Azure"](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Создание URL-адреса для развертывания шаблона

Чтобы создать URL-адрес для шаблона, начните с необработанного URL-адреса шаблона в репо:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Затем URL-адрес кодирует его. Вы можете использовать онлайн-кодера или запустить команду. Следующий пример PowerShell показывает, как КОДировать значение.

```powershell
[uri]::EscapeDataString($url)
```

URL-адрес примера имеет следующее значение при кодировании URL-адреса.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Каждая ссылка начинается с одного и того же базового URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Добавьте ссылку шаблона, закодированную URL, к концу базового URL-адреса.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

У вас есть полный URL для ссылки.

## <a name="create-deploy-to-azure-button"></a>Создание кнопки «Развертывание в Azure»

Наконец, поместите ссылку и изображение вместе.

Чтобы добавить кнопку с Markdown в README.md файл в репозитории GitHub или на веб-странице, используйте:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

Для HTML используйте:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>Развертывание шаблона

Чтобы протестировать полное решение, выберите следующую кнопку:

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Портал отображает панель, которая позволяет легко обеспечить значения параметров. Параметры предварительно заполнены значениями по умолчанию из шаблона.

![Используйте портал для развертывания](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать больше о [Understand the structure and syntax of Azure Resource Manager templates](template-syntax.md)шаблонах, см.
