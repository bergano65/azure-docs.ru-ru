---
title: включить файл
titleSuffix: Azure
description: включить файл
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678486"
---
Перед началом настройки установите и импортируйте необходимые модули. Для установки модулей в PowerShell необходимы привилегии администратора.

1. Установите и импортируйте модуль Az.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Установка и импортируется модуль Az.Peering.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Убедитесь, что модули, импортированные должным образом, с помощью этой команды:
    ```powershell
    Get-Module
    ```
1. Войдите в учетную запись Azure с помощью следующей команды:
    ```powershell
    Connect-AzAccount
    ```
1. Проверьте подписку на учетную запись и выберите подписку, в которой вы хотите создать пиринг.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Если у вас еще нет группы ресурсов, необходимо создать ее перед созданием пиринга. с помощью следующей команды:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Если вы еще не связали свой ASN и подписку, выполните последующие действия в [Associate Peer ASN.](../howto-subscription-association-powershell.md) Это действие требуется для запроса пиринга.

> [!NOTE]
> Местоположение группы ресурсов не зависит от местоположения, по которому вы решили настроить пиринг.
&nbsp;
