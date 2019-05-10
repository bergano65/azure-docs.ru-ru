---
title: Настройте среду разработки Azure Red Hat OpenShift | Документация Майкрософт
description: Ниже приведены предварительные требования для работы с Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: red hat openshift Настройка
author: TylerMSFT
ms.author: twhitney
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: 3c265d6695af7ba1bc5833db59966a626cb29cb9
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65416076"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Настройте среду разработки Azure Red Hat OpenShift

Для создания и запуска приложений Microsoft Azure Red Hat OpenShift, вам потребуется:

* Приобрести Azure зарезервированные экземпляры виртуальных машин.
* Установите версию 2.0.64 (или более поздней версии) из командной строки Azure (или используйте Azure Cloud Shell).
* Зарегистрируйтесь для `openshiftmanagedcluster` функция и поставщики связанный ресурс.
* Создание клиента Azure Active Directory (Azure AD).
* Создайте объект приложения Azure AD.
* Создание пользователя Azure AD.

Приведенные ниже инструкции поможет выполнить все эти необходимые компоненты.

## <a name="purchase-azure-virtual-machine-reserved-instances"></a>Приобрести Azure зарезервированные экземпляры виртуальных машин

Перед тем как использовать Azure Red Hat OpenShift, вам потребуется приобрести Azure зарезервированные экземпляры виртуальных машин.

Если вы являетесь клиентом Azure, здесь как [покупки виртуальной машины Azure зарезервированные экземпляры](https://aka.ms/openshift/buy). Резервирование позволяет сократить ваши расходы по предварительной оплаты полностью управляемых служб Azure. Ссылаться на [ *Каковы резервирования Azure* ](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations) получить дополнительные сведения о резервировании и как они помочь сэкономить денежные средства.

Если вы не являетесь клиентом Azure [связаться с отделом продаж](https://aka.ms/openshift/contact-sales) и заполните форму продаж в нижней части страницы, чтобы начать процесс.

## <a name="install-the-azure-cli"></a>Установка Azure CLI

Azure Red Hat OpenShift требуется версия 2.0.64 или более поздней версии интерфейса командной строки Azure. Если вы уже установили Azure CLI, можно проверить версию, выполните:

```bash
az --version
```

Первая строка выходных данных будет иметь версию интерфейса командной строки, например `azure-cli (2.0.64)`.

Ниже приведены инструкции для [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Если требуется новая установка или обновление.

Кроме того, можно использовать [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Если вы используете Azure Cloud Shell, не забудьте выбрать **Bash** среды, если вы планируете поработать с [Создание и управление кластером Azure Red Hat OpenShift](tutorial-create-cluster.md) серии руководств.

## <a name="register-providers-and-features"></a>Регистрация поставщиков и компоненты

`Microsoft.ContainerService openshiftmanagedcluster` Функции `Microsoft.Solutions`, и `Microsoft.Network` поставщики должны быть зарегистрированы в подписку вручную, перед развертыванием первого кластера Azure Red Hat OpenShift.

Чтобы вручную зарегистрировать эти поставщики и функции, используйте приведенные ниже инструкции, из оболочки Bash, если вы уже установили интерфейс командной строки или из сеанса Azure Cloud Shell (оболочка) на портале Azure:.
1. Если у вас несколько подписок Azure, укажите идентификатор соответствующую подписку:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

2. Зарегистрируйтесь для использования функции openshiftmanagedcluster Microsoft.ContainerService:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

3. Зарегистрируйте поставщик Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

4. Регистрация поставщика Microsoft.Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

5. Зарегистрируйте поставщик Microsoft.KeyVault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

6. Обновите регистрацию поставщика ресурсов Microsoft.ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Создание клиента Azure Active Directory (Azure AD)

Для службы Azure Red Hat OpenShift требуется связанный клиент Azure Active Directory (Azure AD), представляющий вашей организации и ее связи в корпорацию Майкрософт. Клиент Azure AD позволяет зарегистрировать, сборки и управлять приложениями, а также использовать другие службы Azure.

Если у вас нет Azure AD для использования в качестве клиента для кластера Azure Red Hat OpenShift, или вы хотите создать клиент для тестирования, следуйте инструкциям в [создание клиента Azure AD для кластера Azure Red Hat OpenShift](howto-create-tenant.md) перед Продолжение с помощью этого руководства.

## <a name="create-an-azure-ad-application-object-and-user"></a>Создание объекта приложения Azure AD и пользователя

Azure Red Hat OpenShift, требуются разрешения для выполнения задач в кластере, например при настройке хранилища. Эти разрешения, представлены через [субъекта-службы](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) и создаются при регистрации приложения Azure AD, представляющий рабочей нагрузки, которые вы планируете размещать в Azure Red Hat OpenShift. Кроме того, потребуется создать нового пользователя Active Directory для тестирования приложений, выполняющихся в кластере Azure Red Hat OpenShift.

Следуйте инструкциям в [создать объект приложения Azure AD и пользователя](howto-aad-app-configuration.md) о создании субъекта-службы, создания секрета и проверки подлинности обратного вызова URL-адрес клиента для приложения и создания нового пользователя Active Directory для тестирования.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы готовы использовать Azure Red Hat OpenShift!

Ознакомьтесь с учебником:
> [!div class="nextstepaction"]
> [Создание кластера Azure Red Hat OpenShift](tutorial-create-cluster.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli