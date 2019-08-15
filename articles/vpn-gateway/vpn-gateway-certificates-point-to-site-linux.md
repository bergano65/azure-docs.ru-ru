---
title: 'Создание и экспорт сертификатов для подключений "точка — сеть" — Linux: CLI: Azure | Документация Майкрософт'
description: Создание самозаверяющего корневого сертификата, экспорт открытого ключа и создание сертификатов клиента с помощью Linux CLI и strongSwan.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: 2544df920580745e42aee1fc5e681d40bd1e74f9
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036012"
---
# <a name="generate-and-export-certificates"></a>Создание и экспорт сертификатов

Для аутентификации подключений типа "точка — сеть" используются сертификаты. Эта статья поможет создать самозаверяющий корневой сертификат, а также сертификаты клиента с помощью Linux CLI и strongSwan. Если вы ищете инструкции для других сертификатов, ознакомьтесь со статьями, посвященными [PowerShell](vpn-gateway-certificates-point-to-site.md) или [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Инструкции по установке strongSwan с помощью графического пользовательского интерфейса вместо интерфейса командной строки приведены в разделе [Установка и настройка](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="install-strongswan"></a>Установка strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Создание и экспорт сертификатов

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Следующие шаги

Продолжайте настраивать параметры конфигурации типа "точка — сеть", чтобы [создать и установить файлы конфигурации VPN-клиента](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
