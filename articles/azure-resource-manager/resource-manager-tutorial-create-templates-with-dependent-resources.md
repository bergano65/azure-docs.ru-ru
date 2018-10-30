---
title: Создание шаблонов Azure Resource Manager с зависимыми ресурсами | Документация Майкрософт
description: Узнайте, как создавать шаблон Azure Resource Manager с несколькими ресурсами, а также, как развертывать его, используя портал Azure
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/19/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5e198310dd18cc8574b5510b9318ff4badaffca3
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646316"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Руководство. Создание шаблонов Azure Resource Manager с зависимыми ресурсами

Узнайте, как создать шаблон Azure Resource Manager для развертывания нескольких ресурсов.  После создания шаблона можно развернуть шаблон с помощью Cloud Shell с портала Azure.

В рамках этого руководства вы создадите учетную запись хранения, виртуальную машину, виртуальную сеть и другие зависимые ресурсы. Некоторые ресурсы невозможно развернуть до тех пор, пока не будет существовать другой ресурс. Например, невозможно создать виртуальную машину, пока не существуют ее учетная запись хранения и сетевой интерфейс. Эта связь определяется путем пометки зависимости одного ресурса от других. Диспетчер ресурсов оценивает зависимости между ресурсами и развертывает эти ресурсы согласно установленным зависимостям. Если ресурсы не зависят друг от друга, диспетчер ресурсов развертывает их параллельно. Дополнительные сведения см. в статье [Define the order for deploying resources in Azure Resource Manager Templates](./resource-group-define-dependencies.md) (Определение порядка развертывания ресурсов в шаблонах Azure Resource Manager).

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Настройка безопасной среды
> * Открытие шаблона быстрого запуска
> * Обзор шаблона
> * Изменение файла параметров
> * Развертывание шаблона

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с расширением средств Resource Manager.  Как установить расширение см. в разделе [Quickstart: create Azure Resource Manager templates by using Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) (Создание шаблонов Azure Resource Manager с помощью Visual Studio Code)
* Для предотвращения атак путем распыления пароля создайте пароль для учетной записи администратора виртуальной машины. Ниже приведен пример:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Руководство. Интеграция Azure Key Vault в развертывание шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md). Мы также рекомендуем обновлять пароль каждые три месяца.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows).

1. В Visual Studio Code выберите **Файл**>**Открыть файл**.
2. Скопируйте приведенный ниже URL-адрес и вставьте его в поле **Имя файла**.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Чтобы открыть файл, выберите **Открыть**.
4. Выберите **Файл**>**Сохранить как**, чтобы сохранить файл на локальный компьютер с именем **azuredeploy.json**.

## <a name="explore-the-template"></a>Обзор шаблона

Рассматривая шаблон в этом разделе, постарайтесь ответить на следующие вопросы:

- Сколько ресурсов Azure определено в этом шаблоне?
- Одним из ресурсов является учетная запись хранения Azure.  Похоже ли это определение на использованное в предыдущем руководстве?
- Можете ли вы найти справочники по шаблонам для ресурсов, определенных в этом шаблоне?
- Можете ли вы найти зависимости ресурсов?

1. В Visual Studio Code сверните элементы, пока не появятся элементы только первого уровня и элементы второго уровня внутри **ресурсов**.

    ![Шаблоны Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Шаблоном определено пять ресурсов.
2. Разверните первый ресурс. Это учетная запись хранения. Определение должно соответствовать определению, используемому в начале предыдущего руководства.

    ![Определение учетной записи хранения для шаблонов Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Разверните второй ресурс. Тип ресурса — **Microsoft.Network/publicIPAddresses**. Чтобы найти справочник по шаблону, перейдите по [этой ссылке](https://docs.microsoft.com/azure/templates/) и в поле **Фильтровать по названию** введите **общедоступный IP-адрес** или **общедоступные IP-адреса**. Сравните определение ресурса с определением в справочнике по шаблону.

    ![Определение общедоступного IP-адреса для шаблонов Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Повторите последний шаг, чтобы найти справочники по шаблонам для других ресурсов, определенных в этом шаблоне.  Сравните определения ресурсов со справочными экземплярами.
5. Разверните четвертый ресурс:

    ![Шаблоны Azure Resource Manager в Visual Studio Code для элемента dependsOn](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Элемент dependsOn позволяет определить один ресурс как зависимый от одного или нескольких ресурсов. В этом примере ресурс является networkInterface.  Он зависит от двух других ресурсов:

    * publicIpAddress;
    * virtualNetwork.

6. Разверните пятый ресурс. Этот ресурс — это виртуальная машина. Он зависит от двух других ресурсов:

    * storageAccount
    * networkInterface.

На следующей схеме приведены ресурсы и информация о зависимости для этого шаблона.

![Схема зависимости шаблонов Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Путем указания зависимостей диспетчер ресурсов позволяет эффективно развертывать решение. Развертывание учетной записи хранения, общедоступного IP-адреса и виртуальной сети происходит в параллельном режиме, так как они не имеют зависимостей. После развертывания общедоступного IP-адреса и виртуальной сети создается сетевой интерфейс. Когда все ресурсы развернуты, диспетчер ресурсов развертывает виртуальную машину.

## <a name="deploy-the-template"></a>Развертывание шаблона

Существует множество методов по развертыванию шаблонов.  В этом руководстве используется Cloud Shell на портале Azure.

1. Войдите в [Cloud Shell](https://shell.azure.com). 
2. Выберите **PowerShell** в верхнем левом углу Cloud Shell и нажмите **Confirm** (Подтвердить).  В этом руководстве используется PowerShell.
3. Выберите **Отправить файл** из Cloud Shell.

    ![Файл отправки Cloud Shell портале Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
4. Выберите шаблон, сохраненный ранее при выполнении этого руководства. **azuredeploy.json** — имя по умолчанию.  Если файл с тем же именем уже существует, он будет перезаписан без уведомления.
5. Чтобы убедиться, что файл загружен успешно, выполните в командной строке Cloud Shell следующую команду. 

    ```bash
    ls
    ```

    ![Файл списка оболочки Cloud портала Azure](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Имя файла, показанное на снимке экрана, — azuredeploy.json.

6. Чтобы проверить содержимое JSON-файла, выполните в командной строке Cloud Shell следующую команду.

    ```bash
    cat azuredeploy.json
    ```
7. Выполните следующие команды PowerShell в командной строке Cloud Shell. Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. См. раздел [Предварительные требования](#prerequisites).

    ```azurepowershell
    $deploymentName = Read-Host -Prompt "Enter the name for this deployment"
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password"
    $dnsLablePrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmResourceGroupDeployment -Name $deploymentName `
        -ResourceGroupName $resourceGroupName `
        -adminUsername = $adminUsername `
        -adminPassword = $adminPassword `
        -dnsLabelPrefix = $dnsLabelPrefix `
        -TemplateFile azuredeploy.json 
    ```
8. Для отображения вновь созданной виртуальной машины выполните следующую команду PowerShell.

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    ```

    Имя виртуальной машины внутри шаблона жестко закодировано как **SimpleWinVM**.

9. Войдите в виртуальную машину для проверки учетных данных администратора. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве был разработан и развернут шаблон для создания виртуальной машины, виртуальной сети и зависимых ресурсов. Сведения о том, как развернуть ресурсы Azure на основе условий, см. по ссылке ниже.


> [!div class="nextstepaction"]
> [Использование условий](./resource-manager-tutorial-use-conditions.md)

