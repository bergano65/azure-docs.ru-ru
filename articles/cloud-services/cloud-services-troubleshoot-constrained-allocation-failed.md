---
title: Устранение неполадок Констраинедаллокатионфаилед при развертывании облачной службы в Azure | Документация Майкрософт
description: В этой статье показано, как устранить исключение Констраинедаллокатионфаилед при развертывании облачной службы в Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521173"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Устранение неполадок Констраинедаллокатионфаилед при развертывании облачной службы в Azure

В этой статье вы узнаете об устранении ошибок выделения, когда облачные службы Azure не могут быть развернуты из-за ограничений.

Microsoft Azure выделяется в следующих случаях:

- Обновление экземпляров облачных служб

- Добавление новых экземпляров веб-роли или рабочих ролей

- Развертывание экземпляров в облачной службе

Иногда при выполнении этих операций могут возникать ошибки даже до достижения лимита подписки Azure.

> [!TIP]
> Эта информация также может быть полезна при планировании развертывания служб.

## <a name="symptom"></a>Симптом

В портал Azure перейдите к своей облачной службе и в боковой панели выберите *журналы операций (классические)* , чтобы просмотреть журналы.

При проверке журналов облачной службы вы увидите следующее исключение:

|Тип исключения  |Сообщение об ошибке  |
|---------|---------|
|констраинедаллокатионфаилед     |Операция Azure " `{Operation ID}` " завершилась ошибкой с кодом COMPUTE. констраинедаллокатионфаилед. Сведения: сбой при выделении; не удалось удовлетворить ограничения в запросе. Запрошенное новое развертывание службы привязано к территориальной группе, предназначено для выполнения в виртуальной сети, или в этой размещенной службе уже существует развертывание. Любое из этих условий ограничивает новое развертывание использованием определенных ресурсов Azure. Повторите попытку позже или уменьшите размер виртуальной машины или число экземпляров роли. Кроме того, по возможности удалите вышеупомянутые ограничения или попробуйте выполнить развертывание в другом регионе.|

## <a name="cause"></a>Причина

Существует ошибка емкости для региона или кластера, в который выполняется развертывание. Это происходит, когда выбранный номер SKU ресурса недоступен для указанного расположения.

> [!NOTE]
> При развертывании первого узла облачной службы он *закрепляется* в пуле ресурсов. Пул ресурсов может быть одним кластером или группой кластеров.
>
> Со временем ресурсы в этом пуле ресурсов могут быть полностью использованы. Если облачная служба выполняет запрос на выделение дополнительных ресурсов, если в закрепленном пуле ресурсов нет достаточного количества ресурсов, запрос приведет к [сбою выделения](cloud-services-allocation-failures.md).

## <a name="solution"></a>Решение

В этом сценарии следует выбрать другой регион или номер SKU для развертывания облачной службы. Перед развертыванием или обновлением облачной службы можно определить, какие номера SKU доступны в регионе или зоне доступности. Следуйте приведенным ниже процессам [Azure CLI](#list-skus-in-region-using-azure-cli), [PowerShell](#list-skus-in-region-using-powershell)или [REST API](#list-skus-in-region-using-rest-api) .

### <a name="list-skus-in-region-using-azure-cli"></a>Вывод списка номеров SKU в регионе с помощью Azure CLI

Можно использовать команду [AZ VM List-SKU](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) .

- Используйте `--location` параметр, чтобы отфильтровать выходные данные в используемое расположение.
- Параметр `--size` позволяет выполнить поиск по частичному названию размера.
- Дополнительные сведения см. в разделе [Устранение ошибки для недоступности SKU](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli) .

    **Пример:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Пример результатов:** ![ Azure CLI выводятся результаты выполнения команды AZ VM List-SKU--Location southcentralus--size Standard_F--выходной таблице ", которая показывает доступные номера SKU.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Вывод списка номеров SKU в регионе с помощью PowerShell

Можно использовать команду [Get-азкомпутересаурцеску](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) .

- Отфильтруйте результаты по расположению.
- Эта команда поддерживается только в Azure PowerShell последней версии.
- Дополнительные сведения см. в разделе [Устранение ошибки для недоступности SKU](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell) .

**Пример:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Некоторые другие полезные команды:**

Отфильтровать расположения, которые содержат размер (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Отфильтровать все расположения, содержащие размер (v3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Вывод списка номеров SKU в регионе с помощью REST API

Вы можете использовать операцию " [список номеров SKU ресурсов](https://docs.microsoft.com/rest/api/compute/resourceskus/list) ". Он возвращает доступные номера SKU и регионы в приведенном ниже формате.

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Дальнейшие шаги

Для получения дополнительных решений об ошибках выделения ресурсов и для лучшего понимания того, как они создаются:

> [!div class="nextstepaction"]
> [Сбои при выделении (облачные службы)](cloud-services-allocation-failures.md)

Если проблема с Azure не устранена в этой статье, посетите форумы Azure в [MSDN и Stack overflow](https://azure.microsoft.com/support/forums/). Описание своей проблемы можно опубликовать на этих форумах или [в Twitter (@AzureSupport)](https://twitter.com/AzureSupport). Также можно отправить запрос в службу поддержки Azure. Чтобы отправить такой запрос, на странице [поддержки Azure](https://azure.microsoft.com/support/options/) щелкните *Получить поддержку*.
