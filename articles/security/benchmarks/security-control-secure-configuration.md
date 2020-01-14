---
title: Управление безопасностью Azure — безопасная конфигурация
description: Безопасная конфигурация управления безопасностью
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934290"
---
# <a name="security-control-secure-configuration"></a>Управление безопасностью: безопасная конфигурация

Установите, реализуйте и активно Управляйте (Отслеживайте, сообщайте об исправьте) конфигурацию безопасности ресурсов Azure, чтобы предотвратить взлом злоумышленниками уязвимых служб и параметров.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Установите безопасные конфигурации для всех ресурсов Azure.

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.1 | 5.1 | Customer |

Используйте политику Azure или центр безопасности Azure, чтобы поддерживать конфигурации безопасности для всех ресурсов Azure.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: Установка безопасных конфигураций операционной системы

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.2 | 5.1 | Customer |

Используйте рекомендацию центра безопасности Azure &quot;исправьте уязвимости в конфигурациях безопасности на виртуальных машинах&quot; для поддержки конфигураций безопасности во всех ресурсах вычислений.

Как отслеживать рекомендации центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Как исправлять рекомендации центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: поддержание защищенных конфигураций ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.3 | 5,2 | Customer |

Используйте политику Azure [Deny] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Общие сведения о влиянии политики Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: поддержание защищенных конфигураций операционных систем

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.4 | 5,2 | Общая |

Базовые образы операционной системы управляются и обслуживаются корпорацией Майкрософт.

Однако параметры безопасности, необходимые для Организации, можно применить с помощью шаблонов Azure Resource Manager и (или) настройки требуемого состояния.

Как создать виртуальную машину Azure на основе шаблона Azure Resource Manager:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Общие сведения о конфигурации требуемого состояния для виртуальных машин Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: безопасное хранение конфигурации ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.5 | 5,3 | Customer |

Если вы используете пользовательские определения политики Azure, используйте Azure DevOps или Azure Repos для безопасного хранения кода и управления им.

Как сохранить код в Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Документация по Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: безопасное хранение пользовательских образов операционной системы

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.6 | 5,3 | Customer |

При использовании пользовательских образов используйте RBAC, чтобы обеспечить доступ к образам только полномочным пользователям. Для образов контейнеров Храните их в реестре контейнеров Azure и используйте RBAC, чтобы обеспечить доступ к образам только полномочным пользователям.

Общие сведения о RBAC в Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Общие сведения о RBAC для реестра контейнеров:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Как настроить RBAC в Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7,7: развертывание средств управления конфигурацией системы

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.7 | 5.4 | Customer |

Используйте политику Azure для оповещения, аудита и принудительного применения конфигураций системы. Кроме того, разрабатывайте процесс и конвейер для управления исключениями политик.

Как настроить политику Azure и управлять ей:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией системы для операционных систем

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.8 | 5.4 | Customer |

Используйте расширения вычислений Azure, такие как настройка требуемого состояния PowerShell для расширения Windows Compute или Linux Chef для Linux.

Как установить расширения виртуальной машины в Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9. Реализация автоматического мониторинга конфигурации для служб Azure

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.9 | 5.5 | Customer |

Использование центра безопасности Azure для выполнения проверок базовых показателей ресурсов Azure

Как исправить рекомендации в центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10. Реализация автоматического мониторинга конфигурации для операционных систем

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.1 | 5.5 | Customer |

Используйте центр безопасности Azure для выполнения проверок базовых параметров ОС и DOCKER для контейнеров.

Сведения о рекомендациях по контейнеру центра безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7,11: безопасное управление секретами Azure 

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.11 | Версия 13.1 | Customer |

Используйте Управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы упростить и защитить управление секретами для облачных приложений.

Как интегрироваться с управляемыми удостоверениями Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Создание Key Vault.

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Как обеспечить проверку подлинности Key Vault с помощью управляемого удостоверения:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: безопасно управляйте удостоверениями и автоматически

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7.12 | 4.1 | Customer |

Используйте управляемые удостоверения для предоставления служб Azure с автоматически управляемым удостоверением в Azure AD. Управляемые удостоверения позволяют проходить проверку подлинности в любой службе, поддерживающей проверку подлинности Azure AD, включая Key Vault без каких бы то ни было учетных данных в коде.

Как настроить управляемые удостоверения:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: устранение непреднамеренной раскрытия учетных данных

| Идентификатор Azure | Идентификаторы CIS | Ответственность |
|--|--|--|
| 7,13 | 13.3 | Customer |

Реализуйте средство проверки учетных данных для обнаружения учетных данных в коде. Средство проверки учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault. 

Настройка средства проверки учетных данных:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Дальнейшие действия

См. следующий контроль безопасности: [Защита от вредоносных программ](security-control-malware-defense.md)
