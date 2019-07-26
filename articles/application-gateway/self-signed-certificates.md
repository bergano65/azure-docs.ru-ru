---
title: Создание самозаверяющего сертификата шлюза приложений Azure с помощью пользовательского корневого ЦС
description: Узнайте, как создать самозаверяющий сертификат шлюза приложений Azure с помощью пользовательского корневого ЦС.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 0b97f2f6df87255e10faaf58c40ea9136354bff6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386308"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Создание самозаверяющего сертификата шлюза приложений Azure с помощью пользовательского корневого ЦС

В SKU шлюза приложений v2 используется Доверенные корневые сертификаты для поддержки внутренних серверов. Это приведет к удалению сертификатов проверки подлинности, которые были необходимы в номере SKU версии 1. *Корневой сертификат* является Base-64 в кодировке X. 509 (. CER). отформатируйте корневой сертификат на сервере серверных сертификатов. Он определяет корневой центр сертификации (ЦС), выдавший сертификат сервера, а затем использует сертификат сервера для SSL-соединения.

Шлюз приложений доверяет сертификату вашего веб-сайта по умолчанию, если он подписан известным центром сертификации (например, GoDaddy или DigiCert). В этом случае вам не нужно явно загружать корневой сертификат. Дополнительные сведения см. в разделе Общие сведения о [завершении SSL и сквозном использовании SSL с помощью шлюза приложений](ssl-overview.md). Однако если у вас есть среда разработки и тестирования и вы не хотите приобретать проверенный сертификат, подписанный центром сертификации, можно создать собственный пользовательский ЦС и создать самозаверяющий сертификат. 

> [!NOTE]
> Самозаверяющие сертификаты не являются доверенными по умолчанию и могут быть сложными в обслуживании. Кроме того, они могут использовать устаревшие пакеты хэширования и шифра, которые могут быть не надежными. Для повышения безопасности приобретите сертификат, подписанный хорошо известным центром сертификации.

В этой статье раскрываются следующие темы:

- Создание собственного пользовательского центра сертификации
- Создание самозаверяющего сертификата, подписанного пользовательским центром сертификации
- Отправка самозаверяющего корневого сертификата в шлюз приложений для проверки подлинности внутреннего сервера

## <a name="prerequisites"></a>Предварительные требования

