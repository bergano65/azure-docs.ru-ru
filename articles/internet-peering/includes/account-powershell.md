---
title: Включить имя файла
titleSuffix: Azure
description: включить файл
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: beffb2babefd86c2807e21e9337cba66f42fcfc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678486"
---
Перед началом настройки установите и импортируйте необходимые модули. Для установки модулей в PowerShell требуются права администратора.

1. Установите и импортируйте модуль AZ.
    ```powershell
    Install-Module Az -AllowClobber
    Import-Module Az
    ```
1. Установите и импортируйте модуль AZ. пиринг.
    ```powershell
    Install-Module -Name Az.Peering -AllowClobber
    Import-Module Az.Peering
    ```
1. Проверьте правильность импорта модулей с помощью следующей команды:
    ```powershell
    Get-Module
    ```
1. Войдите в учетную запись Azure с помощью следующей команды:
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
> Если вы еще не установили связь с ASN и подпиской, выполните действия, описанные в разделе [связывание однорангового ASN](../howto-subscription-association-powershell.md). Это действие требуется для запроса пиринга.

> [!NOTE]
> Расположение группы ресурсов не зависит от расположения, в котором вы решили настроить пиринг.
&nbsp;
