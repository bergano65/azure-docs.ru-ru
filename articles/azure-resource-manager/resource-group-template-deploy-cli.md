---
title: Развертывание ресурсов с помощью Azure CLI и шаблона
description: Используйте Azure Resource Manager и Azure CLI для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: c31a139e40953bf3b652af2172cd5c51578c45cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149743"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI

В этой статье объясняется, как использовать Azure CLI и шаблоны Resource Manager для развертывания ресурсов в Azure. Если вы не знакомы с основными понятиями, связанными с развертыванием решений Azure и управлением ими, см. [обзор Azure Resource Manager](resource-group-overview.md).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Если у вас не установлен интерфейс командной строки Azure, можете использовать [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Область развертывания

Вы можете ориентироваться на развертывание либо в подписке Azure, либо в группу ресурсов в рамках подписки. В большинстве случаев вы планируете развертывание в группе ресурсов. Используйте развертывания подписок для применения политик и назначений ролей в рамках подписки. Вы также можете использовать развертывания подписок для создания группы ресурсов и развертывания в ней ресурсов. В зависимости от области развертывания используются разные команды.

Для развертывания в **группе ресурсов**используйте команду [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Для развертывания в **подписке**используйте команду [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Дополнительные сведения о развертываниях уровня подписки см. в статье [Создание групп ресурсов и ресурсов на уровне подписки](deploy-to-subscription.md).

В настоящее время развертывания группы управления поддерживаются только в REST API. Дополнительные сведения о развертывании на уровне группы управления см. [в разделе Создание ресурсов на уровне группы управления](deploy-to-management-group.md).

В примерах этой статьи используются развертывания групп ресурсов.

## <a name="deploy-local-template"></a>Развертывание локального шаблона

При развертывании ресурсов в Azure выполните следующие действия:

1. Вход в учетную запись Azure
2. Создайте группу ресурсов, которая послужит в качестве контейнера для развертываемых ресурсов. Имя группы ресурсов может содержать только буквы, цифры, точки, знаки подчеркивания, дефисы и скобки. Оно может содержать до 90 знаков и не должно заканчиваться точкой.
3. Разверните в группе ресурсов шаблон, определяющий ресурсы, которые необходимо создать.

Шаблон может включать параметры, которые позволяют настроить развертывание. Например, вы можете предоставить значения, предназначенные для конкретной среды (такой как среда разработки, тестирования и рабочая среда). Пример шаблона определяет параметр для номера SKU учетной записи хранения.

В следующем примере создается группа ресурсов и развертывается шаблон с локального компьютера:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Развертывание может занять несколько минут. По завершении появится сообщение, содержащее результат:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Развертывание удаленного шаблона

Шаблоны Resource Manager можно хранить не на локальном компьютере, а на внешнем источнике. Вы можете хранить шаблоны в репозитории системы управления версиями (например, GitHub). А также их можно хранить в учетной записи хранения Azure для общего доступа в организации.

Для развертывания внешнего шаблона используйте параметр **template-uri**. Используйте универсальный код ресурса (URI) в примере для развертывания примера шаблона из GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

В предыдущем примере для шаблона требуется общедоступный код URI, который подходит для большинства сценариев, так как шаблон не должен содержать конфиденциальные данные. Если необходимо указать конфиденциальные данные (например, пароль администратора), то передайте это значение с помощью безопасного параметра. Но если вы не хотите, чтобы шаблон был общедоступным, можно защитить его, сохранив в закрытом контейнере хранилища. Сведения о развертывании шаблона, требующего маркер подписанного URL-адреса (SAS), см. в статье [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

В Cloud Shell введите следующие команды:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>parameters

Чтобы передать значения параметров, можно использовать встроенные параметры или файл параметров.

### <a name="inline-parameters"></a>Встроенные параметры

Чтобы передать встроенные параметры, укажите значения в `parameters`. Например, строки и массив передаются в шаблон из оболочки Bash следующим образом:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Если вы используете Azure CLI в командной строке Windows (CMD) или PowerShell, передайте массив в формате: `exampleArray="['value1','value2']"`.

Можно также получить содержимое файла и предоставлять его в виде встроенного параметра.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Получение значения параметра из файла полезно в тех случаях, когда есть необходимость предоставить значения конфигурации. Например, вы можете предоставить [значения cloud-init для виртуальной машины Linux](../virtual-machines/linux/using-cloud-init.md).

Файл ArrayContent.json имеет следующий формат:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Файлы параметров

Вместо передачи параметров в виде встроенных значений в сценарии вам может быть проще использовать JSON-файл, содержащий значения параметров. Файл параметров должен находиться в локальной среде. Внешние файлы параметров не поддерживаются в Azure CLI.

Дополнительные сведения о файле параметров см. в разделе [Создание файла параметров Диспетчер ресурсов](resource-manager-parameter-files.md).

Для передачи локального файла параметров используйте `@`, чтобы указать локальный файл с именем storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Обработку расширенного формата JSON

Чтобы развернуть шаблон с многострочными строками или комментариями, необходимо использовать параметр `--handle-extended-json-format`.  Например,

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Тестовое развертывание шаблона

Чтобы проверить шаблон и значения параметров без фактического развертывания ресурсов, используйте [az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate).

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Если ошибок не обнаружено, то команда возвращает сведения о тестовом развертывании. В частности, обратите внимание, что **error** имеет значение null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Если обнаруживается ошибка, то команда возвращает сообщение об ошибке. Например, попытка передать недопустимое значение для номера SKU учетной записи возвращает следующую ошибку:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Если шаблон содержит синтаксическую ошибку, команда возвращает сообщение об ошибке, указывающее, что ей не удалось проанализировать шаблон. В сообщении указывается номер строки и позиция ошибки синтаксического анализа.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Дополнительная информация

- Сведения о откате к успешному развертыванию при возникновении ошибки см. в разделе [откат при ошибке для успешного развертывания](rollback-on-error.md).
- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](resource-group-authoring-templates.md).
- Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](resource-manager-cli-sas-token.md).
- Для безопасного развертывания службы в нескольких регионах обратитесь к статье о [диспетчере развертывания Azure](deployment-manager-overview.md).
