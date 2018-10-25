---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: c2dc0f2dea752dd44ab2805a0d6fc0e5517f5c6c
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48831710"
---
[Служба файлов Azure](../articles/storage/files/storage-files-introduction.md) поддерживает проверку подлинности на основе удостоверений по протоколу SMB (Server Message Block) (предварительная версия) через [доменные службы Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/active-directory-ds-overview.md). Присоединенные к домену виртуальные машины Windows имеют доступ к файловым ресурсам Azure с помощью учетных данных [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md). 

Azure AD проверяет подлинность удостоверения, например пользователя, группы или субъекта-службы, с помощью [управления доступом на основе ролей (RBAC)](../articles/role-based-access-control/overview.md). Можно определить пользовательские роли RBAC, которые распространяются на общие наборы разрешений, используемых для доступа к службе файлов Azure. При назначении пользовательской роли RBAC удостоверению Azure AD этому удостоверению предоставляется доступ к общей папке Azure в соответствии с этими разрешениями.

В рамках предварительной версии служба файлов Azure также поддерживает сохранение, наследование и применение [списков DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) ко всем файлам и каталогам в общей папке. При копировании данных из общей папки в службу файлов Azure или наоборот можно указать, что поддерживаются списки DACL NTFS. Таким образом, можно реализовать сценарии резервного копирования, использующие службу файлов Azure, сохраняя списки DACL NTFS между локальной и облачной общими папками. 

> [!NOTE]
> В предварительной версии проверка подлинности Azure AD по протоколу SMB не поддерживается для виртуальных машин Linux. Поддерживаются только виртуальные машины Windows.

> Проверка подлинности Azure AD по протоколу SMB не поддерживается компьютерами локальной среды при подключении к файлам Azure с помощью учетных данных AD или AAD.

> Проверка подлинности Azure AD доступна только для учетных записей хранения, созданных после 24 сентября 2018 г.
