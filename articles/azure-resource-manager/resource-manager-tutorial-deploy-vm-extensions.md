---
title: Развертывание расширений виртуальной машины с помощью шаблонов Azure Resource Manager | Документация Майкрософт
description: Узнайте, как можно развернуть расширения виртуальной машины с помощью шаблонов Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca07f044a423a4ea1b5dee484c56f457c7d785de
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239737"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Руководство. Развертывание расширений виртуальной машины с помощью шаблонов Azure Resource Manager

Узнайте, как использовать [расширения виртуальных машин Azure](../virtual-machines/extensions/features-windows.md), чтобы выполнять задачи настройки и автоматизации после развертывания виртуальных машин Azure. Существует множество разных расширений виртуальных машин, которые можно использовать с виртуальными машинами Azure. При работе с этим руководством вы развернете расширение настраиваемого скрипта из шаблона Resource Manager для выполнения скрипта PowerShell на виртуальной машине Windows.  Скрипт устанавливает веб-сервер на виртуальной машине.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * подготовка скрипта PowerShell;
> * открытие шаблона быстрого запуска;
> * Изменение шаблона
> * Развертывание шаблона
> * Проверка развертывания

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с расширением средств Resource Manager.  См. в разделе [Краткое руководство. Создание шаблона Azure Resource Manager c помощью Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. Ниже приведен пример создания пароля.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Руководство. Интеграция Azure Key Vault в развертывание шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md). Мы также рекомендуем обновлять пароль каждые три месяца.

## <a name="prepare-a-powershell-script"></a>Подготовка скрипта PowerShell

Скрипт PowerShell со следующим содержимым совместно используется [из учетной записи хранения Azure с открытым доступом](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Если вы решили опубликовать файл в своем расположении, необходимо позже обновить элемент [fileUri] в шаблоне в этом руководстве.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Шаблоном определено пять ресурсов:

    * `Microsoft.Storage/storageAccounts`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Прежде чем настраивать шаблон, рекомендуется получить основные сведения о нем.
5. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.

## <a name="edit-the-template"></a>Изменение шаблона

Добавьте ресурс расширения виртуальной машины в существующий шаблон со следующим содержимым:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Дополнительные сведения об определении этого ресурса см. в [справочнике по расширению](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Ниже приведены некоторые важные элементы.

* **name**. Так как ресурс расширения является дочерним ресурсом объекта виртуальной машины, у имени должен быть префикс имени виртуальной машины. См. сведения в разделе [Дочерние ресурсы](./resource-manager-templates-resources.md#child-resources).
* **dependsOn**. Ресурс расширения должен быть создан после создания виртуальной машины.
* **fileUris**. Это расположение, в котором хранятся файлы скриптов. Если вы решили не использовать указанное расположение, необходимо обновить значения.
* **commandToExecute**. Это команда для вызова скрипта.  

## <a name="deploy-the-template"></a>Развертывание шаблона

Дополнительные сведения о процедуре развертывания шаблона см. в [этом разделе](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template). Рекомендуется использовать пароль, созданный для учетной записи администратора виртуальной машины. См. раздел [Предварительные требования](#prerequisites).

## <a name="verify-the-deployment"></a>Проверка развертывания

Выберите на портале виртуальную машину. В окне обзора скопируйте IP-адрес с помощью находящейся справа от него ссылки (**Щелкните, чтобы скопировать**), а затем вставьте его на вкладку браузера. Откроется страница приветствия IIS по умолчанию, которая будет выглядеть следующим образом:

![Azure Resource Manager развертывает расширения виртуальной машины для клиентского скрипта веб-сервера IIS](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы развернули виртуальную машину и расширение виртуальной машины. Расширение установило веб-сервер IIS на виртуальной машине. Чтобы узнать, как использовать расширение Базы данных SQL Azure для импорта BACPAC-файла, перейдите к следующему руководству:

> [!div class="nextstepaction"]
> [](./resource-manager-tutorial-deploy-vm-extensions.md)
