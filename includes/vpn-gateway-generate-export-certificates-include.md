---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/10/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 89fa06dda418f328b3bc07aada49aa347e35220a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182274"
---
## <a name="rootcert"></a>Создание самозаверяющего корневого сертификата

Используйте командлет New-SelfSignedCertificate для создания самозаверяющего корневого сертификата. Дополнительные сведения о параметре см. в разделе [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. На компьютере под управлением Windows 10 или Windows Server 2016 откройте консоль Windows PowerShell с повышенными привилегиями. Эти примеры не запускаются через кнопку "Попробовать" в Azure Cloud Shell. Их нужно запустить локально.
2. Используйте следующий пример для создания самозаверяющего корневого сертификата. Следующий пример создает самозаверяющий корневой сертификат P2SRootCert, который автоматически устанавливается в папку Certificates-Current User\Personal\Certificates. Этот сертификат можно просмотреть, открыв файл *certmgr.msc* или раздел *Управление сертификатами пользователей*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>Создание сертификата клиента

На каждом клиентском компьютере, который подключается к виртуальной сети с помощью подключения типа "точка —сеть", должен быть установлен сертификат клиента. Вы можете создать сертификат клиента из самозаверяющего корневого сертификата, а затем экспортировать и установить его. Если сертификат клиента не установлен, произойдет сбой аутентификации. 

Ниже описан способ создания сертификата клиента из самозаверяющего корневого сертификата. Из одного корневого сертификата можно создать несколько сертификатов клиента. При создании сертификатов клиента с помощью приведенных ниже инструкций сертификат клиента автоматически устанавливается на компьютер, который использовался для его создания. Если вы хотите установить сертификат клиента на другой клиентский компьютер, его можно экспортировать.

В примерах используется командлет New-SelfSignedCertificate для создания сертификата клиента, срок действия которого истекает через год. Дополнительные сведения о параметре, например о задании другого значения срока действия сертификата клиента, см. в разделе [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Пример 1

Если вы не закрыли консоль PowerShell после создания самозаверяющего корневого сертификата, воспользуйтесь этим примером. Этот пример является продолжением предыдущего раздела и в нем используется переменная $cert. Если вы закрыли консоль PowerShell после создания самозаверяющего корневого сертификата или создаете дополнительные сертификаты клиента в новом сеансе консоли PowerShell, выполните инструкции, описанные в [примере 2](#ex2).

Измените и запустите пример, чтобы создать сертификат клиента. Если выполнить этот пример, не изменив его, то будет создан сертификат клиента P2SChildCert.  Если требуется указать другое имя дочернего сертификата, измените значение CN. Не изменяйте TextExtension при выполнении данного примера. Сертификат клиента, который создается, автоматически устанавливается в папку Certificates - Current User\Personal\Certificates на компьютере.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Пример 2

Если вы создаете дополнительные сертификаты клиента или не используете тот же сеанс PowerShell, в котором был создан самозаверяющий корневой сертификат, выполните следующее.

1. Определите самозаверяющий корневой сертификат, установленный на компьютере. Этот командлет возвращает список сертификатов, установленных на компьютере.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. Найдите имя субъекта в полученном списке, а затем скопируйте отпечаток, расположенный рядом с ним, в текстовый файл. В следующем примере указано два сертификата. CN-имя — это имя самозаверяющего корневого сертификата, на основе которого требуется создать дочерний сертификат. В данном случае это P2SRootCert.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Объявите переменную для корневого сертификата, используя отпечаток из предыдущего шага. Замените THUMBPRINT отпечатком корневого сертификата, на основе которого требуется создать дочерний сертификат.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Например, если использовать отпечаток для P2SRootCert из предыдущего шага, то переменная будет выглядеть следующим образом.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Измените и запустите пример, чтобы создать сертификат клиента. Если выполнить этот пример, не изменив его, то будет создан сертификат клиента P2SChildCert. Если требуется указать другое имя дочернего сертификата, измените значение CN. Не изменяйте TextExtension при выполнении данного примера. Сертификат клиента, который создается, автоматически устанавливается в папку Certificates - Current User\Personal\Certificates на компьютере.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>Экспорт открытого ключа корневого сертификата (CER)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Экспорт самозаверяющего корневого сертификата и закрытого ключа для его сохранения (необязательно)

Может возникнуть необходимость экспортировать самозаверяющий корневой сертификат и сохранить его как резервную копию в надежном месте. При необходимости позже можно будет установить его на другом компьютере и создать дополнительные сертификаты клиента. Чтобы экспортировать самозаверяющий корневой сертификат в формате PFX, выберите корневой сертификат и выполните те же действия, что описаны в разделе [Экспорт сертификата клиента](#clientexport).

## <a name="clientexport"></a>Экспорт сертификата клиента

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
