---
title: Создание сертификата самоподписанных с пользовательским корневым CA
titleSuffix: Azure Application Gateway
description: Узнайте, как создать самоподписанный сертификат Azure Application Gateway с помощью пользовательского корневого CA
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 0447e87fd8685188af8008995ba938092f2b87fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293608"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Создание самоподписанного сертификата Azure Application Gateway с пользовательским корневым Ca

Приложение шлюз v2 SKU вводит использование доверенных корневых сертификатов, чтобы серверы бэкэнда. Это удаляет сертификаты аутентификации, которые были необходимы в V1 SKU. *Корневой сертификат* — это закодированный X.509 Базы-64(. CER) корневой сертификат формата с сервера сертификата бэкэнда. Он определяет корневой сертификат (CA), который выдал сертификат сервера, и сертификат сервера затем используется для SSL-коммуникации.

Приложение Gateway доверяет сертификату вашего сайта по умолчанию, если он подписан известным ЦС (например, GoDaddy или DigiCert). В этом случае вам не нужно явно загружать корневой сертификат. Для получения дополнительной информации смотрите [Обзор прекращения SSL и окончание sSL с помощью шлюза приложений](ssl-overview.md). Однако, если у вас есть среда dev/test и вы не хотите приобретать проверенный сертификат CA, вы можете создать свой собственный ca и создать с ним сертификат с самим подписанным. 

> [!NOTE]
> Самоподписанные сертификаты не доверяют по умолчанию, и они могут быть трудно поддерживать. Кроме того, они могут использовать устаревшие наборы хэш-шифров, которые могут быть не сильными. Для лучшей безопасности приобретите сертификат, подписанный известным сертификационным органом.

В этой статье раскрываются следующие темы:

- Создайте свой собственный сертификат
- Создание сертификата, подписанного самостоятельно, подписанного пользовательским Ca
- Загрузите самоподписанный корневой сертификат в шлюз приложения для проверки подлинности сервера бэкэнда

## <a name="prerequisites"></a>Предварительные требования

