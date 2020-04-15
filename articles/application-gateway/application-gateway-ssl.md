---
title: Разгрузка TLS с помощью PowerShell - Шлюз приложений Azure
description: В этой статье содержатся инструкции по созданию шлюза приложения с разгрузкой TLS с помощью классической модели развертывания Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 2ead16b61784b8073d50b7e0e6079805a1e48e9b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312325"
---
# <a name="configure-an-application-gateway-for-tls-offload-by-using-the-classic-deployment-model"></a>Настройка шлюза приложения для разгрузки TLS с помощью классической модели развертывания

> [!div class="op_single_selector"]
> * [Портал Azure](application-gateway-ssl-portal.md)
> * [PowerShell и диспетчер ресурсов Azure](application-gateway-ssl-arm.md)
> * [Классическая модель — Azure PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Шлюз приложений Azure можно настроить для завершения сеанса безопасности уровня транспорта (TLS), ранее известного как Secure Sockets Layer (SSL), чтобы избежать дорогостоящих задач по расшифровке TLS на веб-ферме. Разгрузка TLS также упрощает настройку сервера и управление веб-приложением.

## <a name="before-you-begin"></a>Перед началом

1. Установите последнюю версию командлетов Azure PowerShell, используя установщик веб-платформы. Скачать и установить последнюю версию вы можете в разделе **Windows PowerShell** на [странице загрузок](https://azure.microsoft.com/downloads/).
2. Убедитесь, что у вас есть рабочая виртуальная сеть с действительной подсетью. Убедитесь, что подсеть не используется виртуальной машиной или облачным развертыванием. Сам шлюз приложений должен находиться в подсети виртуальной сети.
3. Для использования шлюза приложений настраиваются имеющиеся серверы или серверы, для которых в виртуальной сети созданы конечные точки либо же назначен общедоступный или виртуальный IP-адрес.

Для настройки разгрузки TLS на шлюзе приложения выполните следующие шаги в перечисленном порядке:

1. [Создание шлюза приложения](#create-an-application-gateway)
2. [Загрузка сертификатов TLS/SSL](#upload-tlsssl-certificates)
3. [Настройка шлюза](#configure-the-gateway)
4. [Настройка конфигурации шлюза](#set-the-gateway-configuration)
5. [Запуск шлюза](#start-the-gateway)
6. [Проверка состояния шлюза](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Создание шлюза приложений

Для создания шлюза введите командлет `New-AzureApplicationGateway`, подставив в него свои значения. Выставление счетов для шлюза начинается не на данном этапе, а позднее, после успешного запуска шлюза.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Чтобы проверить создание шлюза, введите командлет `Get-AzureApplicationGateway`.

В примере параметры **Description** (Описание), **InstanceCount** (Количество экземпляров) и **GatewaySize** (Размер шлюза) являются необязательными. Значение параметра **InstanceCount** по умолчанию — **2** (максимальное значение — **10**). Значение по умолчанию для **GatewaySize** является **средним.** Также доступны значения Small (Маленький) и Large (Большой). Параметры **VirtualIPs** и **DnsName** отображаются без значений, так как шлюз еще не запущен. Эти значения будут определены после запуска шлюза.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-tlsssl-certificates"></a>Загрузка сертификатов TLS/SSL

Чтобы загрузить на шлюз приложений сертификаты сервера в формате PFX, введите командлет `Add-AzureApplicationGatewaySslCertificate`. Имя сертификата выбирается пользователем и должно быть уникальным в пределах шлюза приложений. В операциях управления сертификатами в шлюзе приложений сертификат указывается по имени.

В следующем примере приведен командлет. Замените значения в нем собственными.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Затем проверьте загрузку сертификата. Введите командлет `Get-AzureApplicationGatewayCertificate`.

В следующем примере командлет показан в первой строке, а за ним следуют выходные данные.

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> Пароль сертификата должен содержать 4–12 символов, состоящих из букв или цифр. Специальные знаки не допускаются.

## <a name="configure-the-gateway"></a>Настройка шлюза

Конфигурация шлюза приложений состоит из нескольких значений. Значения конфигурации могут быть взаимосвязаны.

Значения качества производительности:

* **Пул серверов бэк-энда**: Список IP-адресов серверов бэк-энда. Указанные IP-адреса должны относиться к подсети виртуальной сети либо представлять собой общедоступные или виртуальные IP-адреса.
* **Параметры внутреннего пула серверов**. Каждый пул имеет такие параметры, как порт, протокол и сходство на основе файлов cookie. Эти параметры привязываются к пулу и применяются ко всем серверам в этом пуле.
* **Передний порт**: Этот порт является общественным портом, который открыт на шлюзе приложения. Трафик поступает на этот порт, а затем перенаправляется на один из тыловых серверов.
* **Слушатель**: Слушатель имеет передний порт, протокол (http или Https; эти значения чувствительны к случаям), и имя сертификата TLS/SSL (если настройка разгрузки TLS).
* **Правило**. Правило связывает прослушиватель и внутренний пул серверов, а также определяет, в какой внутренний пул серверов следует направлять трафик, поступающий на определенный прослушиватель. В настоящее время поддерживается только *основное* правило. *Основное* правило предусматривает циклическое распределение нагрузки.

**Дополнительные примечания по настройке конфигурации**

Для конфигурации сертификатов TLS/SSL протокол в **httpListener** должен быть изменяться на **Https** (дело чувствительное). Добавьте элемент **SslCert** в **HttpListener** с тем же именем, используемым в разделе [сертификатов Upload TLS/SSL.](#upload-tlsssl-certificates) Передний порт должен быть обновлен до **443**.

**Включение сходства на основе файлов cookie**. Шлюз приложений можно настроить так, чтобы запросы от клиентского сеанса всегда направлялись на одну виртуальную машину в веб-ферме. Для этого необходимо вставить файлы cookie сеанса. Таким образом, шлюз сможет перенаправлять трафик соответствующим образом. Чтобы включить сходство на основе файлов cookie, присвойте параметру **CookieBasedAffinity** в элементе **BackendHttpSettings** значение **Enabled**.

Настроить конфигурацию можно либо путем создания объекта конфигурации, либо с помощью XML-файла конфигурации.
Чтобы создать конфигурацию с помощью XML-файла конфигурации, введите следующий пример.


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Настройка конфигурации шлюза

Теперь необходимо настроить шлюз приложений. Для этого можно ввести командлет `Set-AzureApplicationGatewayConfig` либо с объектом конфигурации, либо с XML-файлом конфигурации.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Запуск шлюза

Настроенный шлюз можно запустить с помощью командлета `Start-AzureApplicationGateway`. Выставление счетов для шлюза приложений начинается после запуска шлюза.

> [!NOTE]
> Выполнение командлета `Start-AzureApplicationGateway` может занять 15–20 минут.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Проверка состояния шлюза

Проверить состояние шлюза можно с помощью командлета `Get-AzureApplicationGateway`. Если на предыдущем этапе командлет `Start-AzureApplicationGateway` выполнен успешно, у параметра **State** должно быть значение **Running**, а у параметров **VirtualIPs** и **DnsName** — допустимые записи.

В данном примере показан рабочий шлюз приложений, готовый к приему трафика.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о параметрах балансировки нагрузки в целом см. в статьях:

* [Балансизатор загрузки Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Менеджер трафика Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
