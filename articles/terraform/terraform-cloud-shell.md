---
title: Руководство. Настройка Azure Cloud Shell для Terraform
description: Используйте Terraform с Azure Cloud Shell, чтобы упростить аутентификацию и конфигурацию шаблона.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: db9edfadbe01edc1ee9df09c284e3895ee11f3d3
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159124"
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
}
```

Cloud Shell передает необходимые значения поставщику `azurerm` посредством переменных среды при использовании любой из команд интерфейса командной строки `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Другие инструменты разработки Cloud Shell

Файлы и состояния оболочки сохраняются в службе хранилища Azure между сеансами Cloud Shell. Используйте [Обозреватель службы хранилища Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer), чтобы скопировать и отправить файлы в Cloud Shell с локального компьютера.

Azure CLI доступен в Cloud Shell и является мощным инструментом для тестирования конфигурации и проверки работы после завершения `terraform apply` или `terraform destroy`.


## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание кластера виртуальных машин с помощью Terraform с использованием реестра модулей](terraform-create-vm-cluster-module.md)
