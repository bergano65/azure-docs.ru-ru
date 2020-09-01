---
title: Управление безопасностью Azure — безопасная конфигурация
description: Безопасная конфигурация управления безопасностью Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ac538d71f854643cb59390447be360bc541142ac
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077234"
---
# <a name="security-control-secure-configuration"></a>Управление безопасностью: безопасная конфигурация

Установите, реализуйте и активно Управляйте (Отслеживайте, сообщайте об исправьте) конфигурацию безопасности ресурсов Azure, чтобы предотвратить взлом злоумышленниками уязвимых служб и параметров.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1. Установка безопасных конфигураций для всех ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7.1 | 5,1 | Клиент |

Используйте псевдонимы политик Azure для создания настраиваемых политик для аудита или принудительного применения конфигурации ресурсов Azure. Вы также можете использовать встроенные определения политик Azure.

Кроме того, Azure Resource Manager может экспортировать шаблон в нотация объектов JavaScript (JSON), который следует проверить, чтобы убедиться, что конфигурации соответствуют требованиям безопасности Организации или превышают их.

Вы также можете использовать рекомендации из центра безопасности Azure в качестве защищенного шаблона базовой конфигурации для ресурсов Azure.

- [Просмотр доступных псевдонимов политик Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Руководство по Создание политик и управление ими для обеспечения соответствия требованиям](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Экспорт одного и нескольких ресурсов в шаблон в портал Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Рекомендации по безопасности — справочное руководство](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2. Сохранение безопасных конфигураций для операционных систем

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7.2 | 5,1 | Клиент |

Используйте рекомендации центра безопасности Azure для сохранения конфигураций безопасности во всех ресурсах вычислений.  Кроме того, можно использовать пользовательские образы операционной системы или настройку состояния службы автоматизации Azure, чтобы установить конфигурацию безопасности операционной системы, требуемой для Организации.

- [Как отслеживать рекомендации центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Рекомендации по безопасности — справочное руководство](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Обзор настройки состояния службы автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Отправка виртуального жесткого диска и его использование для создания новых виртуальных машин Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Создание виртуальной машины Linux на основе пользовательского диска с помощью Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3. Сохранение безопасных конфигураций для ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7.3 | 5,2 | Клиент |

Используйте политику Azure [Deny] и [развернуть, если не существует], чтобы обеспечить безопасность параметров в ресурсах Azure.  Кроме того, вы можете использовать шаблоны Azure Resource Manager для поддержания конфигурации безопасности ресурсов Azure, необходимых вашей организации. 

- [Общие сведения о влиянии политики Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Создание политик и управление ими для обеспечения соответствия](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Общие сведения о шаблонах Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4. Сохранение безопасных конфигураций для операционных систем

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7,4 | 5,2 | Совмещаемая блокировка |

Следуйте рекомендациям центра безопасности Azure, чтобы выполнять оценку уязвимостей в ресурсах вычислений Azure.  Кроме того, вы можете использовать шаблоны Azure Resource Manager, пользовательские образы операционной системы или настройку состояния службы автоматизации Azure для поддержания конфигурации безопасности операционной системы, необходимой для Организации.   Шаблоны виртуальных машин Майкрософт, Объединенные с конфигурацией требуемого состояния службы автоматизации Azure, могут помочь в собрании и обслуживании требований безопасности. 

Кроме того, обратите внимание, что образы виртуальных машин Azure Marketplace, опубликованные корпорацией Майкрософт, управляются и обслуживаются корпорацией Майкрософт. 

- [Реализация рекомендаций по оценке уязвимостей в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Создание виртуальной машины Azure на основе шаблона Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Обзор настройки состояния службы автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Создание виртуальной машины Windows на портале Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Сведения о том, как скачать шаблон виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Пример сценария для отправки VHD в Azure и создания виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5. Безопасное хранение конфигурации ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7.5 | 5,3 | Клиент |

Используйте Azure DevOps для безопасного хранения и управления кодом, например пользовательскими политиками Azure, Azure Resource Manager шаблонами и скриптами настройки требуемого состояния. Чтобы получить доступ к ресурсам, которыми вы управляете в Azure DevOps, вы можете предоставить или отклонить разрешения для определенных пользователей, встроенных групп безопасности или групп, определенных в Azure Active Directory (Azure AD), если они интегрированы с Azure DevOps, или Active Directory, если они интегрированы с TFS.

- [Как хранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [О разрешениях и группах в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6. Безопасное хранение пользовательских образов операционной системы

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7.6 | 5,3 | Клиент |

При использовании пользовательских образов используйте управление доступом на основе ролей Azure (Azure RBAC), чтобы обеспечить доступ к образам только полномочным пользователям. Используя коллекцию общих образов, вы можете делиться своими образами с другими пользователями, участниками службы или группами конструктора приложений в вашей организации.  Для образов контейнеров Храните их в реестре контейнеров Azure и используйте Azure RBAC, чтобы обеспечить доступ к образам только полномочным пользователям.  

- [Общие сведения об Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Общие сведения об Azure RBAC для реестра контейнеров](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Настройка Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Обзор коллекции общих образов](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: развертывание средств управления конфигурацией для ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7.7 | 5,4 | Клиент |

Определение и реализация стандартных конфигураций безопасности для ресурсов Azure с помощью политики Azure. Используйте псевдонимы политик Azure для создания настраиваемых политик для аудита или принудительного применения конфигурации сети ресурсов Azure. Вы также можете использовать встроенные определения политик, связанные с конкретными ресурсами.  Кроме того, вы можете использовать службу автоматизации Azure для развертывания изменений конфигурации.

- [Настройка Политики Azure и управление ею](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Использование псевдонимов](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: развертывание средств управления конфигурацией для операционных систем

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7,8 | 5,4 | Клиент |

Настройка состояния службы автоматизации Azure — это служба управления конфигурацией для узлов Desired State Configuration (DSC) в любом облачном или локальном центре обработки данных. Вы можете легко переносить компьютеры в облачную среду, присваивать им декларативные конфигурации, а также просматривать отчеты, отражающие соответствие каждого компьютера требуемому состоянию. 

- [Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9. Реализация автоматического мониторинга конфигурации для ресурсов Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7.9 | 5.5 | Клиент |

Используйте центр безопасности Azure для выполнения проверок базовых показателей ресурсов Azure.  Кроме того, используйте политику Azure для оповещения и аудита конфигураций ресурсов Azure.

- [Как исправить рекомендации в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10. Реализация автоматизированного мониторинга конфигурации для операционных систем

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7.10 | 5.5 | Клиент |

Используйте центр безопасности Azure для выполнения проверок базовых параметров ОС и DOCKER для контейнеров.

- [Общие сведения о рекомендациях центра безопасности Azure в отношении контейнеров](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11. Безопасное управление секретами Azure

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7,11 | Версия 13.1 | Клиент |

Используйте Управляемое удостоверение службы в сочетании с Azure Key Vault, чтобы упростить и защитить управление секретами для облачных приложений.

- [Интеграция с управляемыми удостоверениями Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Создание Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Как обеспечить проверку подлинности Key Vault с помощью управляемого удостоверения](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12. Безопасное и автоматическое управление удостоверениями

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7.12 | 4.1 | Клиент |

Используйте управляемые удостоверения для предоставления служб Azure с автоматически управляемым удостоверением в Azure AD. Управляемое удостоверение можно использовать для проверки подлинности в любой службе, которая поддерживает проверку подлинности Azure AD, включая Key Vault, при этом не сохраняя каких-либо учетных данных в коде.

- [Настройка управляемых удостоверений](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13. Устранение непреднамеренного раскрытия учетных данных

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 7,13 | 18,1, 18,7 | Клиент |

Реализуйте средство проверки учетных данных для обнаружения учетных данных в коде. Сканер учетных данных также рекомендует перемещать обнаруженные учетные данные в более безопасные расположения, такие как Azure Key Vault. 

- [Как настроить сканер учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Дальнейшие действия

- См. следующий контроль безопасности:  [Защита от вредоносных программ](security-control-malware-defense.md)