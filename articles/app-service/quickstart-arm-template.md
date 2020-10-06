---
title: Создание приложения Службы приложений с помощью шаблона Azure Resource Manager
description: Создайте свое первое приложение в Службе приложений Azure за считаные секунды с помощью шаблона Azure Resource Manager, который является одним из множества способов развертывания в Службе приложений.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 05/25/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e577616e0976ca050a55c8524e68129545ed1912
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89653300"
---
# <a name="create-app-service-app-using-an-azure-resource-manager-template"></a>Создание приложения Службы приложений с помощью шаблона Azure Resource Manager

Начните работу со [Службой приложений Azure](overview.md), развернув приложение в облаке с помощью шаблона Azure Resource Manager и [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) в Cloud Shell. Так как вы используете бесплатный уровень Службы приложений, для прохождения этого краткого руководства никакие затраты не требуются.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-azure-app-service-app"></a>Создание приложения Службы приложений Azure

### <a name="review-the-template"></a>Изучение шаблона

::: zone pivot="platform-windows"
Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/). Он развертывает план и приложение Службы приложений в Windows. Шаблон совместим с .NET Core, .NET Framework, PHP, Node.js и статическими HTML-приложениями. Дополнительные сведения о создании приложения Java см. в [этой статье](app-service-web-get-started-java.md). 

[!code-json[<Azure Resource Manager template App Service Windows app>](~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json)]

В шаблоне определено два ресурса Azure:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): создание плана Службы приложений.
* [**Microsoft.Web/Sites**](/azure/templates/microsoft.web/sites): создание приложения Службы приложений.

Этот шаблон содержит несколько параметров, предопределенных для удобства. В таблице ниже приведены параметры по умолчанию и их описание.

| Параметры | Тип    | Значение по умолчанию                | Описание |
|------------|---------|------------------------------|-------------|
| webAppName | строка  | webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** | Имя приложения. |
| location   | строка  | [[resourceGroup().location]](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup) | Регион приложения |
| sku        | строка  | F1                         | Размер экземпляра (F1 = уровень "Бесплатный") |
| Язык   | строка  | .NET                       | Стек языков программирования (.NET, PHP, Node или HTML) |
| helloWorld | Логическое | False                        | True = развертывание приложения Hello World |
| repoUrl    | строка  |                             | Внешний репозиторий Git (необязательно) |
::: zone-end
::: zone pivot="platform-linux"
Шаблон, используемый в этом кратком руководстве, взят из [шаблонов быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates/). Он развертывает план и приложение Службы приложений в Linux. Он совместим со всеми поддерживаемыми языками программирования в Службе приложений.

[!code-json[<Azure Resource Manager template App Service Linux app>](~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json)]

В шаблоне определено два ресурса Azure:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): создание плана Службы приложений.
* [**Microsoft.Web/Sites**](/azure/templates/microsoft.web/sites): создание приложения Службы приложений.

Этот шаблон содержит несколько параметров, предопределенных для удобства. В таблице ниже приведены параметры по умолчанию и их описание.

| Параметры | Тип    | Значение по умолчанию                | Описание |
|------------|---------|------------------------------|-------------|
| webAppName | строка  | webApp- **[`<uniqueString>`](/azure/azure-resource-manager/templates/template-functions-string#uniquestring)** | Имя приложения. |
| location   | строка  | [[resourceGroup().location]](/azure/azure-resource-manager/templates/template-functions-resource#resourcegroup) | Регион приложения |
| sku        | строка  | F1                         | Размер экземпляра (F1 = уровень "Бесплатный") |
| linuxFxVersion   | строка  | DOTNETCORE&#124;3.0        | Версия стека языка программирования &#124; |
| repoUrl    | строка  |                             | Внешний репозиторий Git (необязательно) |

---
::: zone-end


### <a name="deploy-the-template"></a>Развертывание шаблона

Для развертывания шаблона здесь используется Azure CLI. Вы можете также использовать Azure PowerShell, портал Azure и REST API. Дополнительные сведения о других методах развертывания см. в статье о [развертывании с использованием шаблонов](../azure-resource-manager/templates/deploy-powershell.md). 

При помощи этого кода создается группа ресурсов, план службы приложений и веб-приложение. Группа ресурсов по умолчанию, план службы приложений и расположение настроены автоматически. Замените `<app-name>` глобальным уникальным именем приложения (допустимые символы: `a-z`, `0-9` и `-`).

::: zone pivot="platform-windows"
Выполните приведенный ниже код, чтобы развернуть приложение .NET Framework в Windows.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" sample="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux. 

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Чтобы развернуть другой стек языка, обновите соответствующие значения `linuxFxVersion`. Примеры приведены ниже. Чтобы отобразить сведения о поддерживаемой версии, выполните следующую команду в Cloud Shell: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Язык    | Например, .                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> Дополнительные примеры шаблонов Службы приложений Azure можно найти [здесь](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).


## <a name="validate-the-deployment"></a>Проверка развертывания

Перейдите к `http://<app_name>.azurewebsites.net/` и убедитесь, что оно создано.

## <a name="clean-up-resources"></a>Очистка ресурсов

[Удалите группу ресурсов](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), если она больше не нужна.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
>Развертывание из локального репозитория Git

> [!div class="nextstepaction"]
>Использование ASP.NET Core с Базой данных SQL

> [!div class="nextstepaction"]
>Приложение Python с PostgreSQL

> [!div class="nextstepaction"]
> [Использование PHP и MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Подключение к базе данных SQL Azure на Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
>Сопоставление личного домена