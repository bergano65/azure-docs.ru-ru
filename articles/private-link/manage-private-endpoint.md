---
title: Управление подключением частной конечных точек в Azure
description: Узнайте, как управлять приватными конечными соединениями в Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 62b24b3e2f5c1b89fa7db581ac34cf58381db2a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452966"
---
# <a name="manage-a-private-endpoint-connection"></a>Управление подключением к частной конечной точке
Azure Private Link работает на модели потока потока разрешений, в которой потребитель службы Private Link может запросить подключение к поставщику услуг для потребления услуг. Поставщик услуг может решить, разрешать потребителю подключение или нет. Azure Private Link позволяет поставщикам услуг управлять подключением к частной конечной точке на своих ресурсах. В этой статье содержатся инструкции по управлению соединениями private Endpoint.

![Управление частными конечными точками](media/manage-private-endpoint/manage-private-endpoint.png)

Существует два метода утверждения соединения, из которого может выбрать потребитель службы Private Link:
- **Автоматическое:** Если у потребителя услуг есть разрешения RBAC на ресурсе поставщика услуг, потребитель может выбрать метод автоматического утверждения. В этом случае, когда запрос достигает ресурса поставщика услуг, от поставщика услуг не требуется никаких действий и подключение автоматически утверждается. 
- **Руководство**: Напротив, если у потребителя услуг нет разрешений RBAC на ресурсе поставщика услуг, потребитель может выбрать метод ручного утверждения. В этом случае запрос на подключение отображается на ресурсах службы как **«Ожидание»** Поставщик услуг должен вручную утвердить запрос до того, как можно будет установить соединения. В ручных случаях потребитель услуг может также указать сообщение с просьбой предоставить дополнительный контекст поставщику услуг. Поставщик услуг имеет следующие варианты на выбор для всех частных соединений конечных точек: **Утверждено,** **Отклонить**, **Удалить**.

В приведенной ниже таблице показаны различные действия поставщика услуг и результирующее состояния соединения для частных конечных точек.  Поставщик услуг также может изменить состояние соединения частного конечного соединения на более позднее время без вмешательства потребителей. Действие обновит состояние конечной точки на стороне потребителя. 


|Действия поставщика услуг   |Обслуживание потребителей Частные конечные точки государства   |Описание   |
|---------|---------|---------|
|None    |    Ожидает     |    Соединение создается вручную и находится на утверждении владельцем ресурсов Private Link.       |
|Утверждение    |  Approved       |  Подключение утверждено автоматически или вручную и готово к использованию.     |
|Reject     | Отклонено        | Подключение отклонил владелец ресурса Приватного канала.        |
|Удалить    |  Отключено       | Соединение было удалено владельцем частного ресурса ссылки, частная конечная точка становится информативной и должна быть удалена для очистки.        |
|   |         |         |
   
## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Управление подключениями к частным конечным точкам в ресурсах Azure PaaS
Портал является предпочтительным методом управления частными конечными соединениями на ресурсах Azure PaaS. В настоящее время у нас нет поддержки PowerShell/CLI для управления соединениями на ресурсах Azure PaaS.
1. Войдите на портал Azure по адресу https://portal.azure.com.
2. Перейдите в частный центр ссылок.
3. Под **ресурсами**выберите тип ресурса, который требуется для управления частными соединениями конечных точек.
4. Для каждого из типов ресурсов можно просмотреть количество подключенных к ним частных конечных точек. Вы можете фильтровать ресурсы по мере необходимости.
5. Выберите частные соединения конечных точек.  Под перечисленными соединениями выберите соединение, которым вы хотите управлять. 
6. Вы можете изменить состояние соединения, выбрав из опций в верхней части.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Управление частными соединениями конечных точек на клиенте /партнере, принадлежащем частному линкову

Azure PowerShell и Azure CLI являются предпочтительными методами управления подключением частных конечных точек в службах Microsoft Partner или службах, принадлежащих клиентам. В настоящее время у нас нет поддержки портала для управления соединениями на сервисе Private Link.  
 
### <a name="powershell"></a>PowerShell 
  
Используйте следующие команды PowerShell для управления частными соединениями конечных точек.  
#### <a name="get-private-link-connection-states"></a>Получить частные соединения соединения государств 
Используйте `Get-AzPrivateLinkService` cmdlet, чтобы получить частные соединения конечных точек и их состояния.  
```azurepowershell
Get-AzPrivateLinkService -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
 ```
 
#### <a name="approve-a-private-endpoint-connection"></a>Утвердить приватное соединение конечных точек 
 
Используйте `Approve-AzPrivateEndpointConnection` cmdlet, чтобы утвердить соединение Private Endpoint. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Отказать в подключении частной конечных точек 
 
Используйте `Deny-AzPrivateEndpointConnection` cmdlet, чтобы отклонить соединение Private Endpoint. 
```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```
#### <a name="remove-private-endpoint-connection"></a>Удалить приватное соединение конечных точек 
 
Используйте `Remove-AzPrivateEndpointConnection` cmdlet для удаления приватного соединения endpoint. 
```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection1 -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkServiceName 
```
 
### <a name="azure-cli"></a>Azure CLI 
 
Используйте `az network private-link-service update` для управления подключением к частной конечной точке. Состояние соединения указывается ```azurecli connection-status``` в параметре. 
```azurecli
az network private-link-service connection update -g myResourceGroup -n myPrivateEndpointConnection1 --service-name myPLS --connection-status Approved 
```

   

## <a name="next-steps"></a>Дальнейшие действия
- [Узнайте о частных конечных точках](private-endpoint-overview.md)
 
