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
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565097"
---
Служба [файлов Azure](../articles/storage/files/storage-files-introduction.md) поддерживает проверку подлинности на основе удостоверений через протокол SMB с помощью [Active Directory (AD)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (Предварительная версия) и [доменных служб Azure Active Directory (Azure AD DS)](../articles/active-directory-domain-services/overview.md) (GA). Эта статья посвящена тому, как служба файлов Azure может использовать службы доменов (локально или в Azure) для поддержки доступа на основе удостоверений к службе файлов Azure через SMB. Это позволяет легко заменить существующие файловые серверы файлами Azure и продолжить использовать имеющуюся службу каталогов, сохраняя при этом простой доступ пользователей к общим ресурсам. 

Служба "файлы Azure" обеспечивает авторизацию пользователя при доступе к общему ресурсу и на уровне каталога или файла. Назначение разрешений на уровне общего ресурса можно назначить пользователям или группам Azure AD, управляемым с помощью стандартной модели [управления доступом на основе ролей (RBAC)](../articles/role-based-access-control/overview.md) . При использовании RBAC учетные данные, используемые для доступа к файлам, должны быть доступны или синхронизированы с Azure AD. Вы можете назначить встроенные роли RBAC, такие как хранилище файлов хранилища SMB, для пользователей или групп в Azure AD, чтобы предоставить доступ на чтение к файловому ресурсу Azure.

На уровне каталога или файла служба файлов Azure поддерживает сохранение, наследование и принудительное применение списков управления [доступом Windows](https://docs.microsoft.com/windows/win32/secauthz/access-control-lists) , как и для любых файловых серверов Windows. При копировании данных по протоколу SMB из общей папки в службу файлов Azure или наоборот можно выбрать вариант сохранения списков DACL в Windows. Независимо от того, планируется ли выполнять авторизацию, вы можете использовать службу файлов Azure для резервного копирования списков ACL вместе с данными. 
