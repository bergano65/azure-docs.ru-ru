---
title: Руководство по созданию кластера Azure Red Hat OpenShift | Документация Майкрософт
description: Узнайте, как создать кластер Microsoft Azure Red Hat OpenShift с помощью Azure CLI.
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/14/2019
ms.openlocfilehash: 4c186787af08a565dc100dfbd79d166688d89d8f
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013438"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Руководство по Создание кластера Azure Red Hat OpenShift

Это руководство представляет первую часть цикла. Из него вы узнаете, как создать кластер Microsoft Azure Red Hat OpenShift с помощью Azure CLI, выполнить его масштабирование, а затем удалить, чтобы очистить ресурсы.

В первой части цикла вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание кластера Azure Red Hat OpenShift

Из этого цикла руководств вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Создание кластера Azure Red Hat OpenShift
> * [Масштабирование кластера Azure Red Hat OpenShift](tutorial-scale-cluster.md)
> * [Удаление кластера Azure Red Hat OpenShift](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Предварительные требования

> [!IMPORTANT]
> Для этого учебника требуется Azure CLI 2.0.65.
>    
> Прежде чем вы сможете использовать Azure Red Hat OpenShift, вам необходимо приобрести как минимум 4 зарезервированных узла приложений Azure Red Hat OpenShift, как описано в разделе о [настройке среды разработки Azure Red Hat OpenShift](howto-setup-environment.md#purchase-azure-red-hat-openshift-application-nodes-reserved-instances).

Перед началом работы с этим руководством выполните следующие действия:

Убедитесь в том, что вы [настроили среду разработки](howto-setup-environment.md), в том числе:
- установили последнюю версию интерфейса командной строки (версию 2.0.65 или более позднюю версию);
- создали клиент, если у вас его еще нет;
- создали объект приложения Azure, если у вас его еще нет;
- Создание группы безопасности
- создали пользователя Active Directory для входа в кластер.

## <a name="step-1-sign-in-to-azure"></a>Шаг 1. Вход в Azure

Если интерфейс Azure CLI запущен локально, откройте оболочку Bash и выполните команду `az login`, чтобы войти в Azure.

```bash
az login
```

 Если у вас есть доступ к нескольким подпискам, выполните команду `az account set -s {subscription ID}`, заменив `{subscription ID}` необходимой подпиской.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Шаг 2. Создание кластера Azure Red Hat OpenShift

В окне командной строки Bash задайте следующие переменные:

> [!IMPORTANT]
> Выберите уникальное имя для кластера (в нижнем регистре) или создание кластера завершится сбоем.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

Выберите расположение для создания кластера. Список регионов Azure, в которых поддерживается OpenShift в Azure, см. в списке [поддерживаемых регионов](supported-resources.md#azure-regions). Например, `LOCATION=eastus`.

```bash
LOCATION=<location>
```

Присвойте параметру `APPID` значение, которое вы сохранили на шаге 5 процедуры [создания регистрации приложения Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-app-registration).  

```bash
APPID=<app ID value>
```

Присвойте параметру GROUPID значение, которое вы сохранили на шаге 10 процедуры [создания группы безопасности Azure AD](howto-aad-app-configuration.md#create-an-azure-ad-security-group).

```bash
GROUPID=<group ID value>
```

Присвойте параметру `SECRET` значение, которое вы сохранили на шаге 8 процедуры [создания секрета клиента](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Параметру `TENANT` присвойте значение идентификатора клиента, которое вы сохранили на шаге 7 процедуры [создание клиента](howto-create-tenant.md#create-a-new-azure-ad-tenant).  

```bash
TENANT=<tenant ID>
```

Создайте группу ресурсов для кластера. Выполните следующую команду из той же оболочки Bash, в которой вы ранее определили переменные:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Необязательно: подключение виртуальной сети кластера к существующей виртуальной сети

Если вам не нужно подключать виртуальную сеть создаваемого кластера к существующей виртуальной сети с помощью пиринга, пропустите этот шаг.

Если пиринг сети находится за пределами подписки по умолчанию, то в этой подписке также понадобится зарегистрировать поставщик Microsoft.ContainerService. Для этого выполните указанную ниже команду в этой подписке. В противном случае если виртуальная сеть, пиринг которой выполняется, находится в той же подписке, шаг регистрации можно пропустить. 

`az provider register -n Microsoft.ContainerService --wait`

Для начала получите идентификатор существующей виртуальной сети. Этот идентификатор имеет такой формат: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Если вы не знаете имя сети или группу ресурсов, к которой относится существующая виртуальная сеть, перейдите на [колонку виртуальной сети](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) и щелкните нужную виртуальную сеть. Откроется страница виртуальной сети, на которой указаны имя сети и группа ресурсов, к которой она принадлежит.

Определите переменную VNET_ID с помощью следующей команды CLI в оболочке BASH:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Например: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Создание кластера

Теперь можно создать новый кластер. Далее будет создан кластер в указанном клиенте Azure AD, указан объект приложения Azure AD и секрет для использования в качестве субъекта безопасности, а также группа безопасности, содержащая участников, у которых есть доступ администратора к кластеру.

Если вы **не выполняете** пиринг кластера к виртуальной сети, используйте следующую команду:

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID
```

Если вы **выполняете** пиринг кластера к виртуальной сети, используйте следующую команду, которая добавит флаг `--vnet-peer`:
 
```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --customer-admin-group-id $GROUPID --vnet-peer $VNET_ID
```

> [!NOTE]
> Если вы получите ошибку с сообщением о том, что имя узла недоступно, это может означать, что указанное имя кластера уже существует. Попробуйте удалить исходную регистрацию приложения и повторить все действия с другим именем кластера из раздела о [регистрации приложения](howto-aad-app-configuration.md#create-an-azure-ad-app-registration), пропустив шаг создания пользователя и группы безопасности.

Через несколько минут операция `az openshift create` выполнится.

### <a name="get-the-sign-in-url-for-your-cluster"></a>Получение URL-адреса входа для кластера

Получите URL-адрес входа для своего кластера, выполнив следующую команду:

```bash
az openshift show -n $CLUSTER_NAME -g $CLUSTER_NAME
```

Найдите `publicHostName` в выходных данных, например: `"publicHostname": "openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io"`.

URL-адрес входа для кластера будет `https://`, за которым следует значение `publicHostName`.  Например, `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`.  Этот URI будет использоваться на следующем шаге как часть URI-перенаправления регистрации приложения.

## <a name="step-3-update-your-app-registration-redirect-uri"></a>Шаг 3. Обновление URI-перенаправления регистрации приложения

Теперь, когда у вас есть URL-адрес входа для кластера, установите URI перенаправления регистрации приложения:

1. Откройте колонку [Регистрация приложений](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).
2. Щелкните объект регистрации своего приложения.
3. Щелкните **Добавить URI перенаправления**.
4. Убедитесь, что для параметра **Тип** установлено значение **Веб** и задайте **URI перенаправления**, используя следующий шаблон: `https://<public host name>/oauth2callback/Azure%20AD`. Например: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io/oauth2callback/Azure%20AD`
5. Нажмите кнопку **Сохранить**

## <a name="step-4-sign-in-to-the-openshift-console"></a>Шаг 4. Войдите в консоль OpenShift

Теперь вы готовы войти в консоль OpenShift для нового кластера. [Веб-консоль OpenShift](https://docs.openshift.com/aro/architecture/infrastructure_components/web_console.html) позволяет визуализировать, просматривать содержимое проектов OpenShift и управлять им.

Вам потребуется новый экземпляр браузера, в кэше которого нет удостоверения, с которым вы обычно входите на портал Azure.

1. Откройте окно *инкогнито* (Chrome) или *InPrivate* (Microsoft Edge).
2. Перейдите к полученному выше URL-адресу входа, например: `https://openshift.xxxxxxxxxxxxxxxxxxxx.eastus.azmosa.io`

Выполните вход с использованием имени пользователя, созданного на шаге 3 процедуры [создания пользователя Azure Active Directory](howto-aad-app-configuration.md#create-a-new-azure-active-directory-user).

Появится диалоговое окно **Запрошенные разрешения**. Выберите **Согласие от имени вашей организации**, а затем щелкните **Принять**.

Итак, вы вошли в консоль кластера.

![Снимок экрана с консолью кластера OpenShift](./media/aro-console.png)

 Узнайте больше [об использовании консоли OpenShift](https://docs.openshift.com/aro/getting_started/developers_console.html) для создания и сборки образов, ознакомившись с [документацией по Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html).

## <a name="step-5-install-the-openshift-cli"></a>Шаг 5. Установка интерфейса командной строки OpenShift

[Интерфейс командной строки OpenShift](https://docs.openshift.com/aro/cli_reference/get_started_cli.html) (или *OC Tools*) предоставляет команды для управления приложениями и служебные программы низкого уровня для взаимодействия с разными компонентами кластера OpenShift.

В консоли OpenShift щелкните вопросительный знак в правом верхнем углу, рядом с именем текущего пользователя, и выберите **Command Line Tools** (Средства командной строки).  Перейдите по ссылке **Latest Release** (Последний выпуск), чтобы скачать и установить поддерживаемые версии интерфейса командной строки OC для Linux, MacOS или Windows.

> [!NOTE]
> Если вы не видите в правом верхнем углу значка с вопросительным знаком, выберите *Service Catalog* (Каталог служб) или *Application Console* (Консоль приложения) в раскрывающемся списке слева вверху.
>
> Кроме того, вы можете использовать прямую ссылку [на скачивание CLI OC](https://www.okd.io/download.html).

Страница **Command Line Tools** (Средства командной строки) содержит команду формата `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Нажмите кнопку *Копировать в буфер обмена*, чтобы скопировать эту команду.  В окне терминала [задайте переменную path](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli), чтобы она включала локальный каталог установки средств OC. Затем войдите в кластер с помощью команды CLI OC, которую скопировали ранее.

Если описанные выше шаги не позволили получить значение токена, получите это значение с помощью `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Дополнительная информация

В этой части руководства вы узнали, как выполнить следующие действия:

> [!div class="checklist"]
> * Создание кластера Azure Red Hat OpenShift

Перейдите к следующему руководству:
> [!div class="nextstepaction"]
> [Масштабирование кластера Azure Red Hat OpenShift](tutorial-scale-cluster.md)
