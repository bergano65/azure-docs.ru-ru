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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774228"
---
Перед началом настройки установите и импортируйте необходимые модули. Для установки модулей в PowerShell потребуются права администратора.

1. Установка и импорт модуля AZ
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Установка и импорт модуля AZ. пиринг
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Проверьте, правильно ли импортированы модули с помощью приведенной ниже команды.
    ```powershell
    Get-Module
    ```
1. Войдите в учетную запись Azure с помощью следующей команды.
    ```powershell
    Connect-AzAccount
    ```
1. Проверьте подписки для учетной записи и выберите подписку, в которой вы хотите создать пиринг.
    ```powershell
    Get-AzSubscription
    Select-AzSubscription -SubscriptionId "subscription-id"
    ```
1. Если у вас еще нет группы ресурсов, необходимо создать ее, прежде чем создавать пиринг. с помощью следующей команды:

    ```powershell
    New-AzResourceGroup -Name "PeeringResourceGroup" -Location "Central US"
    ```
> [!IMPORTANT]
> Если вы еще не сопоставили сопоставление ASN и подписку, выполните действия для [связывания однорангового ASN](../howto-subscription-association-powershell.md). Это необходимо для запроса пиринга.

> [!NOTE]
> Расположение группы ресурсов не зависит от расположения, в котором вы решили настроить пиринг.
&nbsp;
