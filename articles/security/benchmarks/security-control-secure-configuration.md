---
title: Контроль безопасности Azure - Безопасная конфигурация
description: Безопасная конфигурация управления безопасностью Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 981e13e772ee21e0e4a680e6d07b4ad4892a7cee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417068"
---
# <a name="security-control-secure-configuration"></a>Контроль безопасности: Безопасная конфигурация

Создание, внедрение и активное управление (отслеживание, отчет, исправление) конфигурации безопасности ресурсов Azure, чтобы предотвратить использование злоумышленниками уязвимых служб и настроек.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Создание безопасных конфигураций для всех ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.1 | 5.1 | Customer |

Используйте псевдонимы Azure Policy для создания пользовательских политик для аудита или обеспечения конфигурации ресурсов Azure. Можно также использовать встроенные определения политики Azure.

Кроме того, менеджер ресурсов Azure имеет возможность экспортировать шаблон в javaScript Object Notation (JSON), который должен быть пересмотрен, чтобы убедиться, что конфигурации соответствуют/превышают требования безопасности для вашей организации.

Рекомендации центра безопасности Azure можно также использовать в качестве надежной базовой базы конфигурации для ресурсов Azure.

- [Как просмотреть доступные псевдонимы Azure Policy](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Учебник: Создание и управление политиками для обеспечения соответствия требованиям](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Экспорт одного и многоресурсного ресурса на шаблон на портале Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Рекомендации по безопасности - справочное руководство](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Создание безопасных конфигураций операционных систем

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.2 | 5.1 | Customer |

Используйте рекомендации Центра безопасности Azure для поддержания конфигураций безопасности на всех вычислительных ресурсах.  Кроме того, для настройки конфигурации безопасности операционной системы, требуемой организацией, можно использовать пользовательские изображения операционной системы или конфигурацию состояния Azure Automation State.

- [Как контролировать рекомендации Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Рекомендации по безопасности - справочное руководство](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Обзор конфигурации состояния автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Загрузите VHD и используйте его для создания новых вывмов Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Создание виртуальной машины Linux на основе пользовательского диска с помощью Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Поддержание безопасных конфигураций ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.3 | 5,2 | Customer |

Используйте политику Azure «отрицать» и «развертывать, если не существует», чтобы обеспечить безопасные настройки в ресурсах Azure.  Кроме того, для поддержания конфигурации безопасности ресурсов Azure, требуемых организацией, можно использовать шаблоны диспетчеров ресурсов Azure. 

- [Сведения о действии политик Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Создание и управление политиками для обеспечения соответствия требованиям](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Обзор шаблонов управления ресурсами Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Поддержание безопасных конфигураций операционных систем

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.4 | 5,2 | Shared |

Следуйте рекомендациям Центра безопасности Azure по проведению оценки уязвимостей на ресурсах вычислений Azure.  Кроме того, для поддержания конфигурации безопасности операционной системы, требуемой организацией, можно использовать шаблоны менеджера ресурсов Azure, пользовательские изображения операционной системы или конфигурацию состояния Azure Automation State.   Шаблоны виртуальной машины Майкрософт в сочетании с конфигурацией Azure Automation Automation Desired State могут помочь в удовлетворении и поддержании требований безопасности. 

Кроме того, обратите внимание, что Azure Marketplace Виртуальные изображения машины, опубликованные Microsoft управляются и поддерживаются Microsoft. 

- [Как реализовать рекомендации по оценке уязвимостей Центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Как создать виртуальную машину Azure из шаблона управления ресурсами Azure](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Обзор конфигурации состояния автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Создание виртуальной машины Windows на портале Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Информация о том, как скачать шаблон VM](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Пример сценария для отправки VHD в Azure и создания виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Безопасное хранение конфигурации ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.5 | 5,3 | Customer |

Используйте Azure DevOps для безопасного хранения и управления кодом, таких как пользовательские политики Azure, шаблоны Azure Resource Manager и шаблоны «Желаемая конфигурация состояния». Чтобы получить доступ к ресурсам, которым ими управляети в Azure DevOps, можно предоставить или отказать в выдаче разрешений определенным пользователям, встроенным группам безопасности или группам, определенным в Active Directory Azure (Azure AD), если они интегрированы с Azure DevOps, или Active Directory, если они интегрированы с TFS.

- [Как хранить код в Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [О разрешениях и группах в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Безопасно хранить пользовательские изображения операционной системы

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.6 | 5,3 | Customer |

При использовании пользовательских изображений используйте элементуправления доступа на основе ролей (RBAC), чтобы обеспечить доступ к изображениям только авторизованным пользователям. Используя коллекцию общих образов, вы можете делиться своими образами с другими пользователями, участниками службы или группами конструктора приложений в вашей организации.  Для изображений контейнеров храните их в реестре контейнеров Azure и используете RBAC для обеспечения доступа к изображениям только авторизованных пользователей.  

- [Понять RBAC в Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Понять RBAC для контейнерного реестра](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Как настроить RBAC в Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Обзор коллекции общих образов](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Развертывание инструментов управления конфигурацией для ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.7 | 5.4 | Customer |

Определите и внедрить стандартные конфигурации безопасности для ресурсов Azure с помощью политики Azure. Используйте псевдонимы Azure Policy для создания пользовательских политик для аудита или обеспечения безопасности сетевой конфигурации ресурсов Azure. Вы также можете использовать встроенные определения политики, связанные с вашими конкретными ресурсами.  Кроме того, для развертывания изменений конфигурации можно использовать Azure Automation.

- [Как настроить и управлять политикой Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Как использовать Aliases](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Развертывать инструменты управления конфигурацией для операционных систем

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.8 | 5.4 | Customer |

Azure Automation State Configuration — это служба управления конфигурацией для узлов «Желаемая конфигурация состояния» (DSC) в любом облачном или натерритории. Вы можете легко переносить компьютеры в облачную среду, присваивать им декларативные конфигурации, а также просматривать отчеты, отражающие соответствие каждого компьютера требуемому состоянию. 

- [Подключение компьютеров для управления с помощью службы "Настройка состояния службы автоматизации Azure"](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Внедрение автоматизированного мониторинга конфигурации для ресурсов Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.9 | 5.5 | Customer |

Используйте Центр безопасности Azure для выполнения базовых сканирований ресурсов Azure.  Кроме того, используйте политику Azure для оповещения и аудита конфигураций ресурсов Azure.

- [Как уделать рекомендации в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Внедрение автоматизированного мониторинга конфигурации для операционных систем

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.10 | 5.5 | Customer |

Используйте Центр безопасности Azure для выполнения базовых сканов для настроек ОС и докеров для контейнеров.

- [Общие сведения о рекомендациях центра безопасности Azure в отношении контейнеров](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Надежное управление секретами Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.11 | Версия 13.1 | Customer |

Используйте идентификатор управляемой службы совместно с Azure Key Vault для упрощения и обеспечения секретного управления облачными приложениями.

- [Как интегрироваться с управляемыми идентификаторами Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Как создать Убежище ключей](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Как обеспечить аутентификацию Key Vault с управляемой идентификацией](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Управление идентификаторами безопасно и автоматически

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.12 | 4.1 | Customer |

Используйте управляемые идентификаторы для предоставления службам Azure автоматического управления идентификаторами в Azure AD. Управляемые идентификаторы позволяют аутентифицировать проверку любой службы, которая поддерживает аутентификацию Azure AD, включая Key Vault, без каких-либо учетных данных в коде.

- [Как настроить управляемые идентификаторы](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Устранить непреднамеренное воздействие учетных данных

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 7.13 | 18.1, 18.7 | Customer |

Реализация сканера учетных данных для идентификации учетных данных в коде. Сканер учетных данных также будет способствовать перемещению обнаруженных учетных данных в более безопасные места, такие как Убежище ключей Azure. 

- [Как настроить сканер учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Дальнейшие действия

- Смотрите следующий контроль безопасности: [Защита от вредоносных программ](security-control-malware-defense.md)