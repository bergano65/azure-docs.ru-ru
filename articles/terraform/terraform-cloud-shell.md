---
title: Руководство. Настройка Azure Cloud Shell для Terraform
description: Из этого руководства вы узнаете, как использовать Terraform с Azure Cloud Shell для упрощения аутентификации и конфигурации шаблона.
keywords: Azure DevOps, Terraform, Cloud Shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945333"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Руководство по Настройка Azure Cloud Shell для Terraform

Terraform хорошо работает из командной строки Bash в macOS, Windows или Linux. Выполнение конфигураций Terraform в Bash [Azure Cloud Shell](/azure/cloud-shell/overview) имеет ряд уникальных преимуществ. В этом руководстве показано, как написать сценарии Terraform, которые развертываются в Azure с помощью Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Автоматическая настройка учетных данных

Модуль Terraform установлен и сразу же доступен в Cloud Shell. Сценарии Terraform выполняют аутентификацию в Azure при входе в Cloud Shell для управления инфраструктурой без дополнительной настройки. Автоматическая проверка подлинности обходит два ручных процесса:
- Создание субъекта-службы Azure Active Directory
- Настройка переменных поставщика Terraform для Azure


## <a name="use-modules-and-providers"></a>Использование модулей и поставщиков

Для доступа к ресурсам Azure и их изменения модулям Azure Terraform требуются учетные данные. Чтобы использовать модули Terraform в Cloud Shell, добавьте следующий код:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell передает необходимые значения поставщику `azurerm` посредством переменных среды при использовании любой из команд интерфейса командной строки `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Другие инструменты разработки Cloud Shell

Файлы и состояния оболочки сохраняются в службе хранилища Azure между сеансами Cloud Shell. Используйте [Обозреватель службы хранилища Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer), чтобы скопировать и отправить файлы в Cloud Shell с локального компьютера.

Azure CLI доступен в Cloud Shell и является мощным инструментом для тестирования конфигурации и проверки работы после завершения `terraform apply` или `terraform destroy`.


## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание кластера виртуальных машин с помощью Terraform с использованием реестра модулей](terraform-create-vm-cluster-module.md)