---
title: 'Создание и экспорт сертификатов для P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Создание самозаверяющего корневого сертификата, экспорт открытого ключа и создание сертификатов клиента с помощью PowerShell в Windows 10 или Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151374"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Создание и экспорт сертификатов для подключений "точка — сеть" с помощью PowerShell

Для аутентификации подключений типа "точка — сеть" используются сертификаты. Эта статья поможет создать самозаверяющий корневой сертификат, а также сертификаты клиента с помощью PowerShell в Windows 10 или Windows Server 2016. Если вы ищете инструкции для других сертификатов, ознакомьтесь со статьями о [создании сертификатов с помощью средств Linux](vpn-gateway-certificates-point-to-site-linux.md) или [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Выполните описанные действия на компьютере с Windows 10 или Windows Server 2016. Командлеты PowerShell, которые используются для создания сертификатов, являются частью операционной системы и не работают в других версиях Windows. Компьютер с Windows 10 или Windows Server 2016 требуется только для создания сертификатов. После создания сертификатов их можно отправить или установить в любой поддерживаемой клиентской операционной системе.

При отсутствии доступа к компьютеру с Windows 10 или Windows Server 2016 для создания сертификатов можно использовать [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Сертификаты, созданные с помощью другого метода, можно установить в любой [поддерживаемой](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) клиентской операционной системе.

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install"></a>Установка экспортированного сертификата клиента

Для каждого клиента, который подключается к виртуальной сети через подключение "точка — сеть", сертификат должен быть установлен локально.

См. инструкции по [установке сертификата клиента для подключений типа "точка — сеть"](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Дополнительная информация

Продолжайте настраивать параметры конфигурации типа "точка-сеть".

* При использовании модели развертывания на основе **Resource Manager** см. инструкции по [настройке подключения типа "точка — сеть" с использованием собственной аутентификации Azure на основе сертификата](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Инструкции для **классической** модели развертывания см. в статье [Настройка подключения типа "точка — сеть" к виртуальной сети с помощью портала Azure (классическая модель)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).