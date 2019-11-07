---
title: Настройка среды разработки Azure Red Hat OpenShift | Документация Майкрософт
description: Ниже приведены необходимые условия для работы с Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: Настройка установки Red Hat openshift
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: fa09ed90258a62d37dafeea5f4760e1fabdc210b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581598"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Настройка среды разработки Azure Red Hat OpenShift

Для сборки и запуска Microsoft Azure приложений OpenShift Red Hat необходимо:

* Установите версию 2.0.65 (или более позднюю) Azure CLI (или используйте Azure Cloud Shell).
* Зарегистрируйтесь для `AROGA` функции и связанных с ними поставщиков ресурсов.
* Создайте клиент Azure Active Directory (Azure AD).
* Создайте объект приложения Azure AD.
* Создайте пользователя Azure AD.

Приведенные ниже инструкции покажут все необходимые условия.

## <a name="install-the-azure-cli"></a>Установка Azure CLI

Для Azure Red Hat OpenShift требуется версия 2.0.65 или более поздняя Azure CLI. Если вы уже установили Azure CLI, можно проверить, какая версия установлена, выполнив команду:

```bash
az --version
```

Первая строка выходных данных будет иметь версию CLI, например `azure-cli (2.0.65)`.

Ниже приведены инструкции по [установке Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , если требуется новая установка или обновление.

Кроме того, можно использовать [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). При использовании Azure Cloud Shell не забудьте выбрать среду **Bash** , если вы планируете следовать инструкциям из руководства по [созданию и управлению кластером Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Регистрация поставщиков и компонентов

`Microsoft.ContainerService AROGA` функции, `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` и поставщики `Microsoft.Network` должны быть зарегистрированы в своей подписке вручную перед развертыванием первого кластера Azure Red Hat OpenShift.

Чтобы зарегистрировать эти поставщики и компоненты вручную, используйте следующие инструкции оболочки bash, если вы установили интерфейс командной строки или в сеансе Azure Cloud Shell (bash) в портал Azure:

1. Если у вас несколько подписок Azure, укажите соответствующий идентификатор подписки:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Зарегистрируйте компонент Microsoft. ContainerService АРОГА:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Зарегистрируйте поставщик Microsoft. Storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Зарегистрируйте поставщик Microsoft. COMPUTE:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Зарегистрируйте поставщика Microsoft. Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Зарегистрируйте поставщик Microsoft. Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Зарегистрируйте поставщик Microsoft. KeyVault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Обновите регистрацию поставщика ресурсов Microsoft. ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Создание клиента Azure Active Directory (Azure AD)

Службе Azure Red Hat OpenShift требуется связанный клиент Azure Active Directory (Azure AD), представляющий вашу организацию и ее связь с Майкрософт. Клиент Azure AD позволяет регистрировать, создавать приложения и управлять ими, а также использовать другие службы Azure.

Если у вас нет Azure AD для использования в качестве клиента для кластера Azure Red Hat OpenShift или вы хотите создать клиент для тестирования, следуйте инструкциям в статье [Создание клиента Azure AD для кластера OpenShift для Azure Red Hat](howto-create-tenant.md) , прежде чем продолжить работу с этим руководством. .

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Создание пользователя Azure AD, группы безопасности и объекта приложения

Azure Red Hat OpenShift требуются разрешения для выполнения задач в кластере, например для настройки хранилища. Эти разрешения представлены с помощью [субъекта-службы](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Вам также потребуется создать нового пользователя Active Directory для тестирования приложений, выполняющихся в кластере Azure Red Hat OpenShift.

Следуйте инструкциям в разделе [Создание объекта приложения Azure AD и пользователя](howto-aad-app-configuration.md) для создания субъекта-службы, создание секрета клиента и URL обратного вызова проверки подлинности для вашего приложения, а также создание новой группы безопасности Azure AD и пользователя для доступа к кластеру.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы готовы использовать Azure Red Hat OpenShift!

Воспользуйтесь руководством.
> [!div class="nextstepaction"]
> [Создание кластера Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
