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
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995502"
---
В настоящее время многоканальный трафик SMB для общих файловых ресурсов Azure имеет следующие ограничения.
- Может использоваться только с локально избыточными учетными записями Филестораже.
- Поддерживается только для клиентов Windows. 
- Максимальное число каналов — четыре.
- SMB Direct не поддерживается.
- Для учетных записей хранения с локальной домен Active Directory службами (AD DS) или [проверкой подлинности на основе удостоверений](../articles/storage/files/storage-files-active-directory-overview.md) Azure AD DS для службы файлов Azure клиенты SMB не смогут использовать проводник Windows для настройки разрешений NTFS для каталогов и файлов.
    - Для настройки разрешений Используйте средство Windows [icacls](/windows-server/administration/windows-commands/icacls) или команду [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) .