- **[OpenSSL](https://www.openssl.org/) на компьютере под управлением Windows или Linux** 

   Хотя могут быть и другие инструменты, доступные для управления сертификатами, в этом учебнике используется OpenSSL. Вы можете найти OpenSSL в комплекте со многими дистрибутивами Linux, такими как Ubuntu.
- **Веб-сервер**

   Например, Apache, IIS или NGINX для тестирования сертификатов.

- **Приложение шлюз v2 SKU**
   
  Если у вас нет существующего шлюза приложения, см. [веб-трафик Спомощьу с порталом Azure Application Gateway - Azure.](quick-create-portal.md)

## <a name="create-a-root-ca-certificate"></a>Создание корневого сертификата CA

Создайте сертификат root CA с помощью OpenSSL.

### <a name="create-the-root-key"></a>Создание корневого ключа

1. Вопийте на компьютер, где установлена OpenSSL, и запустите следующую команду. Это создает ключ, защищенный паролем.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. В запросе введите надежный пароль. Например, по крайней мере девять символов, используя верхний корпус, нижний корпус, числа и символы.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Создайте корневой сертификат и самостоятельно подпишите его

1. Используйте следующие команды для создания csr и сертификата.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Предыдущие команды создают корневой сертификат. Вы будете использовать это для подписания сертификата сервера.

1. При запросе введите пароль для корневого ключа и организационную информацию для пользовательского CA, такого как Страна, Государство, Org, OU и полностью квалифицированное доменное имя (это домен эмитента).

   ![создать корневой сертификат](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Создание серверного сертификата

Далее вы создадите сертификат сервера с помощью OpenSSL.

### <a name="create-the-certificates-key"></a>Создание ключа сертификата

Используйте следующую команду для создания ключа для сертификата сервера.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Создание КСО (Запрос на подписание сертификата)

CsR является общедоступным ключом, который дается ЦС при запросе сертификата. CA выдает сертификат для данного конкретного запроса.

> [!NOTE]
> CN (Общее имя) для серверного сертификата должен отличаться от домена эмитента. Например, в этом случае CN для `www.contoso.com` эмитента и CN сертификата сервера `www.fabrikam.com`.


1. Используйте следующую команду для создания КСО:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. При запросе введите пароль для корневого ключа и организационную информацию для пользовательского CA: Страна, Государство, Org, OU и полностью квалифицированное доменное имя. Это домен веб-сайта, и он должен отличаться от эмитента.

   ![Сертификат сервера](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Создайте сертификат с КСО и ключом и подпишите его с корневой ключом CA

1. Используйте следующую команду для создания сертификата:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Проверка вновь созданного сертификата

1. Используйте следующую команду для печати вывода файла CRT и проверки его содержимого:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Проверка сертификата](media/self-signed-certificates/verify-cert.png)

1. Проверьте файлы в каталоге и убедитесь, что у вас есть следующие файлы:

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>Настройка сертификата в настройках SSL сервера веб-сервера

На веб-сервере найди SSL с помощью файлов fabrikam.crt и fabrikam.key. Если ваш веб-сервер не может взять два файла, вы можете объединить их в один файл .pem или .pfx с помощью команд OpenSSL.

### <a name="iis"></a>IIS

Для получения инструкций о том, как импортировать сертификат и загружать их в качестве серверного сертификата на IIS, см. [КАК: Установка импортных сертификатов на веб-сервере в Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Для инструкций по связыванию SSL см. [Как настроить SSL на IIS 7.](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)

### <a name="apache"></a>Apache

Следующая конфигурация представляет собой пример [виртуального хоста, настроенного для SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) в Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

Следующая конфигурация является примером [блокировки сервера NGINX](https://nginx.org/docs/http/configuring_https_servers.html) с конфигурацией SSL:

![NGINX с SSL](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Доступ к серверу для проверки конфигурации

1. Добавьте корневой сертификат в доверенный корневой магазин вашей машины. При доступе к веб-сайту убедитесь, что вся цепочка сертификатов будет видна в браузере.

   ![Доверенные корневые сертификаты](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Предполагается, что DNS был настроен для того, чтобы указать имя веб-сервера (в данном примере www.fabrikam.com) на IP-адрес вашего веб-сервера. В противном случае можно отсватить [файл хостов](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) для устранения имени.
1. Просмотрите свой веб-сайт и нажмите значок блокировки в адресном поле браузера, чтобы проверить информацию об сайте и сертификате.

## <a name="verify-the-configuration-with-openssl"></a>Проверить конфигурацию с помощью OpenSSL

Или вы можете использовать OpenSSL для проверки сертификата.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Проверка сертификата OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Загрузите корневой сертификат в настройки HTTP прикладного шлюза

Чтобы загрузить сертификат в Application Gateway, необходимо экспортировать сертификат .crt в закодированную базу-64 формата .cer. Поскольку .crt уже содержит общедоступный ключ в закодированном формате base-64, просто переименуйте расширение файла с .crt в .cer. 

### <a name="azure-portal"></a>Портал Azure

Чтобы загрузить доверенный корневой сертификат с портала, выберите **настройки HTTP** и выберите протокол **HTTPS.**

![Добавление сертификата с помощью портала](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Для загрузки корневого сертификата можно использовать Azure CLI или Azure PowerShell. Следующим кодом является образец Azure PowerShell.

> [!NOTE]
> Следующий пример добавляет доверенный корневой сертификат в шлюз приложения, создает новую настройку HTTP и добавляет новое правило, предполагая, что пул бэкэнда и слушатель уже существуют.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that SSL handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```
### <a name="verify-the-application-gateway-backend-health"></a>Проверить работу бэкэнда шлюза приложения

1. Нажмите на вид **Backend Health** шлюза приложения, чтобы проверить, является ли зонд работоспособным.
1.    Вы должны видеть, что статус **является здоровым** для зонда HTTPS.

    ![Зонд HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о SSL-TLS в приложении шлюз, [см. Обзор sSL прекращения и от завершения до конца SSL с приложением шлюз](ssl-overview.md).

