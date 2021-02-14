---
title: Устранение неполадок с SMB или двумя протоколами для Azure NetApp Files | Документация Майкрософт
description: Описание сообщений об ошибках и способы их устранения, которые могут помочь при устранении неполадок с SMB или двумя протоколами для Azure NetApp Files.
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
ms.date: 02/02/2021
ms.author: b-juche
ms.openlocfilehash: 237fb514229f370fcba79133232e80a6a655048f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104422"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>Устранение неполадок в томах с протоколом SMB или с двумя протоколами

В этой статье описываются способы устранения ошибок, которые могут возникнуть при создании томов с двумя протоколами или управлении ими.

## <a name="errors-for-dual-protocol-volumes"></a>Ошибки для томов с двумя протоколами

|     Условия ошибок    |     Способы устранения    |
|-|-|
| Протокол LDAP через TLS включен, а создание тома с двойным протоколом завершается ошибкой `This Active Directory has no Server root CA Certificate` .    |     Если эта ошибка возникает при создании тома с двумя протоколами, убедитесь, что сертификат корневого ЦС отправлен в учетную запись NetApp.    |
| Создание тома с двойным протоколом завершается ошибкой `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  На DNS-сервере может отсутствовать запись указателя (PTR) компьютера узла Active Directory. Необходимо создать зону обратного просмотра на DNS-сервере, а затем добавить запись PTR для компьютера узла Active Directory в эту зону обратного просмотра. <br> Например, предположим, что IP-адрес компьютера, на котором размещается компьютер, — это `10.X.X.X` , имя узла (как было найдено с помощью `hostname` команды) `AD1` , а доменным именем является `contoso.com` .  Запись типа PTR, добавленная в зону обратного просмотра, должна иметь значение `10.X.X.X`  ->  `contoso.com` .   |
| Создание тома с двойным протоколом завершается ошибкой `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.X.X.X, port 88 using TCP\\n**[ 950] FAILURE` . |     Эта ошибка означает, что при присоединении Active Directory к учетной записи NetApp пароль AD неверен. Обновите подключение AD, указав правильный пароль, и повторите попытку. |
| Создание тома с двойным протоколом завершается ошибкой `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Эта ошибка означает, что служба DNS недоступна. Причина может быть в том, что IP-адрес DNS неверен или возникла сетевая ошибка. Проверьте IP-адрес DNS, указанный в подключении AD, и убедитесь, что IP-адрес указан правильно. <br> Кроме того, убедитесь, что Active Directory и том находятся в одном и том же регионе и в одной виртуальной сети. Если они находятся в разных виртуальных сетей, убедитесь, что между двумя виртуальных сетей установлен пиринг между виртуальными сетями.|
| Ошибка отказа в разрешении при подключении тома с двумя протоколами. | На томе с двумя протоколами поддерживаются протоколы NFS и SMB.  При попытке доступа к подключенному тому в системе UNIX система пытается сопоставлять пользователя UNIX, который используется с пользователем Windows. Если сопоставление не найдено, возникает ошибка "отказано в разрешении". <br> Эта ситуация также возникает при использовании привилегированного пользователя для доступа. <br> Чтобы избежать проблемы с запретом разрешений, убедитесь, что Active Directory Windows включается `pcuser` перед доступом к точке подключения. При добавлении `pcuser` после возникновения проблемы "отказано в разрешении" Подождите 24 часа, чтобы запись кэша была очищена, прежде чем повторить попытку доступа. |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>Распространенные ошибки для томов с протоколом SMB и двумя протоколами

|     Условия ошибок    |     Способы устранения    |
|-|-|
| Сбой создания тома SMB или Dual-Protocol со следующей ошибкой: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | Эта ошибка означает, что служба DNS недоступна. <br> Рассмотрите следующие решения. <ul><li>Убедитесь, что добавление и том развертываются в одном регионе.</li> <li>Убедитесь, что в Добавление и том используют одну виртуальную сеть. Если они используют разные виртуальных сетей, убедитесь, что виртуальных сетей соединены друг с другом. См. раздел [рекомендации по Azure NetApp Files планировании сети](azure-netapp-files-network-topologies.md). </li> <li>К DNS-серверу могут быть применены группы безопасности сети (группы безопасности сети).  Таким образом, он не разрешает поток трафика. В этом случае откройте группы безопасности сети для DNS или AD, чтобы подключиться к различным портам. Требования к портам см. в разделе [требования для Active Directory подключений](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). </li></ul> <br>Те же решения применимы и к ДОБАВЛЕНию Azure. Добавление Azure должно быть развернуто в одном регионе. Виртуальная сеть должна находиться в том же регионе или в одноранговой сети, что и виртуальная сеть, используемая томом. | 
| Сбой создания тома SMB или Dual-Protocol со следующей ошибкой: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>Убедитесь, что имя пользователя указано правильно. </li> <li>Убедитесь, что пользователь входит в группу администраторов, имеющую право на создание учетных записей компьютеров. </li> <li> Если вы используете Azure ADDS, убедитесь, что пользователь входит в группу Azure AD `Azure AD DC Administrators` . </li></ul> | 
| Сбой создания тома SMB или Dual-Protocol со следующей ошибкой: <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.X.X.X, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | Убедитесь, что пароль, указанный для соединения с AD, указан правильно. |
| Сбой создания тома SMB или Dual-Protocol со следующей ошибкой: `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.X.X.X, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | Убедитесь в правильности пути подразделения, указанного для присоединения к Active Directory. Если вы используете Azure ADDS, убедитесь, что путь к подразделению — `OU=AADDC Computers` . |

## <a name="next-steps"></a>Следующие шаги  

* [Создание тома SMB](azure-netapp-files-create-volumes-smb.md)
* [Создание тома с двумя протоколами](create-volumes-dual-protocol.md)
* [Настройка клиента NFS для Azure NetApp Files](configure-nfs-clients.md)
