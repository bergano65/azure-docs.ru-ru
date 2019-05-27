---
title: Обновление сертификата шлюза приложений Azure
description: Узнайте, как обновить сертификат, связанный с прослушивателем шлюза приложений.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
origin.date: 08/15/2018
ms.date: 03/12/2019
ms.author: v-junlch
ms.openlocfilehash: 90200f7be6c71346441922365fc4439111dd8701
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66133706"
---
# <a name="renew-application-gateway-certificates"></a>Обновление сертификатов шлюза приложений

Если вы настроили в шлюзе приложений SSL-шифрование, рано или поздно вам придется обновить сертификаты.

Сертификат, связанный с прослушивателем, можно обновить с помощью портала Azure, Azure PowerShell или Azure CLI:

## <a name="azure-portal"></a>Портал Azure

Чтобы обновить сертификат прослушивателя на портале, перейдите к прослушивателям шлюза приложений. Щелкните прослушиватель, сертификат которого необходимо обновить, и выберите **Продлить или изменить выбранный сертификат**.

![Обновление сертификата](./media/renew-certificate/ssl-cert.png)

Передайте новый сертификат в формате PFX, присвойте ему имя, введите пароль и нажмите кнопку **Сохранить**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Чтобы обновить сертификат с помощью Azure PowerShell, используйте следующий сценарий:

```azurepowershell
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
## <a name="azure-cli"></a>Инфраструктура CLI Azure

```azurecli
az network application-gateway ssl-cert update `
  -n "<CertName>" `
  --gateway-name "<AppGatewayName>" `
  -g "ResourceGroupName>" `
  --cert-file <PathToCerFile> `
  --cert-password "<password>"
```

## <a name="next-steps"></a>Дальнейшие действия

Сведения по настройке разгрузки SSL с использованием шлюза приложений Azure см. в статье [Настройка шлюза приложений для разгрузки SSL с помощью портала](application-gateway-ssl-portal.md)

<!-- Update_Description: code update -->