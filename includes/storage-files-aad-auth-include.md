---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d1b10b55481b41f42c6c872522d3dd4dd4be0e77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570583"
---
Служба [файлов Azure](../articles/storage/files/storage-files-introduction.md) поддерживает проверку подлинности на основе удостоверений через протокол SMB (Предварительная версия) с помощью [доменных служб Azure Active Directory (AD DS)](../articles/active-directory-domain-services/overview.md). Подключенные к домену виртуальные машины Windows могут получать доступ к файловым ресурсам Azure с помощью учетных данных [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) .

Вы можете управлять доступом к файлам Azure на уровне общего доступа к удостоверению, например пользователю или группе в Azure AD, с помощью [управления доступом на основе ролей (RBAC)](../articles/role-based-access-control/overview.md). Можно определить пользовательские роли RBAC, которые включают общие наборы разрешений, используемых для доступа к службе "файлы Azure". При назначении пользовательской роли RBAC удостоверению Azure AD этому удостоверению предоставляется доступ к общей папке Azure в соответствии с этими разрешениями.

В рамках предварительной версии служба файлов Azure также поддерживает сохранение, наследование и применение [списков DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) ко всем файлам и каталогам в общей папке. При копировании данных из общей папки в службу файлов Azure или наоборот можно указать, что поддерживаются списки DACL NTFS. Таким образом можно реализовать сценарии резервного копирования с помощью службы файлов Azure, сохранив списки DACL NTFS между локальной общей папкой и облачным файловым ресурсом. 

> [!NOTE]
> - Проверка подлинности Azure AD DS для доступа к серверу SMB не поддерживается для виртуальных машин Linux. Поддерживаются только виртуальные машины Windows.
> - Аутентификация Azure AD DS для доступа по протоколу SMB не поддерживается для Active Directory компьютеров, присоединенных к домену. В предварительной среде рассмотрите возможность использования [Синхронизация файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) , чтобы начать перенос данных в службу файлов Azure и продолжить принудительное управление доступом с помощью учетных данных Active Directory локальных Active Directory компьютеров, присоединенных к домену. 
> - Проверка подлинности Azure AD DS для доступа по протоколу SMB доступна только для учетных записей хранения, созданных после 24 сентября 2018 г.
> - Проверка подлинности AD DS Azure для доступа к SMB и контроля DACL NTFS не поддерживается в общих файловых ресурсах Azure, управляемых Синхронизация файлов Azure.
