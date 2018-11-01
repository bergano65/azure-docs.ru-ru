---
title: Настройка зон обратного просмотра для проверки заголовка SMTP в Azure | Документация Майкрософт
description: В этой статье описывается, как настроить зоны обратного просмотра для проверки заголовка SMTP в Azure
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 2164dedc27d81aa488c3e054b4c6df067e96e612
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418200"
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Настройка зон обратного просмотра для проверки заголовка SMTP

В этой статье описывается, как использовать обратную зону в Azure DNS и создавать записи обратной зоны DNS (PTR) для проверки заголовка SMTP. 

## <a name="symptom"></a>Симптом

Если вы размещаете сервер SMTP в Microsoft Azure, при отправке или получении сообщения с удаленных почтовых серверов может появиться следующее сообщение об ошибке:

**554: No PTR Record** (554: запись типа PTR отсутствует). 

## <a name="solution"></a>Решение

Для виртуального IP-адреса в Azure обратные записи создаются не в пользовательских доменах, а в доменах, принадлежащих Майкрософт.

Чтобы настроить запись типа PTR в зонах, принадлежащих Майкрософт, используйте свойство -ReverseFqdn на ресурсе PublicIpAddress. Дополнительные сведения см. в статье [Настройка обратного просмотра DNS для размещенных в Azure служб](../dns/dns-reverse-dns-for-azure-services.md). 

Прежде чем настраивать запись типа PTR, убедитесь, что IP-адрес и обратное FQDN (полное доменное имя) принадлежат подписке. Если вы попытаетесь настроить обратное FQDN, не принадлежащее подписке, появится следующее сообщение об ошибке:

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) ReverseFqdn совпадает с полным доменным именем любого ресурса общедоступного IP-адреса в подписке. 
    2) ReverseFqdn разрешается в полное доменное имя (с использованием цепочки записей CName) любого ресурса общедоступного IP-адреса в той же подписке. 
    3) Он разрешается в IP-адрес (с использованием цепочки записей CName и A) ресурса статического общедоступного IP-адреса в подписке. 

Если вы вручную измените заголовок SMTP в соответствии с обратным FQDN по умолчанию, удаленный почтовый сервер все еще может выйти из строя, так как для него необходимо, чтобы узел заголовка SMTP соответствовал записи MX для домена.