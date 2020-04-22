---
title: Развертывание ресурсов с помощью Azure CLI и шаблона
description: Для развертывания ресурсов в Azure и Azure clI используйте менеджер ресурсов Azure и ClI Azure. Эти ресурсы определяются в шаблоне Resource Manager.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685703"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Развертывание ресурсов с шаблонами ARM и Azure CLI

В этой статье объясняется, как использовать Azure CLI с шаблонами Azure Resource Manager (ARM) для развертывания ресурсов в Azure. Если вы не знакомы с концепциями развертывания и управления решениями Azure, [см.](overview.md)

Команды развертывания изменены в версии Azure CLI 2.2.0. Примеры в этой статье требуют версии Azure CLI 2.2.0 или позже.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Если у вас не установлен интерфейс командной строки Azure, можете использовать [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Область развертывания

Развертывание можно нацелить на группу ресурсов, подписку, группу управления или арендатора. В большинстве случаев развертывание будет нацелено на группу ресурсов. Чтобы применить политики и назначения ролей в более широком диапазоне, используйте подписку, группу управления или развертывания клиентов. При развертывании в подписке можно создать группу ресурсов и развернуть ресурсы в ней.

В зависимости от сферы развертывания используются различные команды.

Для развертывания в **группе ресурсов**используйте [группу развертывания az, создайте:](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create)

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Для развертывания **в подписке**используйте [подгруппу развертывания az:](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create)

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Для получения дополнительной информации о развертывании уровня подписки [см.](deploy-to-subscription.md)

Для развертывания в **группе управления**используйте [az развертывание mg создать:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

Для получения дополнительной информации о развертываниях группы управления [см.](deploy-to-management-group.md)

Для развертывания **для клиента**используйте [создание клиента развертывания az:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

Для получения дополнительной информации о развертываниях уровня клиента [см.](deploy-to-tenant.md)

Примеры в этой статье используют развертывания групп ресурсов.

## <a name="deploy-local-template"></a>Развертывание локального шаблона

При развертывании ресурсов в Azure выполните следующие действия:

1. Вход в учетную запись Azure
2. Создайте группу ресурсов, которая послужит в качестве контейнера для развертываемых ресурсов. Имя группы ресурсов может содержать только буквы, цифры, точки, знаки подчеркивания, дефисы и скобки. Оно может содержать до 90 знаков и не должно заканчиваться точкой.
3. Разверните в группе ресурсов шаблон, определяющий ресурсы, которые необходимо создать.

Шаблон может включать параметры, которые позволяют настроить развертывание. Например, вы можете предоставить значения, предназначенные для конкретной среды (такой как среда разработки, тестирования и рабочая среда). Пример шаблона определяет параметр для номера SKU учетной записи хранения.

В следующем примере создается группа ресурсов и развертывается шаблон с локального компьютера:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Развертывание может занять несколько минут. По завершении появится сообщение, содержащее результат:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Развертывание удаленного шаблона

Вместо того, чтобы хранить шаблоны ARM на локальной машине, вы можете предпочесть хранить их во внешнем месте. Вы можете хранить шаблоны в репозитории системы управления версиями (например, GitHub). А также их можно хранить в учетной записи хранения Azure для общего доступа в организации.

Для развертывания внешнего шаблона используйте параметр **template-uri**. Используйте универсальный код ресурса (URI) в примере для развертывания примера шаблона из GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

В предыдущем примере для шаблона требуется общедоступный код URI, который подходит для большинства сценариев, так как шаблон не должен содержать конфиденциальные данные. Если необходимо указать конфиденциальные данные (например, пароль администратора), то передайте это значение с помощью безопасного параметра. Но если вы не хотите, чтобы шаблон был общедоступным, можно защитить его, сохранив в закрытом контейнере хранилища. Сведения о развертывании шаблона, требующего маркер подписанного URL-адреса (SAS), см. в статье [Развертывание частного шаблона Resource Manager с использованием токена SAS и Azure PowerShell](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

В Cloud Shell введите следующие команды:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Параметры

Чтобы передать значения параметров, можно использовать встроенные параметры или файл параметров.

### <a name="inline-parameters"></a>Встроенные параметры

Чтобы передать встроенные параметры, укажите значения в `parameters`. Например, строки и массив передаются в шаблон из оболочки Bash следующим образом:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Если вы используете Azure CLI с помощью Командного запроса Windows (CMD) или PowerShell, передайте массив в формате: `exampleArray="['value1','value2']"`

Можно также получить содержимое файла и предоставлять его в виде встроенного параметра.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Получение значения параметра из файла полезно в тех случаях, когда есть необходимость предоставить значения конфигурации. Например, вы можете предоставить [значения cloud-init для виртуальной машины Linux](../../virtual-machines/linux/using-cloud-init.md).

Файл ArrayContent.json имеет следующий формат:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Файлы параметров

Вместо передачи параметров в виде встроенных значений в сценарии вам может быть проще использовать JSON-файл, содержащий значения параметров. Файл параметров должен находиться в локальной среде. Внешние файлы параметров не поддерживаются в Azure CLI.

Для получения дополнительной информации о файле параметра [см.](parameter-files.md)

Для передачи локального файла параметров используйте `@`, чтобы указать локальный файл с именем storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Ручка расширенный формат JSON

Чтобы развернуть шаблон с многолинейными строками или комментариями с помощью Azure CLI `--handle-extended-json-format` с версией 2.3.0 или старше, необходимо использовать переключатель.  Пример:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
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

Чтобы проверить значения шаблона и параметров без фактического развертывания ресурсов, используйте [проверку группы развертывания az.](/cli/azure/group/deployment)

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Если ошибок не обнаружено, то команда возвращает сведения о тестовом развертывании. В частности, обратите внимание, что **error** имеет значение null.

```output
{
  "error": null,
  "properties": {
      ...
```

Если обнаруживается ошибка, то команда возвращает сообщение об ошибке. Например, попытка передать недопустимое значение для номера SKU учетной записи возвращает следующую ошибку:

```output
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

```output
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

## <a name="next-steps"></a>Следующие шаги

- Чтобы откатить к успешному развертыванию при ошибке, просмейте [откат по ошибке для успешного развертывания.](rollback-on-error.md)
- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Чтобы понять, как определить параметры в шаблоне, [см.](template-syntax.md)
- Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](common-deployment-errors.md).
- Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](secure-template-with-sas-token.md).
- Для безопасного развертывания службы в нескольких регионах обратитесь к статье о [диспетчере развертывания Azure](deployment-manager-overview.md).
