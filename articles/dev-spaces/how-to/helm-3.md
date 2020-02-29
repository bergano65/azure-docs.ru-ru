---
title: Настройка кластера Azure Dev Spaces для использования Helm 3 (Предварительная версия)
services: azure-dev-spaces
ms.date: 02/28/2020
ms.topic: conceptual
description: Сведения о настройке кластера dev Spaces для использования Helm 3
keywords: Azure Dev Spaces, пространства разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры
ms.openlocfilehash: 9e3f3ff90d36215c386bf1d8b8ec1edd54ebfb6a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202264"
---
# <a name="configure-your-azure-dev-spaces-cluster-to-use-helm-3-preview"></a>Настройка кластера Azure Dev Spaces для использования Helm 3 (Предварительная версия)

Azure Dev Spaces по умолчанию использует Helm 2 для установки пользовательских служб в пространствах разработки в кластере AKS. Можно включить Azure Dev Spaces для использования Helm 3 вместо Helm 2 Установка пользовательских служб в пространствах разработки. Независимо от версии Helm Azure Dev Spaces, используемой для установки пользовательских служб, можно продолжать использовать клиент Helm 2 или 3 для управления своими выпусками в том же кластере.

При включении Helm 3 Azure Dev Spaces ведет себя иначе при установке пользовательских служб в пространствах разработки следующими способами:

* Больше не развертывается в кластере в пространстве имен *аздс* .
* Helm хранит сведения о выпуске в пространстве имен, где установлена служба, а не в пространстве имен *аздс* .
* Сведения о выпуске Helm 3 остаются в пространстве имен, где установлена служба после удаления контроллера.
* Вы можете напрямую взаимодействовать с любым выпуском, управляемым Azure Dev Spaces в кластере с помощью клиента Helm 3.

В этом руководство вы узнаете, как включить Helm 3 для Azure Dev Spaces для установки пользовательских служб в пространствах разработки.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="before-you-begin"></a>Перед началом

### <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Установленный Azure CLI][azure-cli].

### <a name="register-the-helm3preview-preview-feature"></a>Регистрация функции предварительной версии Helm3Preview

Чтобы разрешить Azure Dev Spaces использовать Helm 3 для установки пользовательских служб в пространствах разработки, сначала включите флаг функции *Helm3Preview* в подписке с помощью команды *AZ Feature Register* :

> [!WARNING]
> В любом кластере AKS, включенном Azure Dev Spaces с флагом функции *Helm3Preview* , будет использоваться Предварительная версия этого интерфейса. Чтобы продолжить включение полностью поддерживаемых Azure Dev Spaces в кластерах AKS, не включайте предварительные версии функций в производственных подписках. Используйте отдельную тестовую или новую подписку Azure для тестирования предварительных версий функций.

```azure-cli
az feature register --namespace Microsoft.DevSpaces --name Helm3Preview
```

Для завершения регистрации потребуется несколько минут. Проверьте состояние регистрации с помощью команды *AZ Feature показывать* :

```azure-cli
az feature show --namespace Microsoft.DevSpaces --name Helm3Preview
```

После *регистрации* *состояния* обновите регистрацию *Microsoft. девспацес* с помощью команды *AZ Provider Register*:

```azure-cli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="limitations"></a>Ограничения

При использовании этой функции в режиме предварительной версии действуют следующие ограничения.

* Эту функцию нельзя использовать в кластерах AKS с существующими рабочими нагрузками. Необходимо создать новый кластер AKS.

## <a name="create-your-cluster"></a>Создание кластера

Создайте новый кластер AKS в регионе с этой функцией предварительной версии. Приведенные ниже команды создают группу ресурсов с именем *MyResourceGroup* и новый кластер AKS с именем *мякс* в *юго-центральном регионе США* :

```azure-cli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces"></a>Включить Azure Dev Spaces

Используйте команду *use-dev-Spaces* , чтобы включить пространства разработки в кластере AKS и следовать инструкциям. Приведенная ниже команда включает пространства разработки в кластере *мякс* в группе *MyResourceGroup* и создает пространство разработки по умолчанию.

```cmd
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

## <a name="verify-dev-spaces-is-running-helm-3"></a>Проверка того, что пространства разработки работают под Helm 3

Убедитесь, что кассовый ящик не запущен, выполнив список развертываний в пространстве имен *аздс* :

```cmd
kubectl get deployment -n azds
```

Подтвердите, что *развертывание* не выполняется в пространстве имен аздс. Например:

```console
$ kubectl get deployments -n azds
NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
azds-webhook-deployment   2/2     2            2           39m
traefik                   1/1     1            1           39m
```

## <a name="next-steps"></a>Следующие шаги

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-quickstart]


[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[team-quickstart]: ../quickstart-team-development.md