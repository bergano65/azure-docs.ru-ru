---
title: Обновление узлов кластера для использования управляемых дисков Azure
description: Вот как можно обновить существующий кластер Service Fabric, чтобы использовать управляемые диски Azure с минимальным временем простоя кластера.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: 152bdaea121e65de8332fcde8543b8158ff11714
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88717529"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Обновление узлов кластера для использования управляемых дисков Azure

[Управляемые диски Azure](../virtual-machines/managed-disks-overview.md) являются рекомендуемым предложением дискового хранилища для использования с виртуальными машинами Azure для постоянного хранения данных. Повысить устойчивость рабочих нагрузок Service Fabric можно, обновив масштабируемые наборы виртуальных машин, которые лежат в качестве типов узлов для использования управляемых дисков. Вот как можно обновить существующий кластер Service Fabric, чтобы использовать управляемые диски Azure с минимальным временем простоя кластера.

Общая стратегия обновления Service Fabric узла кластера для использования управляемых дисков состоит в следующих целях:

1. Разверните другой дубликат масштабируемого набора виртуальных машин для этого типа узла, но с помощью объекта [манажеддиск](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) , добавленного в `osDisk` раздел шаблона развертывания масштабируемого набора виртуальных машин. Новый масштабируемый набор должен быть привязан к тому же подсистеме балансировки нагрузки или IP-адресу, что и исходный, поэтому во время миграции клиенты не сталкиваются со сбоями в работе службы.

2. После того как исходные и обновленные масштабируемые наборы работают параллельно, отключите экземпляры исходного узла по одному за раз, чтобы системные службы (или реплики служб с отслеживанием состояния) переводились в новый масштабируемый набор.

3. Убедитесь, что кластер и новые узлы работоспособны, а затем удалите исходный масштабируемый набор и состояние узла для удаленных узлов.

В этой статье описано, как обновить тип первичного узла в примере кластера, чтобы использовать управляемые диски, избегая простоя кластера (см. Примечание ниже). Начальное состояние примера тестового кластера состоит из одного типа узла " [Серебряная устойчивость](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)", поддерживаемого одним масштабируемым набором с пятью узлами.

> [!NOTE]
> Ограничения базового балансировщика нагрузки SKU не позволяют добавить дополнительный масштабируемый набор. Вместо этого рекомендуется использовать балансировщик нагрузки "Стандартный" SKU. Дополнительные сведения см. в разделе [Сравнение двух номеров SKU](/azure/load-balancer/skus).

> [!CAUTION]
> При выполнении этой процедуры будет возникать сбой, если у вас есть зависимости от DNS кластера (например, при доступе к [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)). [Оптимальной архитектурой для интерфейсных служб](/azure/architecture/microservices/design/gateway) является наличие какого-либо [подсистемы балансировки нагрузки](/azure/architecture/guide/technology-choices/load-balancing-overview) перед типами узлов, чтобы сделать возможным переключение узлов без сбоя.

