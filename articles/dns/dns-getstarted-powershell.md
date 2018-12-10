---
title: Краткое руководство. Создание зоны и записи Azure DNS с помощью Azure PowerShell
description: Узнайте, как создать зону и запись DNS в службе DNS Azure. В этом пошаговом руководстве описано, как создать зону и запись DNS, а также как управлять ими с помощью Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: db6ac089f11477ecdb1568195855fae00d3eac61
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888364"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Краткое руководство. Создание зоны и записи Azure DNS с помощью Azure PowerShell

В этом кратком руководстве описано, как создать зону и запись Azure DNS с помощью Azure PowerShell. Эти действия также можно выполнить с помощью [портала Azure](dns-getstarted-portal.md) или [Azure CLI](dns-getstarted-cli.md). 

Зона DNS используется для размещения DNS-записей определенного домена. Чтобы разместить свой домен в Azure DNS, необходимо создать зону DNS для этого доменного имени. Каждая запись DNS для вашего домена создается внутри этой зоны DNS. Наконец, чтобы опубликовать зону DNS в Интернете, необходимо настроить серверы доменных имен для домена. Каждый из этих шагов описан ниже.

Azure DNS также поддерживает создание частных доменов. Пошаговые инструкции о том, как создать закрытую зону DNS и запись, см. в разделе [Приступая к работе с частными зонами Azure DNS с помощью PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-the-resource-group"></a>Создание группы ресурсов

Перед созданием зоны DNS создайте группу ресурсов, которая будет включать эту зону DNS:

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Создание зоны DNS

Зона DNS создается с помощью командлета `New-AzureRmDnsZone`. В следующем примере будет создана зона DNS *contoso.com* в группе ресурсов *MyResourceGroup*. Пример можно использовать для создания зоны DNS, заменив соответствующие значения собственными.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Создание записи DNS

Для создания наборов записей используется командлет `New-AzureRmDnsRecordSet`. В следующем примере создается запись с относительным именем www в зоне DNS contoso.com, в группе ресурсов MyResourceGroup. Полное доменное имя набора записей — www.contoso.com. Тип записи — A, IP-адрес — 1.2.3.4, а срок жизни составляет 3600 секунд.

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

## <a name="view-records"></a>Просмотр записей

Чтобы просмотреть список записей DNS в зоне, используйте следующую команду:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="update-name-servers"></a>Обновление серверов доменных имен

Когда вы убедитесь, что зона и записи DNS настроены правильно, необходимо настроить доменное имя для использования серверов доменных имен службы DNS Azure. Это позволит другим пользователям в Интернете находить ваши записи DNS.

Для получения списка серверов доменных имен для определенной зоны используется командлет `Get-AzureRmDnsZone`:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Эти серверы доменных имен необходимо настроить с помощью регистратора доменных имен (у которого было приобретено доменное имя). Регистратор предложит вам вариант настройки серверов доменных имен для домена. Дополнительные сведения см. в руководстве по [размещению домена в Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="delete-all-resources"></a>Удаление всех ресурсов

Чтобы очистить ненужные ресурсы, созданные при работе с этим кратким руководством, удалите группу ресурсов:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы создали свою первую зону и запись DNS с помощью Azure PowerShell, можно создать записи для веб-приложения в личном домене.

> [!div class="nextstepaction"]
> [Создание записей DNS для веб-приложения в пользовательском домене](./dns-web-sites-custom-domain.md)

