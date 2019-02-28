---
title: Создание шаблона Azure Resource Manager c помощью Visual Studio Code | Документация Майкрософт
description: Используйте Visual Studio Code и расширение средств Azure Resource Manager для работы с шаблонами Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 33bc10bb601fa14a34b6032c54b0c751a3608ccc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823660"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Краткое руководство. Создание шаблона Azure Resource Manager c помощью Visual Studio Code

См. дополнительные сведения об использовании Visual Studio Code и расширения средств Azure Resource Manager для создания и изменения шаблонов Azure Resource Manager. Шаблоны Resource Manager в Visual Studio Code можно создавать и без расширения. Но расширение предоставляет варианты автозаполнения, которые упрощают разработку шаблона. Основные понятия, связанные с развертыванием и управлением решений Azure, см. в [обзоре Azure Resource Manager](resource-group-overview.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

- [Visual Studio Code](https://code.visualstudio.com/).
- Расширение средств Resource Manager. Чтобы их установить, выполните следующие действия.

    1. Откройте Visual Studio Code.
    2. Чтобы открыть панель расширений,нажмите клавиши **CTRL+SHIFT+X**
    3. Выполните поиск по фразе **Средства Azure Resource Manager**, а затем нажмите кнопку **Установить**.
    4. Чтобы завершить установку расширения, щелкните **Перезагрузить**.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Чтобы не создавать шаблон с нуля, откройте его в [Шаблонах быстрого запуска Azure](https://azure.microsoft.com/resources/templates/). Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager.

Шаблон, используемый в этом кратком руководстве, называется [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Создание стандартной учетной записи хранения). Шаблон определяет ресурс учетной записи службы хранилища Azure.

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл в качестве **azuredeploy.json** на локальном компьютере.

## <a name="edit-the-template"></a>Изменение шаблона

Чтобы узнать, как изменить шаблон с помощью Visual Studio Code, в разделе `outputs` необходимо добавить еще один элемент для отображения URI хранилища.

1. Добавьте один или несколько элементов выходных данных в экспортированный шаблон:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    После завершения раздел выходных данных будет выглядеть следующим образом.

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Если вы скопировали код и вставили его в Visual Studio Code, попробуйте повторно ввести элемент **значение**, чтобы проверить функцию IntelliSense расширения Resource Manager Tools.

    ![IntelliSense для шаблона Resource Manager в Visual Studio Code](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. Чтобы сохранить файл выберите **Файл**>**Сохранить**.

## <a name="deploy-the-template"></a>Развертывание шаблона

Существует множество методов по развертыванию шаблонов.  В этом кратком руководстве используется Azure Cloud Shell. Cloud Shell — это веб-приложение, которое не нужно настраивать. Оно поддерживает как Azure CLI, так и Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


1. Войдите в [Azure Cloud Shell](https://shell.azure.com).

    ![Cloud Shell CLI на портале Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)
2. В левом верхнем углу выберите используемую среду — **PowerShell** или **Bash**. Чтобы использовать CLI, необходимо открыть сеанс Bash. Чтобы запустить Azure PowerShell, нужно открыть сеанс PowerShell. Выберите стрелку вниз, чтобы переключаться между Bash и PowerShell. См. предыдущий снимок экрана. После переключения желательно перезагрузить оболочку.
3. Выберите **Отправка и скачивание файлов**, а затем **Отправить**.

    # <a name="clitabcli"></a>[ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](#tab/CLI)

    ![Файл отправки Cloud Shell портале Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Файл отправки Cloud Shell портале Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file-powershell.png)
    
    ---

    Выберите файл, сохраненный ранее. **azuredeploy.json** — имя по умолчанию. Файл шаблона должен быть доступным из облака.

    При необходимости можно использовать команды **ls** и **cat**, чтобы проверить отправку файла. 

    # <a name="clitabcli"></a>[ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](#tab/CLI)

    ![Файл списка оболочки Cloud портала Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Файл списка оболочки Cloud портала Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file-powershell.png)
    
    ---
4. Выполните следующие команды в Cloud Shell. Выберите вкладку, чтобы отобразить код PowerShell или код CLI.

    # <a name="clitabcli"></a>[ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    az group create --name $resourceGroupName --location "$location" &&
    az group deployment create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json"
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    
    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json"
    ```
    
    ---

    Обновите имя файла шаблона, если вы сохраняете файл с именем, отличающимся от **azuredeploy.json**. 

    На следующем снимке экрана показан пример развертывания.

    # <a name="clitabcli"></a>[ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](#tab/CLI)

    ![Шаблон развертывания оболочки Cloud портала Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ![Шаблон развертывания оболочки Cloud портала Azure](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template-powershell.png)
    
    ---

    Имя учетной записи хранения и URL-адрес хранилища в разделе выходных данных выделены на снимке экрана ниже. Чтобы продолжить, необходимо ввести имя учетной записи хранения.

5. Чтобы перечислить созданные учетные записи хранения, выполните следующую команду PowerShell или CLI:

    # <a name="clitabcli"></a>[ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](#tab/CLI)
    ```azurecli
    echo "Enter the Resource Group name:" &&
    read resourceGroupName &&
    echo "Enter the Storage Account name:" &&
    read storageAccountName &&
    az storage account show --resource-group $resourceGroupName --name $storageAccountName
    ```
   
    # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
    
    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $storageAccountName = Read-Host -Prompt "Enter the Storage Account name"
    Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    ```
    
    ---

Дополнительные сведения об использовании учетных записях хранения Azure см. в статье [Краткое руководство. Передача, скачивание и составление списка больших двоичных объектов с помощью портала Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

Основное внимание в этом руководстве уделяется использованию Visual Studio Code для редактирования существующего шаблона Быстрого начала Azure. Вы также узнали, как развернуть шаблон, используя CLI или PowerShell из Azure Cloud Shell. Шаблоны быстрого запуска Azure могут не обеспечить вас всем необходимым. В следующем руководстве показано, как в справочнике по шаблону найти информацию, чтобы создать зашифрованную учетную запись хранения Azure.

> [!div class="nextstepaction"]
> [Создание шаблона Azure Resource Manager для развертывания зашифрованной учетной записи хранения](./resource-manager-tutorial-create-encrypted-storage-accounts.md)
