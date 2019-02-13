---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/16/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c6f9065786879749eee6187e93283f4c026b7fff
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55568810"
---
В указанных ниже шагах использовалась следующая конфигурация компьютера:

  | | |
  |---|---|
  |Компьютер| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
  |Зависимости| strongSwan |

#### <a name="1-install-strongswan"></a>1. Установка strongSwan

Чтобы установить необходимую конфигурацию strongSwan, используйте следующие команды:

```
apt-get install strongswan-ikev2 strongswan-plugin-eap-tls
```

```
apt-get install libstrongswan-standard-plugins
```

```
apt-get install strongswan-pki
```

#### <a name="2-generate-keys-and-certificate"></a>2. Создание ключей и сертификатов

Создайте сертификат ЦС.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Выведите на экран сертификат ЦС в формате base64. Это формат, поддерживаемый Azure. Позже этот сертификат нужно передать в Azure как часть конфигурации P2S.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Создайте сертификат пользователя.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Создайте пакет p12, содержащий сертификат пользователя. Этот пакет понадобится на следующих шагах при работе с файлами конфигурации клиента.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```