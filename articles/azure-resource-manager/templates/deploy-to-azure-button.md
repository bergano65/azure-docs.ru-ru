---
title: Кнопка "Развертывание в Azure"
description: Используйте кнопку для развертывания шаблонов Azure Resource Manager из репозитория GitHub.
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 9fe69eba2a91bf19e0662ae071c222905c348666
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87079472"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Использование кнопки развертывания для развертывания шаблонов из репозитория GitHub

В этой статье описывается, как использовать кнопку **развертывание в Azure** для развертывания шаблонов из репозитория GitHub. Вы можете добавить кнопку непосредственно в файл README.md в репозитории GitHub или на веб-страницу, которая ссылается на репозиторий. Этот метод поддерживает только развертывание на уровне группы ресурсов.

## <a name="use-common-image"></a>Использовать общий образ

Чтобы добавить кнопку на веб-страницу или в репозиторий, используйте следующий рисунок:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Изображение выглядит следующим образом:

![Кнопка "Развертывание в Azure"](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Создание URL-адреса для развертывания шаблона

Чтобы создать URL-адрес для шаблона, начните с необработанного URL-адреса шаблона в репозитории. Чтобы просмотреть необработанный URL-адрес, выберите **необработанный**.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="выбрать необработанный":::

URL-адрес имеет формат:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Затем URL-адрес кодирует его. Можно использовать интерактивный кодировщик или выполнить команду. В следующем примере PowerShell показано, как кодировать значение в URL-адрес.

```powershell
[uri]::EscapeDataString($url)
```

URL-адрес в примере имеет следующее значение при кодировании URL-адреса.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Каждая ссылка начинается с того же базового URL-адреса:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Добавьте ссылку на шаблон в кодировке URL-адреса в конец базового URL-адреса.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

У вас есть полный URL-адрес ссылки.

## <a name="create-deploy-to-azure-button"></a>Кнопка "создать развертывание в Azure"

Наконец, разместите ссылку и изображение вместе.

Чтобы добавить кнопку с Markdown в файл README.md в репозитории GitHub или на веб-странице, используйте:

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

Чтобы протестировать полное решение, нажмите следующую кнопку:

[![Развертывание в Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

На портале отображается панель, с помощью которой можно легко указать значения параметров. Параметры предварительно заполняются значениями по умолчанию из шаблона.

![Развертывание с помощью портала](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о шаблонах см. [в разделе Общие сведения о структуре и синтаксисе шаблонов Azure Resource Manager](template-syntax.md).