Ниже приведены [шаблоны и командлеты](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) для Azure Resource Manager, которые будут использоваться для завершения сценария обновления. Изменения в шаблоне будут объяснены в [развертывании обновленного масштабируемого набора для первичного типа узла](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  .

## <a name="set-up-the-test-cluster"></a>Настройка тестового кластера

Давайте создадим начальный тестовый кластер Service Fabric. Сначала [Скачайте](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage) примеры шаблонов Azure Resource Manager, которые будут использоваться для выполнения этого сценария.

Затем войдите в учетную запись Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Следующие команды помогут создать новый самозаверяющий сертификат и развернуть тестовый кластер. Если у вас уже есть сертификат, который вы хотите использовать, перейдите к [использованию существующего сертификата для развертывания кластера](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Создание самозаверяющего сертификата и развертывание кластера

Сначала назначьте переменные, которые потребуются для развертывания кластера Service Fabric. Измените значения `resourceGroupName`, `certSubjectName`, `parameterFilePath` и `templateFilePath` в соответствии со своей учетной записью и средой.

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
> Перед выполнением команды для развертывания нового кластера Service Fabric убедитесь, что на локальном компьютере существует расположение `certOutputFolder`.

Затем откройте [*Initial-1NodeType-UnmanagedDisks.parameters.jsв*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) файле и измените значения для параметров `clusterName` и, `dnsName` чтобы они соответствовали динамическим значениям, заданным в PowerShell, и сохраните изменения.

Затем разверните тестовый кластер Service Fabric.

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

После завершения развертывания найдите *PFX*-файл (`$certPfx`) на локальном компьютере и импортируйте его в хранилище сертификатов.

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Операция вернет отпечаток сертификата, который будет использоваться для [подключения к новому кластеру](#connect-to-the-new-cluster-and-check-health-status) и проверки его состояния работоспособности. (Пропустите следующий раздел, который является альтернативным подходом к развертыванию кластера.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Использование существующего сертификата для развертывания кластера

Для развертывания тестового кластера можно также использовать существующий сертификат Azure Key Vault. Для этого необходимо [получить ссылки на Key Vault](#obtain-your-key-vault-references) и отпечаток сертификата.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Откройте [*Initial-1NodeType-UnmanagedDisks.parameters.jsв*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) файле и измените значения для `clusterName` и `dnsName` на что-то уникальное.

Наконец, назначьте имя группы ресурсов для кластера и задайте `templateFilePath` `parameterFilePath` расположение и расположения файлов *начального 1NodeType-унманажеддискс* :

> [!NOTE]
> Указанная группа ресурсов должна уже существовать и находиться в том же регионе, что и Key Vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Наконец, выполните следующую команду, чтобы развернуть исходный тестовый кластер:

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

Подключитесь к кластеру и убедитесь, что все пять его узлов работоспособны (замените `clusterName` `thumb` переменные и для кластера):

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

После этого мы готовы начать процедуру обновления.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Развертывание обновленного масштабируемого набора для типа первичного узла

Чтобы обновить или *вертикально масштабировать*тип узла, необходимо развернуть копию масштабируемого набора виртуальных машин этого типа, который в противном случае идентичен исходному масштабируемому набору (включая ссылку на те же `nodeTypeRef` , `subnet` и `loadBalancerBackendAddressPools` ), за исключением того, что он включает в себя требуемое обновление, изменения и собственную отдельную подсеть и пул адресов входящего трафика NAT. Так как мы обновляем тип первичного узла, новый масштабируемый набор будет помечен как основной ( `isPrimary: true` ), как и исходный масштабируемый набор. (Для обновлений типа узла, не являющегося первичным, просто пропустите это.)

Для удобства необходимые изменения уже внесены в файлы [шаблонов](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) *Upgrade-1NodeType-2ScaleSets-манажеддискс* и [Parameters](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) .

В следующих разделах подробно рассматриваются изменения шаблонов. При желании вы можете пропустить объяснение и перейти к [следующему шагу процедуры обновления](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Обновление шаблона кластера с помощью обновленного масштабируемого набора

Ниже приведены изменения в разделе шаблона развертывания исходного кластера для добавления обновленного масштабируемого набора для типа первичного узла.

#### <a name="parameters"></a>Параметры

Добавьте параметры для имени экземпляра, количества и размера нового масштабируемого набора. Обратите внимание, что `vmNodeType1Name` уникален для нового масштабируемого набора, а значения Count и size идентичны исходному масштабируемому набору.

**Файл шаблона**

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

В разделе шаблон развертывания `variables` добавьте запись для пула адресов входящего трафика NAT для нового масштабируемого набора.

**Файл шаблона**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Ресурсы

В разделе *ресурсы* шаблона развертывания добавьте новый масштабируемый набор виртуальных машин, учитывая следующие моменты.

* Новый масштабируемый набор ссылается на тот же тип узла, что и исходный:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Новый масштабируемый набор ссылается на один и тот же внутренний адрес подсистемы балансировки нагрузки и подсеть (но использует другой пул NAT входящего балансировщика нагрузки):

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

* Как и в исходном масштабируемом наборе, новый масштабируемый набор помечается как тип первичного узла. (При обновлении типов, не являющихся первичными узлами, пропустите это изменение.)

    ```json
    "isPrimary": true,
    ```

* В отличие от исходного масштабируемого набора, новый масштабируемый набор обновляется для использования управляемых дисков.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

После реализации всех изменений в файлах шаблонов и параметров перейдите к следующему разделу, чтобы получить ссылки на Key Vault и развернуть обновления в кластере.

### <a name="obtain-your-key-vault-references"></a>Получение ссылок на Key Vault

Чтобы развернуть обновленную конфигурацию, сначала необходимо получить несколько ссылок на сертификат кластера, хранящийся в Key Vault. Самый простой способ найти эти значения — с помощью портал Azure. Что вам понадобится

* **URL-адрес Key Vault сертификата кластера.** Из Key Vault в портал Azure выберите Сертификаты для **Certificates**  >  *Your desired certificate*  >  **идентификатора секрета**нужного сертификата:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Отпечаток сертификата кластера.** (Возможно, у вас уже есть [Подключение к первоначальному кластеру](#connect-to-the-new-cluster-and-check-health-status) , чтобы проверить его состояние работоспособности.) В той же колонке сертификата (**Сертификаты**, для которой  >  *нужен нужный сертификат*) в портал Azure скопируйте **отпечаток X. 509 SHA-1 (в шестнадцатеричном формате)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **Идентификатор ресурса Key Vault.** В Key Vault в портал Azure выберите **Свойства**  >  **идентификатор ресурса**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Развертывание обновленного шаблона

Измените параметры `parameterFilePath` и `templateFilePath` соответствующим образом и выполните приведенную ниже команду.

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

После завершения развертывания снова проверьте работоспособность кластера и убедитесь, что все десять узлов (на исходном и пять в новом масштабируемом наборе) работоспособны.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Перенос начальных узлов в новый масштабируемый набор

Теперь можно приступить к отключению узлов исходного масштабируемого набора. Когда эти узлы будут отключены, системные службы и начальные узлы будут перенесены на виртуальные машины нового масштабируемого набора, так как он также отнесен к типу первичного узла.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Используйте Service Fabric Explorer, чтобы отслеживать перенос начальных узлов в новый масштабируемый набор и переход узлов в исходном масштабируемом наборе из состояния *Отключение* в состояние *Отключен*.

![Service Fabric Explorer отображение состояния отключенных узлов](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Выполнение операции отключения для всех узлов исходного масштабируемого набора может занять некоторое время. Для обеспечения согласованности данных в каждый момент времени допускается изменение только одного начального узла. Для изменения каждого начального узла требуется обновление кластера. Соответственно, для замены начального узла требуются два обновления кластера (одно для добавления и второе для удаления узла). Для обновления пяти начальных узлов в этом примере сценария потребуются 10 обновлений кластера.

## <a name="remove-the-original-scale-set"></a>Удаление исходного масштабируемого набора

После завершения операции отключения удалите масштабируемый набор.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

В Service Fabric Explorer удаленные узлы (и, соответственно, параметр *Состояние работоспособности кластера*) теперь будут отображаться в состоянии *Ошибка*.

![Service Fabric Explorer отображения отключенных узлов в состоянии ошибки](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Удалите устаревшие узлы из кластера Service Fabric, чтобы восстановить состояние работоспособности кластера в значение *ОК*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer с отключенными узлами в состоянии ошибки "удалено"](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом пошаговом руководстве вы узнали, как обновить масштабируемые наборы виртуальных машин кластера Service Fabric, чтобы использовать управляемые диски, избегая простоев службы во время процесса. Дополнительные сведения по связанным темам см. в следующих ресурсах.

Вы узнаете, как выполнять следующие задачи:

* [Масштабирование типа первичного узла кластера Service Fabric](service-fabric-scale-up-primary-node-type.md)

* [Преобразование шаблона масштабируемого набора для использования управляемых дисков](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Удаление типа узла Service Fabric](service-fabric-how-to-remove-node-type.md)

См. также:

* [Пример. обновление узлов кластера для использования управляемых дисков Azure](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade-no-outage)

* [Рекомендации по вертикальному масштабированию](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)
