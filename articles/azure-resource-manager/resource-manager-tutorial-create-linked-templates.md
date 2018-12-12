---
title: Создание связанных шаблонов Azure Resource Manager | Документация Майкрософт
description: Сведения о создании связанных шаблонов Azure Resource Manager для создания виртуальной машины
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: dfdad89d628fda476ecef1c43246ce3927927555
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863505"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Руководство. Создание связанных шаблонов Azure Resource Manager

Узнайте, как создать связанные шаблоны Azure Resource Manager. Используя связанные шаблоны, можно одним шаблоном вызвать другой. Это отлично подходит для создания модулей шаблонов. В статье [Руководство. Создание нескольких экземпляров ресурса с помощью шаблонов Resource Manager](./resource-manager-tutorial-create-multiple-instances.md) используется тот же шаблон, что и здесь, который создает виртуальную машину, виртуальную сеть и другие зависимые ресурсы, в том числе учетную запись хранения. В связанном шаблоне можно отделить ресурс учетной записи хранения.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * открытие шаблона быстрого запуска;
> * Создание связанного шаблона
> * Передача связанного шаблона
> * Ссылка на связанный шаблон
> * Настройка зависимостей
> * Развертывание шаблона

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* [Visual Studio Code](https://code.visualstudio.com/) с [расширением Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. Ниже приведен пример создания пароля.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Для защиты криптографических ключей и других секретов используйте Azure Key Vault. Дополнительные сведения см. в статье [Руководство. Интеграция Azure Key Vault в развертывание шаблона Resource Manager](./resource-manager-tutorial-use-key-vault.md). Мы также рекомендуем обновлять пароль каждые три месяца.

## <a name="open-a-quickstart-template"></a>Открытие шаблона быстрого запуска

Шаблоны быстрого запуска Azure являются репозиторием для шаблонов Resource Manager. Вместо создания шаблона с нуля можно найти пример шаблона и настроить его. Шаблон, используемый в этом руководстве, называется [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Развертывание простой виртуальной машины Windows). Тот же шаблон используется в статье [Руководство. Создание нескольких экземпляров ресурса с помощью шаблонов Resource Manager](./resource-manager-tutorial-create-multiple-instances.md). Можно сохранить две копии одного шаблона для использования как:

* **основной шаблон** — создает все ресурсы, за исключением учетной записи хранения;
* **связанный шаблон** — создает учетную запись хранения.

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
6. Для создания другой копии файла с именем **linkedTemplate.json** выберите **Файл**>**Сохранить как**.

## <a name="create-the-linked-template"></a>Создание связанного шаблона

Связанный шаблон создает учетную запись хранения. Этот шаблон почти идентичен изолированному шаблону, который создает учетную запись хранения. В этом руководстве связанный шаблон должен передать значение основного шаблона. Это значение определяется в элементе `outputs`.

1. В Visual Studio Code откройте файл azuredeploy.parameters.json, если он еще не открыт.
2. Выполните следующие изменения:

    * Удалите все ресурсы, за исключением учетной записи хранения. Удаляются всего четыре ресурса.
    * Обновите элемент **выходных данных**, чтобы он выглядел так, как изображено ниже.

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        Для определения ресурсов виртуальной машины в основном шаблоне необходим **storageUri**.  В качестве значения выходных данных передайте значения обратно в основной шаблон.
    * Удалите параметры, которые никогда не используются. Эти параметры обозначены зеленой волнообразной линией. Должен остаться только один параметр с именем **расположение**.
    * Удалите элементы **переменных**. В этом руководстве они не нужны.
    * Добавьте параметр с именем **storageAccountName**. Как параметр имя учетной записи хранения передается из основного в связанный шаблон.

    Когда все готово, шаблон должен выглядеть, как показано ниже.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. Сохраните изменения.

## <a name="upload-the-linked-template"></a>Передача связанного шаблона

Шаблоны должны быть доступны из любой точки выполнения развертывания. Этим расположением может быть учетная запись хранения Azure, Github и Dropbox. Если ваши шаблоны содержат конфиденциальную информацию, убедитесь, что доступ к ним защищен. В [Руководстве. Создание нескольких экземпляров ресурса с помощью шаблонов Resource Manager](./resource-manager-tutorial-create-multiple-instances.md) используется такой же метод развертывания Cloud Shell, как и здесь. Основной шаблон (azuredeploy.json) загружается в оболочку. Где-то можно поделиться связанным шаблоном (linkedTemplate.json).  Чтобы сократить задачи этого руководства, связанный шаблон, определенный в предыдущем разделе, был загружен в [учетную запись хранения Azure](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json).

## <a name="call-the-linked-template"></a>Вызов связанного шаблона

Основной шаблон находится в файле с именем azuredeploy.json.

1. Откройте файл azuredeploy.json в Visual Studio Code, если он еще не открыт.
2. Удалите определения ресурсов учетной записи хранения из шаблона.
3. К тому месту, где необходимо определение учетной записи хранения, добавьте приведенный ниже фрагмент кода JSON.

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Обратите внимание на следующие детали.

    * В основном шаблоне используется ресурс `Microsoft.Resources/deployments` для ссылки на другой шаблон.
    * Ресурс `deployments` имеет имя `linkedTemplate`. Это имя используется для [настройки зависимостей](#configure-dependency).  
    * Для обоих связанного и вложенного шаблонов можно использовать только [добавочный](./deployment-modes.md) режим развертывания.
    * `templateLink/uri` содержит URI связанного шаблона. Связанный шаблон был загружен в учетную запись общего хранилища. Можно обновить URI, если отправить шаблон в другое расположение в Интернете.
    * Чтобы передать значение из основного шаблона в связанный, используйте `parameters`.
4. Сохраните изменения.

## <a name="configure-dependency"></a>Настройка зависимостей

Как следует из [руководства о создании нескольких экземпляров ресурса с помощью шаблона Resource Manager](./resource-manager-tutorial-create-multiple-instances.md), ресурс виртуальной машины зависит от учетной записи хранения.

![Схема зависимости шаблонов Azure Resource Manager в Visual Studio Code](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Так как учетная запись хранения теперь определяется в связанном шаблоне, необходимо обновить следующие два элемента ресурса `Microsoft.Compute/virtualMachines`.

* Перенастройте элемент `dependOn`. Определение учетной записи хранения перемещается на связанный шаблон.
* Перенастройте элемент `properties/diagnosticsProfile/bootDiagnostics/storageUri`. В разделе [Создать связанный шаблон](#create-the-linked-template) вы добавили значение выходных данных.

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Основной шаблон требует это значение.

1. Откройте файл azuredeploy.json в Visual Studio Code, если он еще не открыт.
2. Разверните определение ресурса виртуальной машины, обновив **dependsOn**, как показано на следующем снимке экрана.

    ![Связанные шаблоны Azure Resource Manager настраивают зависимости ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* — имя ресурса развертываний.  
3. Обновите **properties/diagnosticsProfile/bootDiagnostics/storageUri**, как показано на предыдущем снимке экрана.

Дополнительные сведения см. в разделе [Использование связанных шаблонов в при развертывании ресурсов Azure](./resource-group-linked-templates.md).

## <a name="deploy-the-template"></a>Развертывание шаблона

Дополнительные сведения о процедуре развертывания шаблона см. в [этом разделе](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Для повышения уровня безопасности используйте пароль, созданный для учетной записи администратора виртуальной машины. См. раздел [Предварительные требования](#prerequisites).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.  В группе ресурсов должно появится шесть ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как разработать и развернуть связанный шаблон. Чтобы узнать, как использовать расширения виртуальной машины для задач, выполняемых после развертывания, ознакомьтесь с дополнительными сведениями о

> [!div class="nextstepaction"]
> [развертывании расширений виртуальной машины](./deployment-manager-tutorial.md)
