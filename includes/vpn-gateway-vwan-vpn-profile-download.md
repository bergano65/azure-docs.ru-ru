---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/31/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fc2393cfe87e2639ce40e66e6053d4d430518719
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87515351"
---
## <a name="1-download-the-file"></a>1. Скачивание файла

Выполните команды ниже. Скопируйте полученный URL-адрес в браузер, чтобы скачать ZIP-файл профиля.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Извлечение ZIP-файла

Извлеките ZIP-файл. Файл содержит следующие папки.

* AzureVPN
* Универсальный шаблон
* Опенвпн (если вы включили параметр Опенвпн с **сертификатом Azure** или параметрами **проверки подлинности RADIUS** на шлюзе). Сведения о VPN-шлюзе см. в разделе [Создание клиента](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Сведения о виртуальной глобальной сети см. в разделе [Создание клиента — виртуальная глобальная сеть](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Извлечение информации

В папке **AzureVPN** перейдите к файлу ***azurevpnconfig.xml*** и откройте его в "Блокноте". Запишите текст между следующими тегами.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Сведения о профиле

При добавлении подключения используйте сведения, собранные на предыдущем шаге, для страницы сведений о профиле. Поля соответствуют следующим сведениям.

   * **Audience:** идентифицирует ресурс получателя, для которого предназначен токен.
   * **Issuer:** идентифицирует службу токенов безопасности (STS), которая выдала токен, а также клиент Azure AD.
   * **Tenant:** содержит неизменяемый уникальный идентификатор клиента каталога, который выдал токен.
   * **FQDN:** полное доменное имя (FQDN) VPN-шлюза Azure.
   * **ServerSecret:** общий ключ VPN-шлюза.

## <a name="folder-contents"></a>Содержимое папки

* **Универсальная папка** содержит общедоступный сертификат сервера и файл VpnSettings.xml. Файл VpnSettings.xml содержит сведения, необходимые для настройки универсального клиента.

* Скачанный ZIP-файл также может содержать папки **WindowsAmd64** и **WindowsX86**. Эти папки содержат установщик для SSTP и IKEv2 для клиентов Windows. Для их установки требуются права администратора на клиенте.
