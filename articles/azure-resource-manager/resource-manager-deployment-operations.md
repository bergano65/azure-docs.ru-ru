---
title: Журнал развертывания
description: Сведения о просмотре операций развертывания Azure Resource Manager с помощью портала, PowerShell, Azure CLI и REST API.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 23687ddcfb7911a999ee06ac8df8badf341b41d9
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484199"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Просмотр журнала развертывания с помощью Azure Resource Manager

Azure Resource Manager позволяет просматривать журнал развертывания и проверять конкретные операции в прошлых развертываниях. Можно просмотреть развернутые ресурсы и получить сведения об ошибках.

Сведения об устранении некоторых ошибок развертывания см. в статье об [устранении распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Портал

Для получения сведений о развертывании из журнала развертывания.

1. Выберите группу ресурсов, которую необходимо проверить.

1. Выберите ссылку в разделе **развертывания**.

   ![Выбор журнала развертывания](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Выберите одно из развертываний из журнала развертывания.

   ![Выбор развертывания](./media/resource-manager-deployment-operations/select-details.png)

1. Отобразится сводка развертывания, включая список развернутых ресурсов.

    ![Сводка по развертыванию](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Чтобы просмотреть шаблон, используемый для развертывания, выберите **шаблон**. Вы можете скачать шаблон, чтобы использовать его повторно.

    ![Отображение шаблона](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Если развертывание завершилось сбоем, отображается сообщение об ошибке. Выберите сообщение об ошибке для получения дополнительных сведений.

    ![Просмотр невыполненного развертывания](./media/resource-manager-deployment-operations/show-error.png)

1. Отображается подробное сообщение об ошибке.

    ![Просмотреть сведения об ошибке](./media/resource-manager-deployment-operations/show-details.png)

1. Идентификатор корреляции используется для наблюдения за связанными событиями и может быть полезен при работе с технической поддержкой для устранения неполадок при развертывании.

    ![Получить идентификатор корреляции](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Чтобы узнать больше о том, на каком шаге произошел сбой, выберите **сведения об операции**.

    ![Выбор операций развертывания](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Вы увидите сведения об этом шаге развертывания.

    ![Отобразить сведения об операции](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Общее состояние развернутой службы можно получить с помощью команды **Get-AzResourceGroupDeployment**.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Вы можете отфильтровать результаты, чтобы отобразить только те развертывания, которые завершились сбоем.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

Идентификатор корреляции используется для наблюдения за связанными событиями и может быть полезен при работе с технической поддержкой для устранения неполадок при развертывании. Чтобы получить идентификатор корреляции, используйте:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Каждое развертывание включает в себя несколько операций. Каждая операция представляет шаг в процессе развертывания. Чтобы определить, что пошло не так при развертывании, обычно требуется просмотреть сведения об операциях развертывания. Состояние операций можно просмотреть с помощью команды **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Эта команда возвращает несколько операций, каждая из которых представлена в следующем формате:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Чтобы получить дополнительные сведения о завершившихся сбоем операциях, получите свойства для операций с состоянием **Failed** .

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

В результате будут возвращены все завершившиеся сбоем операции. Каждая из них будет представлена в следующем формате:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Обратите внимание на значения serviceRequestId и trackingId операции. serviceRequestId может быть полезным при работе с технической поддержкой для устранения проблемы развертывания. Вы будете использовать trackingId на следующем шаге, чтобы сосредоточиться на определенной операции.

Чтобы получить сообщение о состоянии конкретной завершившейся сбоем операции, используйте следующую команду:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Возвращаемые данные:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Каждая операция развертывания в Azure включает в себя содержимое запроса и ответа. Во время развертывания можно использовать параметр **DeploymentDebugLogLevel** , чтобы указать, что запрос и/или ответ записываются в журнал.

Получить эту информацию из журнала и сохранить ее локально можно с помощью следующих команд PowerShell:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Интерфейс командной строки Azure

Чтобы получить общее состояние развертывания, используйте команду **Azure Group Deployment демонстрация** .

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
Идентификатор корреляции используется для наблюдения за связанными событиями и может быть полезен при работе с технической поддержкой для устранения неполадок при развертывании.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

Чтобы просмотреть операции развертывания, используйте следующую команду:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

В следующем примере показано, как получить сведения о развертывании. Документацию по последней версии API см. в разделе [deployments-Get](/rest/api/resources/deployments/get) Operation.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

В ответе обратите особое внимание на элементы **provisioningState**, **correlationId** и **error**. **correlationId** используется для отслеживания связанных событий и может быть полезен при взаимодействии со службой технической поддержки для устранения проблемы развертывания.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

В следующем примере показано, как получить операции развертывания. Документацию по последней версии API см. в разделе операция [развертывания-список](/rest/api/resources/deploymentoperations/list) .

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
Ответ будет содержать сведения о запросе и (или) ответе, в зависимости от того, что было указано в свойстве **debugSetting** во время развертывания.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>Дополнительная информация
* Сведения об устранении некоторых ошибок развертывания см. в статье об [устранении распространенных ошибок при развертывании ресурсов в Azure с помощью Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Дополнительные сведения об использовании журналов действий для мониторинга других типов действий см. в статье [Операции аудита с помощью диспетчера ресурсов](resource-group-audit.md).
* Чтобы проверить развернутую службу перед ее выполнением, ознакомьтесь со статьей [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](resource-group-template-deploy.md).

