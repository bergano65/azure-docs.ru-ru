---
title: Включить имя файла
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80066221"
---
## <a name="1-download-the-file"></a>1. Скачайте файл

Выполните команды ниже. Скопируйте полученный URL-адрес в браузер, чтобы скачать его.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. извлечение ZIP-файла

Извлеките ZIP-файл. Файл содержит следующие папки:

* AzureVPN
* Универсальный шаблон
* Опенвпн (если вы включили параметры проверки подлинности Опенвпн и Azure AD на шлюзе. Сведения о VPN-шлюзе см. [в разделе Создание клиента](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). Сведения о виртуальной глобальной сети см. [в разделе Создание клиента-вван](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Получение сведений

В папке **AzureVPN** перейдите к файлу ***азуревпнконфиг. XML*** и откройте его в блокноте. Запишите текст между следующими тегами.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Сведения о профиле

При добавлении подключения используйте сведения, собранные на предыдущем шаге, на странице сведения о профиле. Поля соответствуют следующим сведениям:

   * **Аудитория:** Определяет ресурс получателя, для которого предназначен маркер
   * **Издатель:** Определяет службу маркеров безопасности (STS), которая выдает маркер, а также клиент Azure AD.
   * **Клиент:** Содержит неизменяемый уникальный идентификатор клиента каталога, который выдал токен.
   * **Полное доменное имя:** Полное доменное имя (FQDN) VPN-шлюза Azure;
   * **Серверсекрет:** Общий ключ VPN-шлюза

## <a name="folder-contents"></a>Содержимое папки

* **Общая папка** содержит общедоступный сертификат сервера и файл файл vpnsettings. XML. Файл файл vpnsettings. XML содержит сведения, необходимые для настройки универсального клиента.

* Скачанный ZIP-файл также может содержать папки **WindowsAmd64** и **WindowsX86** . Эти папки содержат установщик для клиентов SSTP и IKEv2 для Windows. Для их установки требуются права администратора на клиенте.