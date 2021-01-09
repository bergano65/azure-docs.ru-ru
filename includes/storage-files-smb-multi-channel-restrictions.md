---
title: включить файл
description: включить файл
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: bbf0530c1a7f1a747d456d87efc106418f23b7ba
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052876"
---
В настоящее время многоканальный трафик SMB для общих файловых ресурсов Azure имеет следующие ограничения.
- Может использоваться только с локально избыточными учетными записями Филестораже.
- Поддерживается только для клиентов Windows. 
- Максимальное число каналов — четыре.
- SMB Direct не поддерживается.
- Частные конечные точки для учетных записей хранения не поддерживаются.
- Для учетных записей хранения с локальной домен Active Directory службами (AD DS) или [проверкой подлинности на основе удостоверений](../articles/storage/files/storage-files-active-directory-overview.md) Azure AD DS для службы файлов Azure клиенты SMB не смогут использовать проводник Windows для настройки разрешений NTFS для каталогов и файлов.
    - Для настройки разрешений Используйте средство Windows [icacls](/windows-server/administration/windows-commands/icacls) или команду [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) .

