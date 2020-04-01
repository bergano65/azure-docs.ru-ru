---
title: Переход на OpenVPN или IKEv2 от SSTP Azure VPN Gateway
description: Эта статья поможет вам понять способы преодоления 128 одновременных ограничений соединения SSTP.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: alzam
ms.openlocfilehash: 5500d993a4bf3c664f14182d983f9abed8ebb08a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398366"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Переход на протокол OpenVPN или IKEv2 из SSTP

Подключение типа "точка — сеть" через VPN-шлюз позволяет создать безопасное подключение к виртуальной сети с отдельного клиентского компьютера. Подключение типа "точка — сеть" сначала устанавливается на клиентском компьютере. Эта статья относится к модели развертывания менеджера ресурсов и рассказывает о путях преодоления 128 одновременных ограничений соединения SSTP путем перехода к протоколу OpenVPN или IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Какой протокол используется для подключения "точка — сеть"?

В VPN-подключении "точка — сеть" может использоваться один из следующих протоколов:

* **Протокол&reg; OpenVPN**, VPN-протокол на основе SSL/TLS. Решение SSL VPN может проникать в брандмауэры, так как большинство брандмауэров открывают порт TCP 443, который использует SSL. OpenVPN можно использовать для подключения с Android, iOS (версии 11.0 и выше), Windows, Linux и Mac (версии OSX 10.13 и выше).

* **Secure Socket Tunneling Protocol (SSTP)**, собственный протокол VPN на основе SSL. Решение SSL VPN может проникать в брандмауэры, так как большинство брандмауэров открывают порт TCP 443, который использует SSL. Протокол SSTP поддерживается только на устройствах Windows. Azure поддерживает все версии Windows с протоколом SSTP (Windows 7 и более поздние версии). **SSTP поддерживает до 128 одновременных соединений только независимо от шлюза SKU.**

* IKEv2 VPN — решение VPN на основе стандартов IPsec. IKEv2 VPN можно использовать для подключения с устройств Mac (OSX версии 10.11 и выше).


>[!NOTE]
>Протоколы IKEv2 и OpenVPN для подключений типа "точка — сеть" доступны только для модели развертывания с помощью Resource Manager. Они недоступны для классической модели развертывания. Базовый шлюз SKU не поддерживает протоколы IKEv2 или OpenVPN. Если вы используете базовый SKU, вам придется удалить и воссоздать производство SKU Виртуальная сеть gateway.
>

## <a name="migrating-from-sstp-to-ikev2-or-openvpn"></a>Миграция из SSTP в IKEv2 или OpenVPN

Там могут быть случаи, когда вы хотите поддержать более 128 одновременных P2S подключение к ШЛюзу VPN, но используют SSTP. В таком случае необходимо перейти на протокол IKEv2 или OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Вариант 1 - Добавить IKEv2 в дополнение к SSTP на шлюзе

Это самый простой вариант. SSTP и IKEv2 могут сосуществовать на одном шлюзе и предоставить вам большее количество одновременных соединений. Вы можете просто включить IKEv2 на существующий шлюз и перезагрузить клиента.

Добавление IKEv2 к существующему VPN-шлюзу SSTP не повлияет на существующих клиентов, и вы можете настроить их на использование IKEv2 небольшими партиями или просто настроить новых клиентов для использования IKEv2. Если клиент Windows настроен как для SSTP, так и для IKEv2, он попытается подключиться с помощью IKEV2 сначала, а если это не удается, он вернется в SSTP.

**IKEv2 использует нестандартные порты UDP, поэтому необходимо убедиться, что эти порты не блокируются на брандмауэре пользователя. В использовании портов UDP 500 и 4500.**

Чтобы добавить IKEv2 в существующий шлюз, просто перейдите на вкладку "точка к сайту" под порталом Virtual Network Gateway и выберите **IKEv2 и SSTP (SSL)** из выпадающих коробок.

![от точки к месту](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Вариант 2 - Удалите SSTP и включите OpenVPN на шлюзе

Поскольку SSTP и OpenVPN являются протоколом на основе TLS, они не могут сосуществовать на одном шлюзе. Если вы решите перейти от SSTP к OpenVPN, вам придется отключить SSTP и включить OpenVPN на шлюзе. Эта операция приведет к потере подключения существующих клиентов к VPN-шлюзу до тех пор, пока новый профиль не будет настроен на клиента.

Вы можете включить OpenVPN вместе с IKEv2, если вы хотите. OpenVPN базируется на TLS и использует стандартный порт TCP 443. Чтобы переключиться на OpenVPN, перейдите на вкладку "точка к сайту" под порталом Virtual Network Gateway и выберите **OpenVPN (SSL)** или **IKEv2 и OpenVPN (SSL)** из выпадающей коробки.

![от точки к месту](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

После настройки шлюза существующие клиенты не смогут подключиться до тех пор, пока вы не [развернете и не наверснете клиентов OpenVPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients)

Если вы используете Windows 10, вы также можете использовать [клиент Vpn Azure для Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Вопросы и ответы
### <a name="what-are-the-client-configuration-requirements"></a>Требования к конфигурации клиента

>[!NOTE]
>Чтобы инициировать VPN-подключение с клиентского устройства Windows к Azure, необходимо иметь права администратора для клиентского устройства.
>

Для подключения "точка — сеть" пользователи используют собственные VPN-клиенты на устройствах Windows и Mac. В Azure есть ZIP-файл конфигурации VPN-клиента, содержащий параметры, необходимые для подключения к Azure с этих собственных клиентов.

* Конфигурация VPN-клиента для устройств Windows состоит из пакета установщика, который пользователи устанавливают на своих устройствах.
* Конфигурация для устройств Mac содержит файл mobileconfig, который пользователи устанавливают на своих устройствах.

ZIP-файл также содержит значения важных параметров, которые можно настроить в Azure и использовать для создания собственного профиля для этих устройств. К этим значениям относится адрес VPN-шлюза, настроенные типы туннелей, маршруты и корневой сертификат для проверки шлюза.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Какие шлюзы SKUs поддерживают P2S VPN?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Рекомендации для номера SKU шлюза см. в разделе [SKU шлюзов](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>SKU "Базовый" не поддерживает проверку подлинности IKEv2 и RADIUS.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Какие политики IKE/IPsec настроены на VPN шлюзах для P2S?


**IKEv2**

|**Шифр** | **Целостность** | **Prf** | **Группа DH** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**Ipsec**

|**Шифр** | **Целостность** | **Группа PFS** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Какие политики TLS настроены на VPN шлюзах для P2S?
**TLS**

|**Политики** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Как настроить подключение "точка — сеть"?

Для настройки подключения "точка — сеть" необходимо выполнить ряд определенных действий. Они описаны в указанных ниже статьях, которые также содержат ссылки для настройки клиентских устройств VPN.

* [Настройка подключения типа "точка — сеть" к виртуальной сети с использованием аутентификации RADIUS и PowerShell](point-to-site-how-to-radius-ps.md)

* [Настройка подключения "точка — сеть" к виртуальной сети с помощью собственной аутентификации Azure на основе сертификата и PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Настройка OpenVPN для подключения "точка — сеть" VPN-шлюза Azure (предварительная версия)](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Следующие шаги

* [Настройка подключения типа "точка — сеть" к виртуальной сети с использованием аутентификации RADIUS и PowerShell](point-to-site-how-to-radius-ps.md)

* [Настройка подключения "точка — сеть" к виртуальной сети с помощью собственной аутентификации Azure на основе сертификата и PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" является торговой маркой openVPN Inc.**
