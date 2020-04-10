---
title: Обновление кластерных узлов для использования дисков управления Azure
description: Вот как обновить существующий кластер Service Fabric для использования управляемых дисков Azure практически без простоя кластера.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 5f4698718a35970e47de2a0ee6d053802c8ef919
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991217"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Обновление кластерных узлов для использования дисков управления Azure

[Управляемые диски Azure](../virtual-machines/windows/managed-disks-overview.md) являются рекомендуемыми дисковыми хранилищами для использования с виртуальными машинами Azure для постоянного хранения данных. Вы можете улучшить устойчивость рабочих нагрузок Service Fabric, модернизируя наборы виртуальной шкалы машин, лежащие в основе типов узлов для использования управляемых дисков. Вот как обновить существующий кластер Service Fabric для использования управляемых дисков Azure практически без простоя кластера.

Общая стратегия модернизации кластерного узла Service Fabric для использования управляемых дисков заключается в:

1. Развертывание в противном случае дублировать виртуальный набор масштаба машины этого `osDisk` типа узла, но с [управляемым объектомDisk](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) добавленвы в раздел виртуальной шкалы шаблона развертывания набора машины. Новый набор масштабов должен привязываться к тому же балансеру нагрузки / IP, что и оригинал, так что ваши клиенты не испытывают сбой в обслуживании во время миграции.

2. После того, как исходные и модернизированные наборы масштабов работают бок о бок, отменяем исходные экземпляры узлов по одному, чтобы системные службы (или реплики служб состояния) мигрировали в новый набор масштабов.

3. Проверить кластер и новые узлы являются здоровыми, а затем удалить исходный набор масштаба и состояние узлов для удаленных узлов.

В этой статье вы проведете этапы обновления типа первичного узла примерного кластера для использования управляемых дисков, избегая при этом какого-либо простоя кластера (см. примечание ниже). Начальное состояние испытательного кластера примера состоит из одного типа узла [Silver,](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)подкрепленного одним набором масштаба с пятью узлами.

> [!CAUTION]
> Вы будете испытывать сбой с этой процедурой только в том случае, если у вас есть зависимости от кластера DNS (например, при доступе к [Service Fabric Explorer).](service-fabric-visualizing-your-cluster.md) Архитектурная [лучшая практика для фронт-энд-сервисов](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) заключается в том, чтобы иметь какой-то [баланселизатор нагрузки](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) перед типами узлов, чтобы сделать возможным замена узлов без простоя.

