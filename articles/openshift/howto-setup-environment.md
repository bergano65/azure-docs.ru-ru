---
title: Настройка среды разработки Azure Red Hat OpenShift
description: Вот предпосылки для работы с Microsoft Azure Red Hat OpenShift.
keywords: красная шляпа openshift настройки
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477040"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Настройка среды разработки Azure Red Hat OpenShift

Для создания и запуска приложений Microsoft Azure Red Hat OpenShift необходимо:

* Установите версию 2.0.65 (или выше) ClI Azure (или используйте лазурную оболочку).
* Зарегистрируйтесь для поставщиков функций `AROGA` и связанных с ними ресурсов.
* Создайте для арендатора Active Directory Azure (Azure AD).
* Создайте объект приложения Azure AD.
* Создайте пользователя Azure AD.

Следующие инструкции будут вас через все эти предпосылки.

## <a name="install-the-azure-cli"></a>Установка Azure CLI

Azure Red Hat OpenShift требует версии 2.0.65 или выше Azure CLI. Если вы уже установили Azure CLI, вы можете проверить, какая версия у вас есть, запустив:

```azurecli
az --version
```

Первая строка вывода будет иметь версию `azure-cli (2.0.65)`CLI, например.

Вот инструкции по [установке Azure CLI,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) если вам требуется новая установка или обновление.

Кроме того, можно использовать [облачную оболочку Azure.](https://docs.microsoft.com/azure/cloud-shell/overview) При использовании облачной оболочки Azure не забудьте выбрать среду **Bash,** если вы планируете следовать вместе с [серией учебников по созданию и управлению кластером Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="register-providers-and-features"></a>Регистрация поставщиков и функций

`Microsoft.Solutions`Функция, `Microsoft.ContainerService AROGA` `Microsoft.Storage`и `Microsoft.Network` провайдеры должны быть зарегистрированы в вашей подписке вручную перед развертыванием первого кластера Azure Red Hat OpenShift. `Microsoft.Compute` `Microsoft.KeyVault`

Чтобы зарегистрировать эти поставщики и функции вручную, используйте следующие инструкции из оболочки Bash, если вы установили CLI, или из сеанса Azure Cloud Shell (Bash) на портале Azure:

1. Если у вас есть несколько подписок Azure, укажите соответствующий идентификатор подписки:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Зарегистрируйте функцию Microsoft.ContainerService AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Зарегистрируйтесь в корпорации Майкрософт:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Зарегистрируйтесь в провайдере Microsoft.Compute:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Зарегистрируйтесь в корпорации Microsoft.Solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Зарегистрируйтесь в провайдере Microsoft.Network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Зарегистрируйтесь в корпорации Microsoft.KeyVault:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Обновите регистрацию поставщика ресурсов Microsoft.ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Создание элемента для обита в каталоге Azure Active Directory (Azure AD)

Служба Azure Red Hat OpenShift требует сяводом для клиента, связанного с Active Directory (Azure AD), который представляет вашу организацию и ее связь с корпорацией Майкрософт. Ваш aD-арендатор Azure позволяет регистрировать, создавать и управлять приложениями, а также использовать другие службы Azure.

Если у вас нет Azure AD для использования в качестве арендатора для кластера Azure Red Hat OpenShift или вы хотите создать арендатора для тестирования, следуйте инструкциям в [создании ad-нанимателя Azure Для кластера Azure Red Hat OpenShift,](howto-create-tenant.md) прежде чем продолжить работу с этим руководством.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Создание пользователя Azure AD, группы безопасности и объекта приложения

Azure Red Hat OpenShift требует разрешений для выполнения задач в кластере, таких как настройка хранилища. Эти разрешения представлены через [основной службы.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) Также необходимо создать нового пользователя Active Directory для тестирования приложений, работающих в кластере Azure Red Hat OpenShift.

Следуйте инструкциям в [создании объекта приложения Azure AD и пользователя](howto-aad-app-configuration.md) для создания основного сервиса, создания URL-адреса обратного вызова клиента и проверки подлинности для вашего приложения и создания новой группы безопасности Azure AD и пользователя для доступа к кластеру.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы готовы использовать Azure Red Hat OpenShift!

Попробуйте учебник:
> [!div class="nextstepaction"]
> [Создание кластера Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
