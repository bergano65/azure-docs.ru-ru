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
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5657ebb2a5b29e4ec5360480c1fef6cb92dad9c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60388546"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Руководство по Развертывание расширений виртуальной машины с помощью шаблонов Azure Resource Manager

Узнайте, как использовать [расширения виртуальных машин Azure](../virtual-machines/extensions/features-windows.md), чтобы выполнять задачи настройки и автоматизации после развертывания виртуальных машин Azure. Существует множество разных расширений виртуальных машин, которые можно использовать с виртуальными машинами Azure. При работе с этим руководством вы развернете расширение настраиваемого скрипта из шаблона Azure Resource Manager для выполнения скрипта PowerShell на виртуальной машине Windows.  Скрипт устанавливает веб-сервер на виртуальной машине.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Подготовка скрипта PowerShell
> * Открытие шаблона быстрого запуска
> * Изменение шаблона
> * Развертывание шаблона
> * Проверка развертывания

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с расширением средств Resource Manager. Дополнительные сведения см. в разделе [об установке расширения](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. Ниже приведен пример создания пароля.

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Руководство. Интеграция с Azure Key Vault при развертывании шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md). Мы также советуем обновлять пароль каждые три месяца.

## <a name="prepare-a-powershell-script"></a>Подготовка скрипта PowerShell

Скрипт PowerShell со следующим содержимым совместно используется [из учетной записи хранения Azure с открытым доступом](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Если вы решили опубликовать файл в своем расположении, необходимо позже обновить элемент `fileUri` в шаблоне в этом руководстве.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure — это репозиторий для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

1. В Visual Studio Code выберите **Файл** > **Открыть файл**.
1. В поле **Имя файла** вставьте следующий URL-адрес: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json.

1. Чтобы открыть файл, выберите **Открыть**.  
    Шаблон определяет пять ресурсов:

   * **Microsoft.Storage/storageAccounts**. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft.Network/publicIPAddresses**. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Ознакомьтесь со статьей о [справочнике по шаблонам](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Прежде чем настраивать шаблон, рекомендуется получить основные сведения о нем.

1. Сохраните файл под именем *azuredeploy.json* на локальный компьютер, выбрав **Файл** > **Сохранить как**.

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

* **name**. Так как ресурс расширения является дочерним ресурсом объекта виртуальной машины, у имени должен быть префикс имени виртуальной машины. См. сведения в разделе [Дочерние ресурсы](./resource-group-authoring-templates.md#child-resources).
* **dependsOn**. Создайте ресурс расширения после создания виртуальной машины.
* **fileUris**. Это расположение, в котором хранятся файлы скриптов. Если вы решили не использовать указанное расположение, необходимо обновить значения.
* **commandToExecute**. Эта команда запускает скрипт.  

## <a name="deploy-the-template"></a>Развертывание шаблона

Дополнительные сведения о процедуре развертывания см. в разделе "Развертывание шаблона" статьи [Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Мы советуем использовать пароль, созданный для учетной записи администратора виртуальной машины. Дополнительные сведения см. в разделе [Предварительные требования](#prerequisites) этой статьи.

## <a name="verify-the-deployment"></a>Проверка развертывания

1. На портале Azure выберите виртуальную машину.
1. В обзоре виртуальной машины скопируйте IP-адрес, выбрав **Щелкните, чтобы скопировать**, а затем вставьте его на вкладке браузера.  
   Откроется страница приветствия IIS по умолчанию, которая будет выглядеть следующим образом:

![Страница приветствия служб IIS](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если развернутые ресурсы Azure больше не нужны, вы можете их очистить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure на панели слева выберите **Группа ресурсов**.
2. В поле **Фильтровать по имени** введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  
    В группе ресурсов будет отображаться шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы развернули виртуальную машину и расширение виртуальной машины. Расширение установило веб-сервер IIS на виртуальной машине. Чтобы узнать, как использовать расширение Базы данных SQL Azure для импорта BACPAC-файла, перейдите к следующему руководству:

> [!div class="nextstepaction"]
> [Развертывание расширений SQL](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)
