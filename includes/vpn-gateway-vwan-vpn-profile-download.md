---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/13/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1d14ee849c89e6c3807636d0a728157abd9de97a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650649"
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
* OpenVPN (если вы включили параметры проверки подлинности OpenVPN и Azure AD на шлюзе). Сведения о VPN-шлюзе см. в разделе [Создание клиента](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Сведения о виртуальной глобальной сети см. в разделе [Создание клиента — виртуальная глобальная сеть](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

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
