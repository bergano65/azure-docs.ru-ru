---
title: Настройка локального управления доступом на основе ролей (RBAC) для Azure API для FHIR
description: В этой статье описывается, как настроить API Azure для FHIR на использование внешнего клиента Azure AD для плоскости данных.
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: mihansen
ms.openlocfilehash: a8c1b36d6a439297dfb0bbcb34efe059349fc5a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871164"
---
# <a name="configure-local-rbac-for-fhir"></a>Настройка локального RBAC для FHIR 

В этой статье объясняется, как настроить API Azure для FHIR на использование внешнего дополнительного клиента Azure Active Directory для управления доступом к плоскости данных. Используйте этот режим, только если вы не можете использовать клиент Azure Active Directory, связанный с подпиской.

> [!NOTE]
> Если плоскость данных службы FHIR настроена для использования основного клиента Azure Active Directory, связанного с вашей подпиской, [Используйте Azure RBAC для назначения ролей плоскости данных](configure-azure-rbac.md).

## <a name="add-service-principal"></a>Добавление субъекта-службы

Локальный RBAC позволяет использовать внешний клиент Azure Active Directory с сервером FHIR. Чтобы система RBAC позволяла проверять принадлежность к группам в этом клиенте, API Azure для FHIR должен иметь субъект-службу в клиенте. Этот субъект-служба будет создан автоматически в клиентах, привязанных к подпискам, которые развернули API Azure для FHIR, но если к клиенту не привязана подписка, администратору клиента потребуется создать этот субъект-службу с помощью одной из следующих команд:

С помощью `Az` модуля PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

также можно использовать `AzureAd` модуль PowerShell:

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

также можно использовать Azure CLI:

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>Настройка локального RBAC

Вы можете настроить API Azure для FHIR на использование внешнего или дополнительного клиента Azure Active Directory в колонке **Аутентификация** :

![Локальные назначения RBAC](media/rbac/local-rbac-guids.png).

В поле Authority (центр) введите допустимый клиент Azure Active Directory. После проверки клиента в поле **Разрешенные идентификаторы объектов** должны быть активированы и можно ввести список идентификаторов объектов идентификаторов. Эти идентификаторы могут быть идентификаторами объектов идентификаторов:

* Пользователь Azure Active Directory.
* Субъект-служба Azure Active Directory.
* Группа безопасности Azure Active Directory.

Дополнительные сведения см. в статье о [поиске идентификаторов объектов идентификаторов](find-identity-object-ids.md) .

После ввода требуемых идентификаторов объектов нажмите кнопку **сохранить** и дождитесь сохранения изменений, прежде чем пытаться получить доступ к плоскости данных с помощью назначенных пользователей, субъектов-служб или групп.

## <a name="caching-behavior"></a>Поведение кэширования

API Azure для FHIR будет кэшировать решения в течение 5 минут. Если вы предоставите пользователю доступ к серверу FHIR, добавив его в список разрешенных идентификаторов объектов или удалите их из списка, вы должны пройти до пяти минут на изменение разрешений для распространения.

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как назначить FHIR доступ к плоскости данных с помощью внешнего (дополнительного) клиента Azure Active Directory. Далее вы узнаете о дополнительных параметрах для Azure API для FHIR:
 
>[!div class="nextstepaction"]
>[Дополнительные параметры API Azure для FHIR](azure-api-for-fhir-additional-settings.md)

