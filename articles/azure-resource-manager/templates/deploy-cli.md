---
title: Развертывание ресурсов с помощью Azure CLI и шаблона
description: Используйте Azure Resource Manager и Azure CLI для развертывания ресурсов в Azure. Эти ресурсы определяются в шаблоне Resource Manager.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 8d033bb9ad1c841614ee1e48aa7edc6b8fe18550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372176"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Развертывание ресурсов с помощью шаблонов ARM и Azure CLI

В этой статье объясняется, как использовать Azure CLI с шаблонами Azure Resource Manager (шаблоны ARM) для развертывания ресурсов в Azure. Если вы не знакомы с концепциями развертывания и управления решениями Azure, см. раздел [Общие сведения о развертывании шаблонов](overview.md).

Команды развертывания изменились в Azure CLI версии 2.2.0. Для использования примеров в этой статье требуется Azure CLI версии 2.2.0 или более поздней.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Если у вас не установлен интерфейс командной строки Azure, можете использовать [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Область развертывания

Вы можете выбрать для развертывания группу ресурсов, подписку, группу управления или клиент. В большинстве случаев вы планируете развертывание в группе ресурсов. Чтобы применить политики и назначения ролей в более масштабной области, используйте развертывание по подписке, группе управления или развертыванию клиента. При развертывании в подписке можно создать группу ресурсов и развернуть в ней ресурсы.

В зависимости от области развертывания используются разные команды.

* Для развертывания в **группе ресурсов**используйте команду [AZ Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* Для развертывания в **подписке**используйте команду [AZ Deployment подсоздание](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Дополнительные сведения о развертываниях на уровне подписки см. в статье [Создание групп ресурсов и ресурсов на уровне подписки](deploy-to-subscription.md).

* Для развертывания в **группе управления**используйте команду [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  Дополнительные сведения о развертываниях на уровне групп управления см. в статье [Создание ресурсов на уровне группы управления](deploy-to-management-group.md).

* Чтобы выполнить развертывание в **клиенте**, используйте [AZ Deployment клиент Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  Дополнительные сведения о развертываниях на уровне клиента см. в статье [Создание ресурсов на уровне клиента](deploy-to-tenant.md).

В примерах, приведенных в этой статье, мы используем развертывание в группе ресурсов.

## <a name="deploy-local-template"></a>Развертывание локального шаблона

При развертывании ресурсов в Azure выполните следующие действия:

1. Вход в учетную запись Azure
2. Создайте группу ресурсов, которая послужит в качестве контейнера для развертываемых ресурсов. Имя группы ресурсов может содержать только буквы, цифры, точки, знаки подчеркивания, дефисы и скобки. Оно может содержать до 90 знаков и не должно заканчиваться точкой.
3. Разверните в группе ресурсов шаблон, который определяет создаваемые ресурсы.

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

## <a name="deployment-name"></a>Deployment name (Имя развертывания)

В предыдущем примере вы назвали развертывание `ExampleDeployment` . Если имя для развертывания не указано, используется имя файла шаблона. Например, если развернуть шаблон с именем `azuredeploy.json` и не указывать имя развертывания, то развертывание будет называться `azuredeploy` .

Каждый раз при выполнении развертывания в журнал развертывания группы ресурсов добавляется запись с именем развертывания. Если запустить другое развертывание и присвоить ему такое же имя, то Предыдущая запись будет заменена текущим развертыванием. Если требуется хранить уникальные записи в журнале развертывания, присвойте каждому развертыванию уникальное имя.

Чтобы создать уникальное имя, можно назначить случайное число.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Или добавьте значение даты.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

При выполнении параллельных развертываний в одной группе ресурсов с тем же именем развертывания завершается только Последнее развертывание. Все развертывания с тем же именем, которые не были завершены, заменяются последним развертыванием. Например, если вы запускаете развертывание с именем, `newStorage` которое развертывает учетную запись хранения с именем `storage1` и в то же время запускает другое развертывание с именем, `newStorage` которое развертывает учетную запись хранения с именем `storage2` , развертывается только одна учетная запись хранения. Результирующая учетная запись хранения называется `storage2` .

Однако при запуске развертывания с именем, `newStorage` которое развертывает учетную запись хранения с именем `storage1` , и сразу после завершения работы вы запускаете другое развертывание с именем, `newStorage` которое развертывает учетную запись хранения с именем `storage2` , а затем у вас есть две учетные записи хранения. Одна из них называется `storage1` , а другая называется `storage2` . Но в журнале развертывания имеется только одна запись.

При указании уникального имени для каждого развертывания их можно запускать параллельно без конфликтов. Если вы запускаете развертывание с именем, `newStorage1` которое развертывает учетную запись хранения с именем `storage1` и в то же время запускает другое развертывание с именем, `newStorage2` которое развертывает учетную запись хранения с именем `storage2` , то в журнале развертывания есть две учетные записи хранения и две записи.

Чтобы избежать конфликтов с параллельными развертываниями и обеспечить уникальность записей в журнале развертывания, присвойте каждому развертыванию уникальное имя.

## <a name="deploy-remote-template"></a>Развертывание удаленного шаблона

Вместо того чтобы хранить шаблоны ARM на локальном компьютере, вы можете хранить их во внешнем расположении. Вы можете хранить шаблоны в репозитории системы управления версиями (например, GitHub). А также их можно хранить в учетной записи хранения Azure для общего доступа в организации.

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

## <a name="deploy-template-spec"></a>Развертывание спецификации шаблона

Вместо развертывания локального или удаленного шаблона можно создать [спецификацию шаблона](template-specs.md). Спецификация шаблона — это ресурс в подписке Azure, который содержит шаблон ARM. Это позволяет легко обеспечить безопасный общий доступ к шаблону для пользователей в вашей организации. Используйте управление доступом на основе ролей Azure (Azure RBAC), чтобы предоставить доступ к спецификации шаблона. Сейчас эта функция доступна в предварительной версии.

В следующих примерах показано, как создать и развернуть спецификацию шаблона. Эти команды доступны только в том случае, если вы [подписались на предварительную версию](https://aka.ms/templateSpecOnboarding).

Сначала вы создадите спецификацию шаблона, предоставив шаблон ARM.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Затем вы получите идентификатор для спецификации шаблона и развернете его.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Дополнительные сведения см. в разделе [спецификации шаблонов Azure Resource Manager (Предварительная версия)](template-specs.md).

## <a name="preview-changes"></a>Предварительный просмотр изменений

Перед развертыванием шаблона можно просмотреть изменения, которые шаблон будет вносить в вашу среду. Используйте [операцию "что если](template-deploy-what-if.md) ", чтобы убедиться, что шаблон вносит необходимые изменения. Что если также проверяет шаблон на наличие ошибок.

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

Если вы используете Azure CLI в командной строке Windows (CMD) или PowerShell, передайте массив в формате: `exampleArray="['value1','value2']"` .

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

Чтобы передать объект, например для установки тегов, используйте JSON. Например, шаблон может содержать параметр, подобный приведенному ниже:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

В этом случае можно передать строку JSON, чтобы задать параметр, как показано в следующем скрипте Bash:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Используйте двойные кавычки вокруг JSON, который необходимо передать в объект.

### <a name="parameter-files"></a>Файлы параметров

Вместо передачи параметров в виде встроенных значений в сценарии вам может быть проще использовать JSON-файл, содержащий значения параметров. Файл параметров должен находиться в локальной среде. Внешние файлы параметров не поддерживаются в Azure CLI.

Дополнительные сведения о файле параметров см. в статье [Создание файла параметров Resource Manager](parameter-files.md).

Для передачи локального файла параметров используйте `@`, чтобы указать локальный файл с именем storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Обработку расширенного формата JSON

Чтобы развернуть шаблон с многострочными строками или комментариями с помощью Azure CLI с версией 2.3.0 или более ранней, необходимо использовать `--handle-extended-json-format` параметр.  Пример:

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

## <a name="next-steps"></a>Дальнейшие действия

- Если возникнет ошибка при развертывании, выполните откат по инструкциям из статьи [Откат к работоспособному развертыванию в случае ошибки](rollback-on-error.md).
- Сведения о том, как указать способ обработки ресурсов, которые существуют в группе ресурсов, но не определены в шаблоне, см. в [описании режимов развертывания с помощью Azure Resource Manager](deployment-modes.md).
- Сведения о том, как определить параметры в шаблоне, см. [в разделе Общие сведения о структуре и синтаксисе шаблонов ARM](template-syntax.md).
- Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](common-deployment-errors.md).
- Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](secure-template-with-sas-token.md).
