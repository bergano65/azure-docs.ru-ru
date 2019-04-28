---
title: включение файла
description: Добавление файла с предварительными требованиями
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359381"
---
> [!IMPORTANT]
> **Прежде чем выполнять инструкции в этой статье, включите синхронизацию хэша паролей в доменных службах Azure AD.**
>
> Следуйте инструкциям, приведенным ниже, в зависимости от типа пользователей в вашем каталоге Azure AD. Выполните оба набора инструкций, если в вашем каталоге Azure AD используется сочетание облачных и синхронизируемых учетных записей пользователей. Вы не сможете выполнять следующие операции в случае, если вы пытаетесь использовать учетную запись гостя B2B (например, ваш gmail или MSA от разных поставщиков удостоверений, которого мы позволяем), так как у нас нет пароля для этих пользователей, синхронизировать управляемый домен, так как они учетные записи гостя в каталоге. Полные сведения об этих учетных записях, включая пароли будут находиться за пределами Azure AD и как эта информация не находится в Azure AD таким образом он не даже синхронизации к управляемому домену. 
> - [Инструкции для облачных учетных записей пользователей](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Инструкции для учетных записей пользователей, синхронизируемых из локального каталога](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
