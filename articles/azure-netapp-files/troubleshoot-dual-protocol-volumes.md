---
title: Устранение неполадок томов с двумя протоколами для Azure NetApp Files | Документация Майкрософт
description: Описываются сообщения об ошибках и способы их устранения, которые могут помочь при устранении неполадок с двумя протоколами для Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/22/2021
ms.author: b-juche
ms.openlocfilehash: fb4233a87231dddb1e3cb2777ac2ef53a61f833e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726621"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Устранение неполадок с томами с двумя протоколами

В этой статье описываются способы устранения ошибок, которые могут возникнуть при создании томов с двумя протоколами или управлении ими.

## <a name="error-conditions-and-resolutions"></a>Условия и способы устранения ошибок

|     Условия ошибок    |     Решение    |
|-|-|
| Протокол LDAP через TLS включен, а создание тома с двойным протоколом завершается ошибкой `This Active Directory has no Server root CA Certificate` .    |     Если эта ошибка возникает при создании тома с двумя протоколами, убедитесь, что сертификат корневого ЦС отправлен в учетную запись NetApp.    |
| Создание тома с двойным протоколом завершается ошибкой `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  На DNS-сервере может отсутствовать запись указателя (PTR) компьютера узла Active Directory. Необходимо создать зону обратного просмотра на DNS-сервере, а затем добавить запись PTR для компьютера узла Active Directory в эту зону обратного просмотра. <br> Например, предположим, что IP-адрес компьютера, на котором размещается компьютер, — это `1.1.1.1` , имя узла (как было найдено с помощью `hostname` команды) `AD1` , а доменным именем является `contoso.com` .  Запись типа PTR, добавленная в зону обратного просмотра, должна иметь значение `1.1.1.1`  ->  `contoso.com` .   |
| Создание тома с двойным протоколом завершается ошибкой `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Эта ошибка означает, что при присоединении Active Directory к учетной записи NetApp пароль AD неверен. Обновите подключение AD, указав правильный пароль, и повторите попытку. |
| Создание тома с двойным протоколом завершается ошибкой `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Эта ошибка означает, что служба DNS недоступна. Причина может быть в том, что IP-адрес DNS неверен или возникла сетевая ошибка. Проверьте IP-адрес DNS, указанный в подключении AD, и убедитесь, что IP-адрес указан правильно. <br> Кроме того, убедитесь, что Active Directory и том находятся в одном и том же регионе и в одной виртуальной сети. Если они находятся в разных виртуальных сетей, убедитесь, что между двумя виртуальных сетей установлен пиринг между виртуальными сетями.|
| Ошибка отказа в разрешении при подключении тома с двумя протоколами. | На томе с двумя протоколами поддерживаются протоколы NFS и SMB.  При попытке доступа к подключенному тому в системе UNIX система пытается сопоставлять пользователя UNIX, который используется с пользователем Windows. Если сопоставление не найдено, возникает ошибка "отказано в разрешении". <br> Эта ситуация также возникает при использовании привилегированного пользователя для доступа. <br> Чтобы избежать проблемы с запретом разрешений, убедитесь, что Active Directory Windows включается `pcuser` перед доступом к точке подключения. При добавлении `pcuser` после возникновения проблемы "отказано в разрешении" Подождите 24 часа, чтобы запись кэша была очищена, прежде чем повторить попытку доступа. |

## <a name="next-steps"></a>Следующие шаги  

* [Создание тома с двумя протоколами](create-volumes-dual-protocol.md)
* [Настройка клиента NFS для Azure NetApp Files](configure-nfs-clients.md)
