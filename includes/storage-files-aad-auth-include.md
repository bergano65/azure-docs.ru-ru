---
title: включить файл
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b248bbb526baf355faf2564358884fd83422b037
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77565097"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) поддерживает аутентификацию на основе идентификации по block Server Message Block (SMB) через [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (предварительный просмотр) и [службы домена Активных каталогов Azure (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). В этой статье основное внимание уделяется тому, как Azure Files может использовать доменные службы, как в предварительном режиме, так и в Azure, для поддержки доступа к файлам Azure через SMB на основе идентификационных данных. Это позволяет легко заменить существующие файлы на Azure Files и продолжать использовать существующий сервис каталогов, сохраняя беспрепятственный доступ пользователей к акциям. 

Azure Files обеспечивает авторизацию на доступ пользователя как к доле, так и к уровню каталога/файла. Назначение разрешения на уровне общего доступа может быть назначено пользователям Azure AD или группам, управляемым с помощью [типичной ролевой модели управления доступом (RBAC).](../articles/role-based-access-control/overview.md) С Помощью RBAC учетные данные, которые используются для доступа к файлам, должны быть доступны или синхронизированы с Azure AD. Можно назначить пользователям или группам в Azure AD встроенные роли RBAC, такие как Хранение файлов SMB Reader, чтобы предоставить доступ к разделу файлов Azure.

На уровне каталога/файлов Azure Files поддерживает сохранение, наследование и обеспечение внедряния [DACL Windows,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) как и любых файлов Windows. Если вы копируете данные по SMB из общего файла в Azure Files, или наоборот, вы можете сохранить DACLs Windows. Планируете ли вы обеспечить выполнение авторизации или нет, можно использовать файлы Azure для резервного копирования АКЛ вместе с данными. 
