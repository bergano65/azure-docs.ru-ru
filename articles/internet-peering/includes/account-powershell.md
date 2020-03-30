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
ms.openlocfilehash: 3a5f7157ef8f3645dd03ec93684238dd8bbc067e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774228"
---
Перед началом конфигурации установите и импортируйте необходимые модули. Для установки модулей в PowerShell потребуются привилегии администратора.

1. Установка и импортирование модуля Az
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Установка и импортирование модуля Az.Peering
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Проверить модули импортируются штрафа с помощью команды ниже.
    ```powershell
    Get-Module
    ```
1. Вопийте в учетную запись Azure, используя следующую команду.
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
> Если вы еще не связали ваш ASN и подписку, а затем следуйте шагам для [Associate Peer ASN](../howto-subscription-association-powershell.md). Это необходимо для запроса пиринга.

> [!NOTE]
> Местоположение группы ресурсов не зависит от местоположения, по которому вы решили настроить пиринг.
&nbsp;
