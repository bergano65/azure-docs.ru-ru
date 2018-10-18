---
title: Создание и экспорт сертификатов для подключений "точка — сеть" в Azure с помощью Linux CLI | Документация Майкрософт
description: Создание самозаверяющего корневого сертификата, экспорт открытого ключа и создание сертификатов клиента с помощью Linux CLI и strongSwan.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305733"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>Создание и экспорт сертификатов для подключений "точка — сеть" с помощью Linux CLI и strongSwan

Для аутентификации подключений типа "точка — сеть" используются сертификаты. Эта статья поможет создать самозаверяющий корневой сертификат, а также сертификаты клиента с помощью Linux CLI и strongSwan. Если вы ищете инструкции для других сертификатов, ознакомьтесь со статьями, посвященными [PowerShell](vpn-gateway-certificates-point-to-site.md) или [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

> [!NOTE]
> Для выполнения действий, описанных в этой статье, требуется strongSwan.
>

Компьютер, используемый при выполнении шагов из этой статьи, имел следующую конфигурацию:

| | |
|---|---|
|**Компьютер**| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
|**Зависимости**| apt-get install strongswan-ikev2 strongswan-plugin-eap-tls<br>apt-get install libstrongswan-standard-plugins |

## <a name="install-strongswan"></a>Установка strongSwan

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

Сведения об установке strongSwan с помощью графического пользовательского интерфейса см. в разделе [Установка Linux](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="generate-keys-and-certificate"></a>Создание ключей и сертификатов

1. Создайте сертификат ЦС.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. Выведите на экран сертификат ЦС в формате base64. Это формат, поддерживаемый Azure. Позже этот сертификат нужно передать в Azure как часть конфигурации P2S.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. Создайте сертификат пользователя.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. Создайте пакет p12, содержащий сертификат пользователя. Этот пакет понадобится на следующих шагах при работе с [файлами конфигурации клиента](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>Дополнительная информация

Продолжайте настраивать параметры конфигурации типа "точка — сеть", чтобы [создать и установить файлы конфигурации VPN-клиента](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).