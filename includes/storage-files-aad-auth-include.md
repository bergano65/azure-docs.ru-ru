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
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536593"
---
[Azure Files](../articles/storage/files/storage-files-introduction.md) поддерживает аутентификацию на основе идентификации через server Message Block (SMB) через [внутренние службы домена Active Directory Domain Services (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (предварительный просмотр) и [службы домена Active Directory Domain Services (Azure AD DS).](../articles/active-directory-domain-services/overview.md) В этой статье основное внимание уделяется тому, как акции файлов Azure могут использовать службы домена, как на месте, так и в Azure, для поддержки доступа к файлам Azure через SMB. Включение доступа на основе идентификационных данных для ваших файлов Azure позволяет заменить существующие файловых серверов на файлы Azure, не заменяя существующий сервис каталогов, сохраняя беспрепятственный доступ пользователей к акциям. 

Azure Files обеспечивает авторизацию доступа пользователей как к уровню общего доступа, так и к уровням каталога/файла. Назначение разрешения на уровне общего доступа может быть выполнено пользователями Active Directory (Azure AD) или группами, управляемыми с помощью модели [управления доступом на основе ролей (RBAC).](../articles/role-based-access-control/overview.md) С Помощью RBAC учетные данные, которые используются для доступа к файлам, должны быть доступны или синхронизированы с Azure AD. Можно назначить пользователям или группам в Azure AD встроенные роли RBAC, такие как Хранение файлов SMB Reader, чтобы предоставить доступ к разделу файлов Azure.

На уровне каталога/файлов Azure Files поддерживает сохранение, наследование и обеспечение внедряния [DACL Windows,](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) как и любых файлов Windows. Вы можете сохранить DACL Windows при копировании данных по SMB между существующей долей файлов и вашими файлами Azure. Планируете ли вы обеспечить выполнение авторизации или нет, можно использовать файлы Azure для резервного копирования АКЛ вместе с данными. 
