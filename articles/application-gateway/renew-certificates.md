---
title: Обновление сертификата шлюза приложений Azure
description: Узнайте, как обновить сертификат, связанный с прослушивателем шлюза приложений.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: c7c27f00b9f8b4fdcd8f735f842edb8f66803c6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277999"
---
# <a name="renew-application-gateway-certificates"></a>Обновление сертификатов шлюза приложений

Если вы настроили в шлюзе приложений SSL-шифрование, рано или поздно вам придется обновить сертификаты.

Сертификат, связанный с прослушивателем, можно обновить с помощью портала Azure, Azure PowerShell или Azure CLI:

## <a name="azure-portal"></a>Портал Azure

Чтобы обновить сертификат прослушивателя на портале, перейдите к прослушивателям шлюза приложений. Щелкните прослушиватель, сертификат которого необходимо обновить, и выберите **Продлить или изменить выбранный сертификат**.

![Продление сертификата](media/renew-certificate/ssl-cert.png)

Передайте новый сертификат в формате PFX, присвойте ему имя, введите пароль и нажмите кнопку **Сохранить**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы обновить сертификат с помощью Azure PowerShell, используйте следующий сценарий:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Дальнейшие действия

Сведения по настройке разгрузки SSL с использованием шлюза приложений Azure см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью портала](application-gateway-ssl-portal.md)
