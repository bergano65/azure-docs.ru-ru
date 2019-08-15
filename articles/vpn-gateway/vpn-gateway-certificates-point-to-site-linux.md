---
title: 'Создание и экспорт сертификатов для подключений "точка — сеть" — Linux: CLI: Azure | Документация Майкрософт'
description: Создание самозаверяющего корневого сертификата, экспорт открытого ключа и создание сертификатов клиента с помощью Linux CLI и strongSwan.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: alzam
ms.openlocfilehash: 79840010561746f2885909123012ffb4efbea5f6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990314"
---
# <a name="generate-and-export-certificates"></a>Создание и экспорт сертификатов

Для аутентификации подключений типа "точка — сеть" используются сертификаты. Эта статья поможет создать самозаверяющий корневой сертификат, а также сертификаты клиента с помощью Linux CLI и strongSwan. Если вы ищете инструкции для других сертификатов, ознакомьтесь со статьями, посвященными [PowerShell](vpn-gateway-certificates-point-to-site.md) или [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Инструкции по установке strongSwan с помощью графического пользовательского интерфейса вместо интерфейса командной строки приведены в разделе [Установка и настройка](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="generate-and-export"></a>Создание и экспорт
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Следующие шаги

Продолжайте настраивать параметры конфигурации типа "точка — сеть", чтобы [создать и установить файлы конфигурации VPN-клиента](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
