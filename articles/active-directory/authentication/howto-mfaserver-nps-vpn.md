---
title: Azure MFA Server и сторонние VPN - Активный каталог Azure
description: Пошаговая конфигурация руководства azure MFA Server для интеграции с Cisco, Citrix и Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdb24bcd79f1766a52f290fd6fe0e6e5bf17e7c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74847955"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Расширенные сценарии с Azure MFA Server и сторонними VPN-решениями

Многофакторный сервер аутентификации Azure (Azure MFA Server) может использоваться для беспрепятственного подключения к различным сторонним VPN-решениям. Эта статья посвящена применению устройств VPN Cisco® ASA, Citrix NetScaler SSL, а также Juniper Networks Secure Access/Pulse Secure Connect Secure SSL. Мы разработали руководства по настройке для этих трех распространенных устройств. Azure MFA Server также может интегрироваться с большинством других систем, которые используют RADIUS, LDAP, IIS или проверку подлинности на основе утверждений в AD FS. Более подробную информацию можно найти в [конфигурациях Azure MFA Server.](howto-mfaserver-deploy.md#next-steps)

> [!IMPORTANT]
> С 1 июля 2019 года Microsoft больше не будет предлагать MFA Server для новых развертываний. Новые клиенты, которые хотели бы требовать многофакторной аутентификации от своих пользователей, должны использовать многофакторную аутентификацию Azure. Существующие клиенты, которые активировали MFA Server до 1 июля, смогут загружать последнюю версию, будущие обновления и генерировать учетные данные активации в обычном режиме.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>VPN-прибор Cisco ASA и сервер Azure MFA
Azure MFA Server интегрируется с вашим VPN-прибором Cisco® ASA, чтобы обеспечить дополнительную безопасность для входа в VPN Cisco AnyConnect® VPN и доступа к порталу.  Вы можете использовать протокол LDAP или RADIUS.  Щелкните одну из следующих ссылок и загрузите подробную пошаговую инструкцию по настройке.

| Руководство по настройке | Описание |
| --- | --- |
| [Настройка Cisco ASA с конфигурацией Anyconnect VPN и многофакторной проверки подлинности Azure для LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Интеграция устройства VPN Cisco ASA с Azure MFA с помощью LDAP |
| [Настройка Cisco ASA с конфигурацией Anyconnect VPN и многофакторной проверки подлинности Azure для RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Интеграция устройства VPN Cisco ASA с Azure MFA с помощью RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN и Azure MFA Server
Azure MFA Server интегрируется с вашим citrix NetScaler SSL VPN, чтобы обеспечить дополнительную безопасность для логинов vpn Citrix NetScaler SSL и доступа к порталу.  Вы можете использовать протокол LDAP или RADIUS.  Щелкните одну из следующих ссылок и загрузите подробную пошаговую инструкцию по настройке.

| Руководство по настройке | Описание |
| --- | --- |
| [Настройка VPN Citrix NetScaler SSL и многофакторной проверки подлинности Azure для LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Интеграция устройства VPN Citrix NetScaler SSL с Azure MFA с помощью LDAP |
| [Настройка VPN Citrix NetScaler SSL и многофакторной проверки подлинности Azure для RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Интеграция устройства VPN Citrix NetScaler SSL с Azure MFA с помощью RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Безопасный VPN-прибор SSL и сервер Azure MFA
Azure MFA Server интегрируется с вашим iJuniper/Pulse Secure SSL VPN, чтобы обеспечить дополнительную безопасность для входа в VPN Juniper/Pulse Secure SSL и доступа к порталу.  Вы можете использовать протокол LDAP или RADIUS.  Щелкните одну из следующих ссылок и загрузите подробную пошаговую инструкцию по настройке.

| Руководство по настройке | Описание |
| --- | --- |
| [Настройка VPN Juniper/Pulse Secure SSL и многофакторной проверки подлинности Azure для LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Интеграция устройства VPN Juniper/Pulse Secure SSL с Azure MFA с помощью LDAP |
| [Настройка VPN Juniper/Pulse Secure SSL и многофакторной проверки подлинности Azure для RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Интеграция устройства VPN Juniper/Pulse Secure SSL с Azure MFA с помощью RADIUS |

## <a name="next-steps"></a>Дальнейшие действия

- [Внедрение расширения NPS для Многофакторной идентификации Azure в существующую инфраструктуру проверки подлинности.](howto-mfa-nps-extension.md)

- [Настройка параметров многофакторной аутентификации Azure](howto-mfa-mfasettings.md)
