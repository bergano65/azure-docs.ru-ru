---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649087"
---
Управляемое удостоверение от Azure Active Directory (Azure AD) позволяет приложению легко получать доступ к другим ресурсам, защищенным Azure AD, таким как Azure Key Vault. Удостоверения управляются платформой Azure, и для них не нужно подготавливать или изменять секреты. Дополнительные сведения об управляемых удостоверениях в Azure AD см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](../articles/active-directory/managed-identities-azure-resources/overview.md)

Приложению можно предоставить два типа удостоверений:

- **Назначаемое системой удостоверение** привязывается к приложению и удаляется при удалении приложения. Приложение может иметь только одно назначаемое системой удостоверение.
- **Назначаемое пользователем удостоверение** — это изолированный ресурс Azure, который можно назначить приложению. Приложение может иметь несколько назначаемых пользователем удостоверений.