---
title: Настройка экземпляра и проверки подлинности (CLI)
titleSuffix: Azure Digital Twins
description: См. раздел Настройка экземпляра службы Digital двойников с помощью интерфейса командной строки.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bb5ff0bf7347b87fdc3a103a03f9ff58279a367d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320781"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Настройка экземпляра и проверки подлинности Azure Digital двойников (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

В этой статье описаны действия по **настройке нового экземпляра Azure Digital двойников**, включая создание экземпляра и настройку проверки подлинности. По завершении работы с этой статьей у вас будет готовый экземпляр Azure Digital двойников для начала программирования.

Эта версия этой статьи выполняет эти действия вручную, по одной с помощью интерфейса командной строки.
* Чтобы выполнить эти действия вручную с помощью портал Azure, см. версию портала этой статьи: инструкции по [*настройке экземпляра и проверки подлинности (портал)*](how-to-set-up-instance-portal.md).
* Чтобы выполнить автоматическую установку с помощью примера скрипта развертывания, см. статью [*как настроить экземпляр и проверку подлинности (в скрипте)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Создание сеанса Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Создание экземпляра Digital двойников для Azure

В этом разделе вы **создадите новый экземпляр Azure Digital двойников** с помощью команды Cloud Shell. Необходимо указать:
* Группа ресурсов для развертывания. Если у вас еще нет имеющейся группы ресурсов, вы можете создать ее сейчас с помощью следующей команды:
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Регион для развертывания. Чтобы узнать, какие регионы поддерживают Azure Digital двойников, посетите страницу [*продукты Azure, доступные по регионам*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Имя экземпляра. Имя нового экземпляра должно быть уникальным в пределах региона для вашей подписки (это означает, что если в вашей подписке есть другой экземпляр Azure Digital двойников в регионе, где уже используется выбранное имя, вам будет предложено выбрать другое имя).

Используйте следующие значения в следующей команде, чтобы создать экземпляр:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Проверка успешности и получение важных значений

Если экземпляр успешно создан, результат в Cloud Shell выглядит примерно так, выводя сведения о созданном ресурсе:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="командное окно с успешным созданием группы ресурсов и экземпляра Azure Digital двойников":::

Обратите внимание на имя *узла*, *имени*и группы Azure Digital *двойников из выходных* данных. Это все важные значения, которые могут потребоваться при продолжении работы с вашим экземпляром Azure Digital двойников для настройки проверки подлинности и связанных ресурсов Azure. Если другие пользователи будут программироваться на экземпляре, вы должны использовать эти значения совместно с ними.

> [!TIP]
> Эти свойства и все свойства экземпляра можно просмотреть в любое время, запустив `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Теперь у вас есть экземпляр Azure Digital двойников, готовый к работе. Далее вы получите соответствующие разрешения пользователя Azure для управления им.

## <a name="set-up-user-access-permissions"></a>Настройка разрешений доступа пользователей

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Используйте следующую команду, чтобы назначить роль (она должна выполняться пользователем с [достаточными разрешениями](#prerequisites-permission-requirements) в подписке Azure). Команда требует передачи *имени участника-пользователя* в учетной записи Azure AD для пользователя, которому должна быть назначена роль. В большинстве случаев это будет соответствовать электронной почте пользователя в учетной записи Azure AD.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Результат этой команды выводит сведения о созданном назначении роли.

> [!NOTE]
> Если эта команда возвращает ошибку, сообщающую, что CLI **не может найти пользователя или субъект-службу в базе данных Graph**:
>
> Назначьте роль с помощью *идентификатора объекта* пользователя. Это может произойти для пользователей в личных [учетных записях Майкрософт (MSAS)](https://account.microsoft.com/account). 
>
> Используйте [страницу портал Azure Azure Active Directory пользователей](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) , чтобы выбрать учетную запись пользователя и открыть сведения о ней. Скопируйте *ObjectID*пользователя:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="командное окно с успешным созданием группы ресурсов и экземпляра Azure Digital двойников" lightbox="media/includes/user-id.png":::
>
> Затем повторите команду списка назначений ролей, используя *идентификатор объекта* пользователя для приведенного `assignee` выше параметра.

### <a name="verify-success"></a>Проверка успешного выполнения

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Теперь у вас есть экземпляр Azure Digital двойников, который готов к работе и ему назначены разрешения для управления им. Далее предстоит настроить разрешения для клиентского приложения на доступ к нему.

## <a name="set-up-access-permissions-for-client-applications"></a>Настройка разрешений доступа для клиентских приложений

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Чтобы создать регистрацию приложения, необходимо указать идентификаторы ресурсов для API цифровых двойников Azure и базовые разрешения для API.

В рабочем каталоге создайте новый файл и введите следующий фрагмент JSON, чтобы настроить следующие сведения: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Сохраните этот файл как _**manifest.js**_.

> [!NOTE] 
> В некоторых местах `https://digitaltwins.azure.net` для идентификатора приложения Azure Digital двойников вместо GUID можно использовать удобную для восприятия строку `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Например, многие примеры по всему этому набору документации используют проверку подлинности с помощью библиотеки MSAL, а для этого можно использовать понятную строку. Однако на этом этапе создания регистрации приложения требуется форма идентификатора GUID, как показано выше. 

Затем вы отправите этот файл в Cloud Shell. В окне Cloud Shell щелкните значок "Отправить/скачать файлы" и выберите "Отправить".

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="командное окно с успешным созданием группы ресурсов и экземпляра Azure Digital двойников":::
Перейдите к только что созданному *manifest.js* и нажмите кнопку "Открыть".

Затем выполните следующую команду, чтобы создать регистрацию приложения с *общедоступным клиентским или собственным (мобильный & Desktop)* URL-адресом ответа `http://localhost` . При необходимости замените заполнители:

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Ниже приведен фрагмент выходных данных этой команды, в котором отображаются сведения о созданной регистрации:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="командное окно с успешным созданием группы ресурсов и экземпляра Azure Digital двойников":::

### <a name="verify-success"></a>Проверка успешного выполнения

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Затем убедитесь, что параметры, отправленные с *manifest.js* , были правильно установлены для регистрации. Для этого в строке меню выберите *Манифест* , чтобы просмотреть код манифеста регистрации приложения. Прокрутите окно кода вниз и найдите поля из *manifest.jsв* разделе `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Получение важных значений

Затем в строке меню выберите *Обзор* , чтобы просмотреть сведения о регистрации приложения:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="командное окно с успешным созданием группы ресурсов и экземпляра Azure Digital двойников":::

Запишите *идентификатор приложения (клиента)* и *идентификатор каталога (клиента)* , показанные на **странице.** Эти значения потребуются позже для [проверки подлинности клиентского приложения с помощью API цифровых двойников Azure](how-to-authenticate-client.md). Если вы не являетесь пользователем, который будет писать код для таких приложений, вам потребуется поделиться этими значениями с пользователем, который будет иметь значение.

### <a name="other-possible-steps-for-your-organization"></a>Другие возможные действия для вашей организации

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вытестируйте отдельные REST API вызовы в экземпляре с помощью команд CLI Azure Digital двойников: 
* [AZ DT Справочник](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
* [*Практическое руководство. Использование CLI для Azure Digital Twins*](how-to-use-cli.md)

Или см. раздел как подключить клиентское приложение к экземпляру, написав код проверки подлинности клиентского приложения:
* [*Пошаговое руководство. Написание кода проверки подлинности приложения*](how-to-authenticate-client.md)