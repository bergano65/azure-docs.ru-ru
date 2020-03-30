---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066221"
---
## <a name="1-download-the-file"></a>1. Скачать файл

Выполните команды ниже. Копируйте URL-адрес результата в браузер, чтобы загрузить файл застежки-молнии профиля.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Извлеките почтовый файл

Извлеките ZIP-файл. Файл содержит следующие папки:

* AzureVPN
* Универсальный шаблон
* OpenVPN (Если вы включили настройки аутентификации OpenVPN и Azure AD на шлюзе. Для VPN Gateway [см.](../articles/vpn-gateway/openvpn-azure-ad-tenant.md) Для виртуального WAN [см. Создать арендатора - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Получение информации

В папке **AzureVPN** перейдите к файлу ***azurevpnconfig.xml*** и откройте его блокнотом. Сделать примечание текста между следующими тегами.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Подробности профиля

При добавлении соединения используйте информацию, собранную на предыдущем этапе, для страницы сведений о профиле. Поля соответствуют следующей информации:

   * **Аудитория:** Идентифицирует ресурс получателя, для который предназначен токен
   * **Эмитент:** Идентифицирует службу маркеров безопасности (STS), которая испускала маркер, а также арендатор Azure AD
   * **Арендатор:** Содержит неизменяемый, уникальный идентификатор арендатора каталога, выпустившего токен
   * **ФЗДН:** Полностью квалифицированное доменное имя (ФЗДН) на VPN-шлюзе Azure
   * **СерверСекретный:** Ключ предварительного использования ШЛюза VPN

## <a name="folder-contents"></a>Содержимое папки

* Общая **папка** содержит общедоступный сертификат сервера и файл VpnSettings.xml. Файл VpnSettings.xml содержит информацию, необходимую для настройки общего клиента.

* Загруженный почтовый файл может также содержать папки **WindowsAmd64** и **WindowsX86.** Эти папки содержат установщик для SSTP и IKEv2 для клиентов Windows. Для их установки необходимо установить права на клиента.