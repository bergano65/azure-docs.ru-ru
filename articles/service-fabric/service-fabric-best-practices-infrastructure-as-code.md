---
title: Инфраструктура Сервиса Обслуживания Azure как лучшие практики кода
description: Рекомендации по разработке и рекомендациям по управлению Azure Service Fabric как инфраструктурой как кодом.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 1c044d5fd973d3c577088a887f2fac413d2ab79d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551834"
---
# <a name="infrastructure-as-code"></a>Инфраструктура как код

В рамках рабочего сценария создайте кластеры Azure Service Fabric с помощью шаблонов Resource Manager. Шаблоны Resource Manager обеспечивают более высокий уровень контроля над свойствами ресурсов и согласованную модель ресурсов.

Примеры шаблонов Resource Manager для Windows и Linux доступны в [образцах Azure на сайте GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Их можно использовать в качестве отправной точки для создания шаблона кластера. Скачайте файлы `azuredeploy.json` и `azuredeploy.parameters.json` и измените их в соответствии со своими требованиями.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы развернуть шаблоны `azuredeploy.json` и `azuredeploy.parameters.json`, скачанные ранее, используйте следующие команды Azure CLI:

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

Создание ресурса с помощью PowerShell

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Ресурсы Azure Service Fabric

Вы можете развертывать приложения и службы в кластере Service Fabric с помощью Azure Resource Manager. Дополнительные сведения см. в статье [Управление приложениями и службами как ресурсами Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource). Ниже приведены рекомендуемые ресурсы приложения Service Fabric, которые следует добавить в список ресурсов шаблона Resource Manager.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Чтобы развернуть приложение с помощью Azure Resource Manager, сначала необходимо [создать пакет приложения Service Fabric sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). В следующем сценарии Python приведен пример создания sfpkg:

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Настройка автоматического обновления виртуальной машины Azure 
Обновление виртуальных машин является инициированной пользователем операцией, и рекомендуется использовать [обновление автоматической операционной системы установки виртуальной шкалы машин](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) для управления патчами кластеров Azure Service Fabric; Patch Orchestration Application — это альтернативное решение, предназначенное для размещения за пределами Azure, хотя POA может быть использована в Azure, при этом накладные расходы на размещение POA в Azure являются распространенной причиной, предпочитающей автоматическое обновление виртуальной системы управления машиной по сравнению с POA. Ниже приведены свойства шаблона шаблона вычислительной виртуальной шкалы вычислительной шкалы, набора ресурсов, позволяющие обновить АвтоОС:

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
При использовании автоматического обновления ОС с помощью Service Fabric новое изображение ОС выкатывается из одного домена обновления в то время, чтобы поддерживать высокую доступность услуг, работающих в Service Fabric. Для использования автоматических обновлений ОС в Service Fabric ваш кластер должен быть настроен на использование уровня надежности Silver или выше.

Убедитесь, что следующий ключ реестра установлен на ложное, чтобы предотвратить ваши окна хост машины от начала несогласованных обновлений: HKEY_LOCAL_MACHINE-SOFTWARE-Политики »Microsoft-WindowsUpdate-AU.

Ниже приведены свойства шаблона шаблона «Вычислительная виртуальная шкала вычислительной шкалы, набор ресурсов для установки ключа реестра WindowsUpdate» на ложное:
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Конфигурация кластера обновления кластера Azure Service Fabric
Ниже приводится свойство шаблона шаблона кластера Service Fabric Resource Manager для автоматического обновления:
```json
"upgradeMode": "Automatic",
```
Чтобы вручную обновить кластер, загрузите дистрибутив кабины/деба в кластерную виртуальную машину, а затем вывешив следующее PowerShell:
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>Дальнейшие действия

* Создание кластера на основе виртуальных машин или компьютеров под управлением Windows Server: [Создание кластера Azure Service Fabric в локальной или облачной средах](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Создание кластера на основе виртуальных машин или компьютеров под управлением Linux: [Создание кластера Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md).
* Узнайте о [вариантах поддержки Service Fabric](service-fabric-support.md)
