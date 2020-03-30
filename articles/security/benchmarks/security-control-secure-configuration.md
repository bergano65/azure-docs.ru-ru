---
title: Контроль безопасности Azure - Безопасная конфигурация
description: Безопасная конфигурация управления безопасностью
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934290"
---
# <a name="security-control-secure-configuration"></a>Контроль безопасности: Безопасная конфигурация

Создание, внедрение и активное управление (отслеживание, отчет, исправление) конфигурации безопасности ресурсов Azure, чтобы предотвратить использование злоумышленниками уязвимых служб и настроек.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Создание безопасных конфигураций для всех ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.1 | 5.1 | Customer |

Для поддержания конфигураций безопасности для всех ресурсов Azure используйте Azure Policy или Azure Security Center.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Создание безопасных конфигураций операционных систем

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.2 | 5.1 | Customer |

Для поддержания конфигураций безопасности на всех вычислительных ресурсах используйте рекомендации &quot;Центра безопасности Azure Исправления уязвимостей в конфигурациях безопасности на ваших виртуальных машинах.&quot;

Как следить за рекомендациями Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Как уделать рекомендации Центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Поддержание безопасных конфигураций ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.3 | 5,2 | Customer |

Используйте политику Azure «отрицать» и «развертывать, если не существует», чтобы обеспечить безопасные настройки в ресурсах Azure.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Понять эффекты политики Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Поддержание безопасных конфигураций операционных систем

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.4 | 5,2 | Shared |

Базовые изображения операционной системы управляются и поддерживаются корпорацией Майкрософт.

Тем не менее, можно применить параметры безопасности, требуемые организацией, с помощью шаблонов Azure Resource Manager и/или конфигурации состояния.

Как создать виртуальную машину Azure из шаблона управления ресурсами Azure:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Поймите желаемую конфигурацию состояния для виртуальных машин Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.5 | 5,3 | Customer |

При использовании пользовательских определений политики Azure используйте Azure DevOps или Azure Repos для безопасного хранения и управления кодом.

Как хранить код в Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Документация Репос Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Безопасно хранить пользовательские изображения операционной системы

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.6 | 5,3 | Customer |

При использовании пользовательских изображений используйте RBAC, чтобы обеспечить доступ к изображениям только авторизованным пользователям. Для изображений контейнеров храните их в реестре контейнеров Azure и используете RBAC для обеспечения доступа к изображениям только авторизованных пользователей.

Поймите RBAC в Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Поймите RBAC для контейнерного реестра:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Как настроить RBAC в Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: Инструменты управления конфигурацией конфигурации системы развертывание

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.7 | 5.4 | Customer |

Используйте политику Azure для оповещения, аудита и обеспечения безопасности конфигураций систем. Кроме того, разработайте процесс и конвейер для управления исключениями политики.

Как настроить и управлять политикой Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Развертывать инструменты управления конфигурацией системы для операционных систем

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.8 | 5.4 | Customer |

Используйте расширения Azure для вычислений, такие как конфигурация состояния PowerShell Desired state Configuration для Windows compute или linux Chef Extension for Linux.

Как установить виртуальные расширения машины в Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Внедрение автоматизированного мониторинга конфигурации для служб Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.9 | 5.5 | Customer |

Используйте Центр безопасности Azure для выполнения базовых сканирований ресурсов Azure

Как уделать рекомендации в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Внедрение автоматизированного мониторинга конфигурации для операционных систем

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.1 | 5.5 | Customer |

Используйте Центр безопасности Azure для выполнения базовых сканов для настроек ОС и докеров для контейнеров.

Поймите рекомендации контейнерного центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Надежное управление секретами Azure 

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.11 | Версия 13.1 | Customer |

Используйте идентификатор управляемой службы совместно с Azure Key Vault для упрощения и обеспечения секретного управления облачными приложениями.

Как интегрироваться с управляемыми идентификаторами Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Как создать Убежище ключей:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Как обеспечить аутентификацию Key Vault с управляемой идентификацией:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Управление идентификаторами безопасно и автоматически

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.12 | 4.1 | Customer |

Используйте управляемые идентификаторы для предоставления службам Azure автоматического управления идентификаторами в Azure AD. Управляемые идентификаторы позволяют аутентифицировать проверку любой службы, которая поддерживает аутентификацию Azure AD, включая Key Vault, без каких-либо учетных данных в коде.

Как настроить управляемые идентификаторы:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Устранить непреднамеренное воздействие учетных данных

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.13 | 13.3 | Customer |

Реализация сканера учетных данных для идентификации учетных данных в коде. Сканер учетных данных также будет способствовать перемещению обнаруженных учетных данных в более безопасные места, такие как Убежище ключей Azure. 

Как настроить сканер учетных данных:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Дальнейшие действия

Смотрите следующий контроль безопасности: [Защита от вредоносных программ](security-control-malware-defense.md)
