---
title: Включить имя файла
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/30/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 167d50e5c7f3049f46fd8540630e47044240809f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81536593"
---
Служба [файлов Azure](../articles/storage/files/storage-files-introduction.md) поддерживает проверку подлинности на основе удостоверений через протокол SMB с помощью [локальных домен Active Directory служб (AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (Предварительная версия) и [Azure Active Directory доменных служб (Azure AD DS)](../articles/active-directory-domain-services/overview.md). Эта статья посвящена тому, как файловые ресурсы Azure могут использовать службы домена локально или в Azure для поддержки доступа на основе удостоверений к файловым ресурсам Azure через SMB. Включение доступа на основе удостоверений для файловых ресурсов Azure позволяет заменить существующие файловые серверы на файловые ресурсы Azure, не заменяя существующую службу каталогов, сохраняя при этом простой доступ пользователей к общим ресурсам. 

Служба "файлы Azure" обеспечивает авторизацию доступа пользователей к общим ресурсам и уровням каталогов и файлов. Назначение разрешений на уровне общего ресурса может выполняться для пользователей Azure Active Directory (Azure AD) или групп, управляемых с помощью модели [управления доступом на основе ролей (RBAC)](../articles/role-based-access-control/overview.md) . При использовании RBAC учетные данные, используемые для доступа к файлам, должны быть доступны или синхронизированы с Azure AD. Вы можете назначить встроенные роли RBAC, такие как хранилище файлов хранилища SMB, для пользователей или групп в Azure AD, чтобы предоставить доступ на чтение к файловому ресурсу Azure.

На уровне каталога или файла служба файлов Azure поддерживает сохранение, наследование и принудительное применение списков управления [доступом Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) , как и для любых файловых серверов Windows. Вы можете использовать списки DACL Windows при копировании данных по протоколу SMB между существующей общей папкой и файловыми ресурсами Azure. Если вы планируете применять авторизацию, вы можете использовать файловые ресурсы Azure для резервного копирования списков ACL вместе с данными. 