- **[OpenSSL](https://www.openssl.org/) на компьютере под управлением Windows или Linux** 

   Хотя для управления сертификатами могут быть доступны и другие средства, в этом руководстве используется OpenSSL. Вы можете найти OpenSSL с несколькими дистрибутивами Linux, например Ubuntu.
- **Веб-сервер**

   Например, Apache, IIS или NGINX для тестирования сертификатов.

- **SKU шлюза приложений версии 2**
   
  Если у вас нет шлюза приложений, см. [раздел Краткое руководство. Направление веб-трафика с помощью Шлюза приложений Azure на портале Azure](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Создание сертификата корневого ЦС

Создайте сертификат корневого ЦС с помощью OpenSSL.

### <a name="create-the-root-key"></a>Создание корневого ключа

1. Войдите на компьютер, где установлен OpenSSL, и выполните следующую команду. При этом создается ключ, защищенный паролем.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. В командной строке введите надежный пароль. Например, по крайней мере девять символов с прописными буквами, строчными буквами, числами и символами.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Создание корневого сертификата и его самостоятельный вход

1. Используйте следующие команды для создания CSR и сертификата.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Предыдущие команды создают корневой сертификат. Вы будете использовать его для подписи сертификата сервера.

1. При появлении запроса введите пароль для корневого ключа и сведения о организации для пользовательского ЦС, например Country, штат, org, OU и полное доменное имя (это домен издателя).

   ![создать корневой сертификат](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Создание сертификата сервера

Далее предстоит создать сертификат сервера с помощью OpenSSL.

### <a name="create-the-certificates-key"></a>Создание ключа сертификата

Используйте следующую команду, чтобы создать ключ для сертификата сервера.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Создание CSR (запрос подписи сертификата)

CSR — это открытый ключ, предоставляемый ЦС при запросе сертификата. ЦС выдает сертификат для этого конкретного запроса.

> [!NOTE]
> CN (общее имя) для сертификата сервера должно отличаться от имени домена издателя. Например, в этом случае CN-имя издателя — www.contoso.com, а www.fabrikam.com сертификата сервера —.


1. Чтобы создать CSR, используйте следующую команду:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. При появлении запроса введите пароль для корневого ключа и сведения о организации для пользовательского ЦС. Country, штат, org, OU и полное доменное имя. Это домен веб-сайта, который должен отличаться от издателя.

   ![Сертификат сервера](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Создайте сертификат с CSR и ключом и подпишите его с помощью корневого ключа ЦС.

1. Чтобы создать сертификат, используйте следующую команду:

   ```
   openssl x509 -req -in fabrikam.csr -CA public.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Проверка только что созданного сертификата

1. Используйте следующую команду, чтобы напечатать выходные данные CRT-файла и проверить его содержимое:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Проверка сертификата](media/self-signed-certificates/verify-cert.png)

1. Проверьте файлы в каталоге и убедитесь, что у вас есть следующие файлы:

   - Contoso. CRT
   - Contoso. key
   - Fabrikam. CRT
   - Fabrikam. key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>Настройка сертификата в параметрах SSL веб-сервера

На веб-сервере настройте SSL с помощью файлов Fabrikam. CRT и Fabrikam. key. Если веб-сервер не может принимать два файла, их можно объединить в один файл PEM или PFX с помощью команд OpenSSL.

### <a name="iis"></a>IIS

Инструкции по импорту сертификата и его передаче в качестве сертификата сервера в IIS см. в [разделе как Установите импортированные сертификаты на веб-сервер в Windows Server](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)2003.

Инструкции по привязке SSL см. в разделе [Настройка SSL в IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

Ниже приведен пример [виртуального узла, настроенного для SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) в Apache:

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

Ниже приведен пример [nginx Server Block](http://nginx.org/docs/http/configuring_https_servers.html) с конфигурацией SSL:

![NGINX с SSL](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Доступ к серверу для проверки конфигурации

1. Добавьте корневой сертификат в доверенное корневое хранилище компьютера. При доступе к веб-сайту убедитесь, что в браузере отображается вся цепочка сертификатов.

   ![Доверенные корневые сертификаты](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Предполагается, что DNS настроена на указание имени веб-сервера (в данном примере — www.fabrikam.com) на IP-адрес вашего сервера. В противном случае можно изменить [файл hosts](https://answers.microsoft.com/windows/forum/windows_10-other_settings-winpc/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) , чтобы разрешить имя.
1. Перейдите на свой веб-сайт и щелкните значок замка в поле адреса вашего браузера, чтобы проверить сведения о сайте и сертификате.

## <a name="verify-the-configuration-with-openssl"></a>Проверка конфигурации с помощью OpenSSL

Для проверки сертификата можно также использовать OpenSSL.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Проверка сертификата OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Отправка корневого сертификата в параметры HTTP шлюза приложений

Чтобы отправить сертификат в шлюзе приложений, необходимо экспортировать CRT-сертификат в формате CER с кодировкой 64. Поскольку CRT уже содержит открытый ключ в формате Base-64, просто Переименуйте расширение файла с CRT на CER. 

### <a name="azure-portal"></a>портала Azure

Чтобы отправить доверенный корневой сертификат с портала, выберите **Параметры HTTP** и выберите протокол **HTTPS** .

![Добавление сертификата с помощью портала](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Для отправки корневого сертификата можно также использовать Azure CLI или Azure PowerShell. Следующий код является Azure PowerShell образцом.

> [!NOTE]
> В следующем примере в шлюз приложений добавляется доверенный корневой сертификат, создается новый параметр HTTP и добавляется новое правило, предполагая, что серверный пул и прослушиватель уже существуют.

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

## Add the configuration to the HTTP Setting and don’t forget to set the “hostname” field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server’s certificate. Note that SSL handshake will
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
### <a name="verify-the-application-gateway-backend-health"></a>Проверка работоспособности серверной части шлюза приложений

1. Щелкните представление **работоспособность серверной** части шлюза приложений, чтобы проверить работоспособность пробы.
1.  Должно отобразиться состояние " **Исправен** " для проверки HTTPS.

    ![Проверка HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о ССЛ\ТЛС в шлюзе приложений см. в разделе [Общие сведения о завершении работы SSL и сквозном использовании SSL с помощью шлюза приложений](ssl-overview.md).

