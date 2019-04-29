---
title: Сопоставление настраиваемого поля со схемой службы "Сетка событий Azure"
description: В этой статье описано преобразование настраиваемой схемы в схему службы "Сетка событий Azure".
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: a0e054be3ab7d4818ac323eb5fb93968f57eca4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565503"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Сопоставление настраиваемых полей со схемой службы "Сетка событий Azure"

Если данные события не соответствуют ожидаемой [схеме службы "Сетка событий"](event-schema.md), вы по-прежнему можете использовать службу "Сетка событий", чтобы выполнять маршрутизацию событий для подписчиков. В этой статье описывается сопоставление схемы со схемой службы "Сетка событий".

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Установка предварительной версии функции

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Исходная схема событий

Предположим, что у вас есть приложение, которое отправляет события в следующем формате:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Несмотря на то, что данный формат не соответствует требуемой схеме, служба "Сетка событий" позволяет сопоставлять поля со схемой. Или же вы можете получить значения в исходной схеме.

## <a name="create-custom-topic-with-mapped-fields"></a>Создание настраиваемого раздела с сопоставленными полями

При создании настраиваемого раздела укажите способ сопоставления полей исходного события со схемой службы "Сетка событий". Существует три значения, которые позволяют настраивать сопоставление:

* Значение **input schema** указывает тип схемы. Его возможные значения: схема CloudEvents, схема custom event или схема "Сетка событий". Значение по умолчанию — схема "Сетка событий". При создании настраиваемого сопоставления между своей схемой и схемой "Сетка событий" используйте значение "схема custom event". Если события находятся в схеме CloudEvents, используйте значение "схема Cloudevents".

* Свойство **mapping default values** задает значения по умолчанию для полей в схеме "Сетка событий". Можно задать значения по умолчанию для `subject`, `eventtype`, и `dataversion`. Как правило, этот параметр используется, если настраиваемая схема не содержит поле, которое соответствует одному из этих трех полей. Например, можно указать, что версия данных всегда имеет значение **1.0**.

* Значение **mapping fields** сопоставляет поля из вашей схемы со схемой "Сетка событий". Необходимо указать значения в разделенных пробелами парах "ключ — значение". В качестве имени ключа используйте имя поля сетки событий. В качестве значения используйте имя поля. Можно использовать имена ключей для `id`, `topic`, `eventtime`, `subject`, `eventtype`, и `dataversion`.

Чтобы создать настраиваемый раздел с помощью Azure CLI, выполните следующий действия.

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

Для PowerShell используйте команду:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Подписка на раздел службы "Сетка событий"

При подписке на настраиваемый раздел укажите схему, которую вы хотите использовать для получения событий. Задайте значение: схема CloudEvents, схема custom event или схема "Сетка событий". Значение по умолчанию — схема "Сетка событий".

В следующем примере показана подписка на раздел службы "Сетка событий" и использование схемы "Сетка событий". Для интерфейса командной строки Azure:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

В следующем примере используется входная схема события:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

В следующем примере показана подписка на раздел службы "Сетка событий" и использование схемы "Сетка событий". Для PowerShell используйте команду:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

В следующем примере используется входная схема события:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Публикация события в разделе

Теперь вы можете отправить событие в настраиваемый раздел и просмотреть результат сопоставления. Отправьте событие в [пример схемы](#original-event-schema) с помощью следующего скрипта:

Для интерфейса командной строки Azure:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Для PowerShell используйте команду:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Теперь посмотрите на конечную точку веб-перехватчика. Две подписки доставили события в разных схемах.

Первая подписка использует схему сетки событий. Событие доставлено в следующем формате:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Эти поля содержат сопоставления из настраиваемого раздела. Поле **myEventTypeField** сопоставляется с полем **EventType**. Для полей **DataVersion** и **Subject** используются значения по умолчанию. Объект **Data** содержит поля исходной схемы событий.

Вторая подписка использует входную схему событий. Событие доставлено в следующем формате:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Обратите внимание, что были доставлены исходные поля.

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о [доставке сообщений и повторных попытках в Сетке событий](delivery-and-retry.md).
* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Сведения о том, как быстро приступить к использованию службы "Сетка событий", см. в разделе [Создание и перенаправление пользовательского события со службой "Сетка событий Azure"](custom-event-quickstart.md).
