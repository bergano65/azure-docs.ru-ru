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
ms.openlocfilehash: ff2ed5abbf2ce67a0b96a5da450b83832403db1f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269368"
---
[Служба файлов Azure](../articles/storage/files/storage-files-introduction.md) поддерживает проверку подлинности на основе удостоверений по протоколу SMB (Server Message Block) (предварительная версия) через [доменные службы Azure Active Directory (Azure AD)](../articles/active-directory-domain-services/overview.md). Присоединенные к домену виртуальные машины Windows имеют доступ к файловым ресурсам Azure с помощью учетных данных [Azure AD](../articles/active-directory/fundamentals/active-directory-whatis.md). 

Вы можете управлять доступом уровня общей папки службы файлов Azure на работу с удостоверением, таких как пользователь, группа в Azure AD с [управления доступом на основе ролей (RBAC)](../articles/role-based-access-control/overview.md). Можно определить пользовательские роли RBAC, которые распространяются на общие наборы разрешений, используемых для доступа к службе файлов Azure. При назначении пользовательской роли RBAC удостоверению Azure AD этому удостоверению предоставляется доступ к общей папке Azure в соответствии с этими разрешениями.

В рамках предварительной версии служба файлов Azure также поддерживает сохранение, наследование и применение [списков DACL NTFS](https://technet.microsoft.com/library/2006.01.howitworksntfs.aspx) ко всем файлам и каталогам в общей папке. При копировании данных из общей папки в службу файлов Azure или наоборот можно указать, что поддерживаются списки DACL NTFS. Таким образом, можно реализовать сценарии резервного копирования, использующие службу файлов Azure, сохраняя списки DACL NTFS между локальной и облачной общими папками. 

> [!NOTE]
> - Идентификация Azure службы домена AD для доступа по SMB не поддерживается для виртуальных машин Linux. Поддерживаются только виртуальные машины Windows.
> - Идентификация Azure службы домена AD для доступа по SMB не поддерживается для присоединения к домену Active Directory. Тем временем вы можете использовать [службе синхронизации файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) начать перенос данных в службу файлов Azure и продолжить для реализации управления доступом, используя учетные данные AD из вашего локального домена AD компьютеров, присоединенных к. 
> - Идентификация Azure службы домена AD для доступа по SMB доступен только для учетных записей хранения, созданной после 24 сентября 2018 г.
> - Идентификация доменные службы Azure для доступа по SMB и постоянные DACL NTFS не поддерживается в файловые ресурсы Azure под управлением службы синхронизации файлов Azure. 
