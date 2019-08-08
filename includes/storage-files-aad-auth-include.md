---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5c45dbe29bb86150c76cf5bc136c4a7504270f86
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854552"
---
Служба [файлов Azure](../articles/storage/files/storage-files-introduction.md) поддерживает проверку подлинности на основе удостоверений через протокол SMB с помощью [доменных служб Azure Active Directory (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Подключенные к домену виртуальные машины Windows могут получать доступ к файловым ресурсам Azure с помощью учетных данных [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) .

Вы можете управлять доступом к файлам Azure на уровне общего доступа к удостоверению, например пользователю или группе в Azure AD, с помощью [управления доступом на основе ролей (RBAC)](../articles/role-based-access-control/overview.md). Можно определить пользовательские роли RBAC, которые включают общие наборы разрешений, используемых для доступа к службе "файлы Azure". При назначении пользовательской роли RBAC удостоверению Azure AD этому удостоверению предоставляется доступ к общей папке Azure в соответствии с этими разрешениями.

Службы файлов Azure также поддерживают сохранение, наследование и применение [списков DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) ко всем файлам и каталогам в общей папке. При копировании данных из общей папки в службу файлов Azure или наоборот можно указать, что поддерживаются списки DACL NTFS. Таким образом можно реализовать сценарии резервного копирования с помощью службы файлов Azure, сохранив списки DACL NTFS между локальной общей папкой и облачным файловым ресурсом. 

> [!NOTE]
> - Проверка подлинности Azure AD DS для доступа к серверу SMB не поддерживается для виртуальных машин Linux. Поддерживаются только виртуальные машины Windows.
> - Аутентификация Azure AD DS для доступа по протоколу SMB не поддерживается для Active Directory компьютеров, присоединенных к домену. В предварительной среде рассмотрите возможность использования [Синхронизация файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) , чтобы начать перенос данных в службу файлов Azure и продолжить принудительное управление доступом с помощью учетных данных Active Directory локальных Active Directory компьютеров, присоединенных к домену. 
> - Проверка подлинности Azure AD DS для доступа по протоколу SMB доступна только для учетных записей хранения, созданных после 24 сентября 2018 г.
> - Проверка подлинности AD DS Azure для доступа к SMB и контроля DACL NTFS не поддерживается в общих файловых ресурсах Azure, управляемых Синхронизация файлов Azure.
> - Проверка подлинности Azure AD DS не поддерживает проверку подлинности учетных записей компьютеров, созданных в Azure AD DS.