Ниже [приведены шаблоны и cmdlets](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) для менеджера ресурсов Azure, которые мы будем использовать для завершения сценария обновления. Изменения шаблона будут объяснены в [развертывании обновленного набора шкалы для типа основного узла](#deploy-an-upgraded-scale-set-for-the-primary-node-type) ниже.

## <a name="set-up-the-test-cluster"></a>Настройка тестового кластера

Давайте навескажем начальный кластер тестирования Service Fabric. Во-первых, [загрузите](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) шаблоны шаблонов управления ресурсами Azure, которые мы будем использовать для завершения этого сценария.

Затем вопийте на свою учетную запись Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Следующие команды помогут вам создать новый самоподписанный сертификат и развернуть кластер тестирования. Если у вас уже есть сертификат, который вы хотели бы использовать, перейдите к [использованию существующего сертификата для развертывания кластера.](#use-an-existing-certificate-to-deploy-the-cluster)

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Создание самоподписанного сертификата и развертывание кластера

Во-первых, назначаем переменные, необходимые для развертывания кластера Service Fabric. Отрегулируйте значения `resourceGroupName` `certSubjectName`для, `parameterFilePath` `templateFilePath` и для вашей конкретной учетной записи и среды:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Убедитесь, `certOutputFolder` что местоположение существует в локальной машине перед запуском команды для развертывания нового кластера Service Fabric.

Затем откройте [*файл Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) и `clusterName` направьте значения и `dnsName` соответсь динамическим значениям, установленным в PowerShell, и сохраните свои изменения.

Затем развернуть кластер тестирования Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

После завершения развертывания найдите файл *.pfx* ()`$certPfx`на локальной машине и импортируйте его в магазин сертификатов:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Операция вернет отпечаток пальца сертификата, который вы будете использовать для [подключения к новому кластеру](#connect-to-the-new-cluster-and-check-health-status) и проверки его состояния работоспособности. (Пропустить следующий раздел, который является альтернативным подходом к развертыванию кластеров.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Используйте существующий сертификат для развертывания кластера

Для развертывания тестового кластера можно также использовать существующий сертификат Azure Key Vault Vault. Для этого вам необходимо [получить ссылки на ваш ключ Vault](#obtain-your-key-vault-references) и отпечаток пальца сертификата.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Откройте файл [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) и измените значения для `clusterName` и `dnsName` чего-то уникального.

Наконец, обозначьте название группы `templateFilePath` ресурсов `parameterFilePath` для кластера и установите и расположение файлов *Initial-1NodeType-UnmanagedDisks:*

> [!NOTE]
> Назначенная группа ресурсов уже должна существовать и находиться в том же регионе, что и ваше ключевое хранилище.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Наконец, запустите следующую команду для развертывания исходного кластера тестирования:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Подключение к новому кластеру и проверка состояния работоспособности

Подключитесь к кластеру и убедитесь, что все `clusterName` пять `thumb` его узлов являются здоровыми (замена и переменных для кластера):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

С этим мы готовы приступить к процедуре обновления.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Развертывание обновленного набора шкалы для типа основного узла

Для обновления или *вертикального масштаба*типа узлов нам необходимо развернуть копию виртуального набора масштабов машины этого типа узла, который в противном `nodeTypeRef`случае `subnet`идентичен исходной набору масштабов (включая ссылку на тот же , и), `loadBalancerBackendAddressPools`за исключением того, что он включает в себя желаемое обновление/изменения и свой собственный отдельный подсеть и входящий пул адресов NAT. Поскольку мы модернизируем тип основного узла, новый`isPrimary: true`набор масштабов будет помечен как основной (), как и исходный набор масштаба. (Для непервичных обновлений типа узлов просто опустите это.)

Для удобства необходимые для вас изменения уже внесены в [шаблон](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) и [файлы параметров](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) *Upgrade-1NodeType-2ScaleSets-ManagedDisks.*

В следующих разделах подробно будут описаны изменения шаблона. Если вы предпочитаете, вы можете пропустить объяснение и продолжить [следующий шаг процедуры обновления.](#obtain-your-key-vault-references)

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Обновление шаблона кластера с обновленным набором масштабов

Ниже приведены изменения исходного шаблона развертывания кластеров для добавления обновленного набора шкалы для типа основного узла.

#### <a name="parameters"></a>Параметры

Добавьте параметры для имени экземпляра, количества и размера нового набора шкалы. Обратите `vmNodeType1Name` внимание, что он уникален для нового набора шкалы, в то время как значения количества и размера идентичны исходным набором шкалы.

**Шаблон файла**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Файл параметров**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Переменные

В разделе `variables` шаблона развертывания добавьте запись для входящего пула адресов NAT нового набора шкалы.

**Шаблон файла**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Ресурсы

В разделе *ресурсов* шаблона развертывания добавьте новый набор виртуальной шкалы машин, имея в виду следующие вещи:

* Новый набор шкалы ссылается на тот же тип узла, что и исходный:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Новая шкала устанавливает ссылки на тот же адрес бэкэнда и подсети балансового баланса нагрузки (но использует другой балансоровую нагрузку, входящий в пул NAT):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Как и исходный набор шкалы, новый набор шкалы помечается как основной тип узла. (При обновлении типов непервичных узлов опустите это изменение.)

    ```json
    "isPrimary": true,
    ```

* В отличие от исходного набора масштабов, новый набор масштабов обновляется для использования управляемых дисков.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

После того, как вы внедрили все изменения в файлах шаблонов и параметров, перейдите к следующему разделу, чтобы получить ссылки Key Vault и развернуть обновления в кластере.

### <a name="obtain-your-key-vault-references"></a>Получите ссылки на Key Vault

Для развертывания обновленной конфигурации сначала можно получить несколько ссылок на сертификат кластера, хранящийся в Хранилище ключей. Самый простой способ найти эти значения через портал Azure. Что вам понадобится:

* **URL-адрес Ключевого Убежища сертификата кластера.** На портале Key Vault in Azure выберите **Сертификаты** > Ваш**желаемый***сертификат* > Secret Secret:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Отпечаток пальца сертификата кластера.** (Вы, вероятно, уже есть это, если вы [подключены к первоначальному кластеру,](#connect-to-the-new-cluster-and-check-health-status) чтобы проверить его состояние здоровья.) Из того же лезвия**сертификата (Сертификаты** > *Ваш желаемый сертификат)* на портале Azure, копия **X.509 SHA-1 Thumbprint (в гексе)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Идентификатор ресурсов вашего ключевого убежища.** На портале Key Vault in Azure выберите**идентификатор ресурсов** **свойств:** > 

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Развертывание обновленного шаблона

`parameterFilePath` Отрегулируйте `templateFilePath` и по мере необходимости, а затем запустите следующую команду:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Когда развертывание завершится, проверьте работу кластера снова и убедитесь, что все десять узлов (пять в исходном и пять в новом наборе шкалы) являются здоровыми.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Мигрировать семенные узлы в новый набор масштаба

Теперь мы готовы начать отключение узлов исходного набора масштаба. По мере того, как эти узлы отключаются, системные службы и семенные узлы мигрируют на визы нового набора шкалы, поскольку они также помечены как основной тип узла.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Используйте Service Fabric Explorer для мониторинга миграции семенных узлов в новый набор масштабов и прогрессирования узлов в исходной шкале, от *отключать* до *статуса инвалидов.*

![Сервис Fabric Explorer показывает состояние отключенных узлов](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Для завершения операции отключения во всех узлах исходного набора шкалы может потребоваться некоторое время. Чтобы гарантировать согласованность данных, только один узлы семян может изменяться одновременно. Каждое изменение узла семян требует обновления кластера; таким образом, замена семенного узла требует двух обновлений кластера (по одному для добавления и удаления узлов). Обновление пяти семенных узлов в этом сценарии выборочного варианта приведет к десяти обновлениям кластеров.

## <a name="remove-the-original-scale-set"></a>Удалить исходный набор масштабов

Как только операция отключения завершена, удалите набор шкалы.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

В Service Fabric Explorer удаленные узлы (и, следовательно, *состояние кластерного состояния здоровья)* теперь будут отображаться в состоянии *ошибки.*

![Служба Fabric Explorer показывает отключенные узлы в состоянии ошибки](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Удалите устаревшие узлы из кластера Service Fabric для восстановления состояния кластерного состояния работы в *OK.*

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Служба Fabric Explorer с вниз узлы в состоянии ошибки удалены](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Следующие шаги

В этом пошаговом шаге вы узнали, как обновить наборы виртуальной шкалы машин кластера Service Fabric для использования управляемых дисков, избегая при этом перебоев в обслуживании во время процесса. Для получения дополнительной информации по смежным темам ознакомьтесь со следующими ресурсами.

Вы узнаете, как выполнять следующие задачи:

* [Масштабирование типа первичного узла кластера Service Fabric](service-fabric-scale-up-node-type.md)

* [Преобразование шаблона набора масштабов для использования управляемых дисков](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Удаление типа узла Service Fabric](service-fabric-how-to-remove-node-type.md)

См. также:

* [Пример: Обновление кластерных узлов для использования дисков с управлением Azure](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [Рекомендации по вертикальному масштабированию](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)