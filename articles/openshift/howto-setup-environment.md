---
title: Настройте среду разработки Azure Red Hat OpenShift | Документация Майкрософт
description: Ниже приведены предварительные требования для работы с Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: red hat openshift Настройка
author: jimzim
ms.author: jzim
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: openshift
manager: jeconnoc
ms.openlocfilehash: f0ef421d7954aa33cf69e7de2f4902a86ed8b580
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306402"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Настройка среды разработки Azure Red Hat OpenShift

Для создания и запуска приложений Microsoft Azure Red Hat OpenShift, вам потребуется:

* Приобрести Azure зарезервированные экземпляры виртуальных машин.
* Установите версию 2.0.65 (или более поздней версии) из командной строки Azure (или используйте Azure Cloud Shell).
* Зарегистрируйтесь для `openshiftmanagedcluster` функция и поставщики связанный ресурс.
* Создание клиента Azure Active Directory (Azure AD).
* Создайте объект приложения Azure AD.
* Создание пользователя Azure AD.

Приведенные ниже инструкции поможет выполнить все эти необходимые компоненты.

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>Приобрести зарезервированные экземпляры Azure Red Hat OpenShift приложения узлов

Перед тем как использовать Azure Red Hat OpenShift, вам потребуется приобрести не менее 4 узлов приложения защищены Azure Red Hat OpenShift, после чего вы сможете Подготовка кластеров.

Если вы являетесь клиентом Azure, [приобрести Azure Red Hat OpenShift зарезервированные экземпляры](https://aka.ms/openshift/buy) через портал Azure. После приобретения, подписки будут активированы в течение 24 часов.

Если вы не являетесь клиентом Azure [связаться с отделом продаж](https://aka.ms/openshift/contact-sales) и заполните форму продаж в нижней части страницы, чтобы начать процесс.

Ссылаться на [странице с ценами Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) Дополнительные сведения.

## <a name="install-the-azure-cli"></a>Установка Azure CLI

Azure Red Hat OpenShift требуется версия 2.0.65 или более поздней версии интерфейса командной строки Azure. Если вы уже установили Azure CLI, можно проверить версию, выполните:

```bash
az --version
```

Первая строка выходных данных будет иметь версию интерфейса командной строки, например `azure-cli (2.0.65)`.

Ниже приведены инструкции для [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Если требуется новая установка или обновление.

Кроме того, можно использовать [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Если вы используете Azure Cloud Shell, не забудьте выбрать **Bash** среды, если вы планируете поработать с [Создание и управление кластером Azure Red Hat OpenShift](tutorial-create-cluster.md) серии руководств.

## <a name="register-providers-and-features"></a>Регистрация поставщиков и компоненты

`Microsoft.ContainerService openshiftmanagedcluster` Функции `Microsoft.Solutions`, и `Microsoft.Network` поставщики должны быть зарегистрированы в подписку вручную, перед развертыванием первого кластера Azure Red Hat OpenShift.

Чтобы вручную зарегистрировать эти поставщики и функции, используйте приведенные ниже инструкции, из оболочки Bash, если вы уже установили интерфейс командной строки или из сеанса Azure Cloud Shell (оболочка) на портале Azure:

1. Если у вас несколько подписок Azure, укажите идентификатор соответствующую подписку:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Зарегистрируйтесь для использования функции openshiftmanagedcluster Microsoft.ContainerService:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n openshiftmanagedcluster
    ```

1. Зарегистрируйте поставщик Microsoft.Storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Зарегистрируйте поставщик Microsoft.Compute:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Зарегистрируйте поставщик Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Регистрация поставщика Microsoft.Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Зарегистрируйте поставщик Microsoft.KeyVault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Обновите регистрацию поставщика ресурсов Microsoft.ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Создание клиента Azure Active Directory (Azure AD)

Для службы Azure Red Hat OpenShift требуется связанный клиент Azure Active Directory (Azure AD), представляющий вашей организации и ее связи в корпорацию Майкрософт. Клиент Azure AD позволяет зарегистрировать, сборки и управлять приложениями, а также использовать другие службы Azure.

Если у вас нет Azure AD для использования в качестве клиента для кластера Azure Red Hat OpenShift, или вы хотите создать клиент для тестирования, следуйте инструкциям в [создание клиента Azure AD для кластера Azure Red Hat OpenShift](howto-create-tenant.md) перед Продолжение с помощью этого руководства.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Создание пользователя, группы безопасности и приложения Azure AD объект

Azure Red Hat OpenShift, требуются разрешения для выполнения задач в кластере, например при настройке хранилища. Эти разрешения, представлены через [субъекта-службы](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Кроме того, потребуется создать нового пользователя Active Directory для тестирования приложений, выполняющихся в кластере Azure Red Hat OpenShift.

Следуйте инструкциям в [создать объект приложения Azure AD и пользователя](howto-aad-app-configuration.md) Чтобы создать субъект-службу, создать секрет и проверки подлинности обратного вызова URL-адрес клиента для приложения и создать новую группу безопасности Azure AD и пользователя для доступа к кластер.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы готовы использовать Azure Red Hat OpenShift!

Ознакомьтесь с учебником:
> [!div class="nextstepaction"]
> [Создание кластера Azure Red Hat OpenShift](tutorial-create-cluster.md).

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
