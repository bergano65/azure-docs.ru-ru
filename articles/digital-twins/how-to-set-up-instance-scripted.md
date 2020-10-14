---
title: Настройка экземпляра и проверки подлинности (с использованием скрипта)
titleSuffix: Azure Digital Twins
description: Сведения о настройке экземпляра службы Digital двойников см. в разделе Создание сценария автоматического развертывания.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4e8e26f73aef155e4194ca7a4178827b6a7366fb
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047426"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Настройка экземпляра Azure Digital двойников и проверки подлинности (с помощью сценария)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

В этой статье описаны действия по **настройке нового экземпляра Azure Digital двойников**, включая создание экземпляра и настройку проверки подлинности. По завершении работы с этой статьей у вас будет готовый экземпляр Azure Digital двойников для начала программирования.

Эта версия этой статьи завершает эти действия, запуская [пример **скрипта автоматического развертывания** ](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) , который упрощает процесс. 
* Для просмотра действий CLI вручную, выполняемых сценарием в фоновом режиме, см. версию CLI этой статьи: [*инструкции по настройке экземпляра и проверки подлинности (CLI)*](how-to-set-up-instance-cli.md).
* Чтобы просмотреть действия вручную в соответствии с портал Azure, см. версию портала этой статьи: инструкции по [*настройке экземпляра и проверки подлинности (портал)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="prerequisites-download-the-script"></a>Предварительные требования: скачивание скрипта

Пример скрипта написан на PowerShell. Она входит в [**примеры цифровых двойников Azure**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), которые можно загрузить на компьютер, перейдя по этой ссылке и нажав кнопку *скачать ZIP-файл* под заголовком.

При этом пример проекта будет скачан на компьютер как _**Azure_Digital_Twins_samples.zip**_. Перейдите в папку на компьютере и распакуйте ее, чтобы извлечь файлы.

В распакованной папке скрипт развертывания находится в каталоге _Azure_Digital_Twins_samples > скрипты > **deploy.ps1** _.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Выполнение скрипта развертывания

В этой статье используется пример кода Azure Digital двойников для развертывания экземпляра цифрового двойников Azure и автоматической проверки подлинности. Его также можно использовать в качестве отправной точки для написания собственных взаимодействий со сценариями.

Ниже приведены действия по запуску скрипта развертывания в Cloud Shell.
1. Перейдите в окно [Azure Cloud Shell](https://shell.azure.com/) в браузере. Выполните вход с помощью следующей команды:
    ```azurecli
    az login
    ```
    Если в CLI можно запустить браузер по умолчанию, откроется браузер со страницей входа. В противном случае самостоятельно откройте в браузере страницу *https://aka.ms/devicelogin* и введите код авторизации, отображаемый в терминале.
 
2. На панели значков Cloud Shell убедитесь, что Cloud Shell настроена для запуска версии PowerShell.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Окно Cloud Shell, в котором отображается выбор версии PowerShell&quot;:::

1. Выберите значок &quot;Отправить/скачать файлы" и нажмите кнопку "Отправить".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Окно Cloud Shell, в котором отображается выбор версии PowerShell&quot;:::

1. Выберите значок &quot;Отправить/скачать файлы":::

    Перейдите в файл _**deploy.ps1**_ на компьютере (в _Azure_Digital_Twins_samples > сценарии > **deploy.ps1** _) и нажмите кнопку "Открыть". Файл будет отправлен в Cloud Shell, чтобы его можно было запустить в окне Cloud Shell.

4. Запустите сценарий, отправив `./deploy.ps1` команду в окне Cloud Shell. (Напомним, что для вставки в Cloud Shell можно использовать **сочетание клавиш CTRL + SHIFT + v** в Windows и Linux или **CMD + SHIFT + v** в macOS. Также можно использовать контекстное меню.)

    ```azurecli
    ./deploy.ps1
    ```

    Так как сценарий выполняется с помощью действий автоматической установки, вам будет предложено передать следующие значения:
    * Для экземпляра: *идентификатор подписки* Azure для использования.
    * Для экземпляра: *Расположение* , в котором вы хотите развернуть экземпляр. Чтобы узнать, какие регионы поддерживают Azure Digital двойников, посетите страницу [*продукты Azure, доступные по регионам*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Для экземпляра: имя *группы ресурсов* . Вы можете использовать существующую группу ресурсов или ввести новое имя, чтобы создать его.
    * Для экземпляра: *имя* вашего экземпляра Digital двойников для Azure. Имя нового экземпляра должно быть уникальным в пределах региона для вашей подписки (это означает, что если в вашей подписке есть другой экземпляр Azure Digital двойников в регионе, где уже используется выбранное имя, вам будет предложено выбрать другое имя).
    * Для регистрации приложения: *Отображаемое имя приложения Azure AD* , связываемое с регистрацией. Регистрация приложения — это место, где вы настраиваете разрешения на доступ к [API-интерфейсам цифровых двойников Azure](how-to-use-apis-sdks.md). Позже клиентское приложение будет проходить проверку подлинности при регистрации приложения, и в результате ему будут предоставлены настроенные разрешения на доступ к API.
    * Для регистрации приложения: *URL-адрес ответа приложения Azure AD* для приложения Azure AD. Используйте команду `http://localhost`. Сценарий настроит *общедоступный клиентский/собственный (мобильный & Настольный)* универсальный код ресурса (URI).

Скрипт создаст экземпляр Azure Digital двойников, назначьте пользователю Azure роль *владельца Azure Digital двойников (Предварительная версия)* на экземпляре и настройте регистрацию приложения Azure AD для использования в клиентском приложении.

>[!NOTE]
>В настоящее время существует **известная ошибка** установки сценариев, в которой некоторые пользователи (особенно пользователи, находящиеся в личных [учетных записях Майкрософт (MSAS)](https://account.microsoft.com/account)) могут найти **назначение ролей для _владельца Azure Digital двойников (Предварительная версия)_ не создано**.
>
>Вы можете проверить назначение ролей в разделе [*Проверка назначения роли пользователя*](#verify-user-role-assignment) далее в этой статье. при необходимости настройте назначение ролей вручную с помощью [портал Azure](how-to-set-up-instance-portal.md#set-up-user-access-permissions) или [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Дополнительные сведения об этой неполадке см. [*в разделе Устранение неполадок: известные проблемы в Azure Digital двойников*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

Ниже приведен фрагмент журнала вывода из скрипта:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Окно Cloud Shell, в котором отображается выбор версии PowerShell&quot;:::

1. Выберите значок &quot;Отправить/скачать файлы" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Если сценарий завершается успешно, окончательная печать будет говорить `Deployment completed successfully` . В противном случае выполняйте сообщение об ошибке и повторно запустите скрипт. Он обходит уже выполненные действия и снова запросит входные данные в том месте, где вы остановились.

После завершения скрипта теперь у вас есть экземпляр Azure Digital двойников, готовый к управлению с разрешениями на управление и устанавливающие разрешения на доступ к нему для клиентского приложения.

> [!NOTE]
> В настоящее время сценарий назначает требуемую роль управления в Azure Digital двойников (*Azure Digital двойников Owner (Предварительная версия)*) тому же пользователю, который запускает скрипт из Cloud Shell. Если вам нужно назначить эту роль другому пользователю, который будет управлять экземпляром, это можно сделать сейчас с помощью портал Azure ([инструкции](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) или CLI ([инструкции](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

## <a name="collect-important-values"></a>Получение важных значений

Существует несколько важных значений ресурсов, настроенных сценарием, которые могут потребоваться при продолжении работы с экземпляром Digital двойников для Azure. В этом разделе для получения этих значений будет использоваться [портал Azure](https://portal.azure.com) . Их следует сохранить в надежном месте или вернуться к этому разделу, чтобы найти их позже по мере необходимости.

Если другие пользователи будут программироваться на экземпляре, эти значения также следует использовать вместе с ними.

### <a name="collect-instance-values"></a>Получение значений экземпляров

В [портал Azure](https://portal.azure.com)найдите экземпляр Azure Digital двойников, выполнив поиск по имени своего экземпляра на панели поиска портала.

При выборе откроется страница *обзора* экземпляра. Запомните его *имя*, *группу ресурсов*и *имя узла*. Эти данные могут потребоваться позже для обнаружения экземпляра и подключения к нему.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Окно Cloud Shell, в котором отображается выбор версии PowerShell&quot;:::

1. Выберите значок &quot;Отправить/скачать файлы":::

### <a name="collect-app-registration-values"></a>Получение значений регистрации приложения 

Регистрация приложения состоит из двух важных значений, которые потребуются позже для [написания кода проверки подлинности клиентского приложения для API-интерфейсов Azure Digital двойников](how-to-authenticate-client.md). 

Чтобы найти их, перейдите по [этой ссылке](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) , чтобы перейти на страницу обзора регистрации приложений Azure AD в портал Azure. На этой странице отображаются все регистрации приложений, созданные в вашей подписке.

Вы должны увидеть только что созданную регистрацию приложения в этом списке. Выберите его, чтобы открыть подробные сведения:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Окно Cloud Shell, в котором отображается выбор версии PowerShell&quot;:::

1. Выберите значок &quot;Отправить/скачать файлы":::

Запишите *идентификатор приложения (клиента)* и *идентификатор каталога (клиента)* , показанные на **странице.** Если вы не являетесь пользователем, который будет писать код для клиентских приложений, вам потребуется поделиться этими значениями с пользователем, который будет иметь значение.

## <a name="verify-success"></a>Проверка успешного выполнения

Если вы хотите проверить создание ресурсов и разрешений, настроенных сценарием, можно просмотреть их в [портал Azure](https://portal.azure.com).

Если вы не можете проверить успешность какого бы то ни было действия, повторите шаг. Эти действия можно выполнить по отдельности с помощью [портал Azure](how-to-set-up-instance-portal.md) или инструкций [CLI](how-to-set-up-instance-cli.md) .

### <a name="verify-instance"></a>Проверить экземпляр

Чтобы проверить, создан ли экземпляр, перейдите на [страницу цифровой двойников Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) в портал Azure. Вы можете перейти на эту страницу самостоятельно, выполнив поиск по запросу *Azure Digital двойников* на панели поиска портала.

На этой странице перечислены все экземпляры Azure Digital двойников. Найдите имя созданного экземпляра в списке.

Если проверка завершилась неудачно, можно повторить попытку создания экземпляра с помощью [портала](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) или [интерфейса командной строки](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### <a name="verify-user-role-assignment"></a>Проверка назначения роли пользователя

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Помните, что сценарий в настоящее время назначает эту требуемую роль тому же пользователю, который запускает скрипт из Cloud Shell. Если вам нужно назначить эту роль другому пользователю, который будет управлять экземпляром, это можно сделать сейчас с помощью портал Azure ([инструкции](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) или CLI ([инструкции](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

Если проверка завершилась неудачно, можно также повторить собственное назначение ролей с помощью [портала](how-to-set-up-instance-portal.md#set-up-user-access-permissions) или [интерфейса командной строки](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

### <a name="verify-app-registration"></a>Проверка регистрации приложения

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Затем убедитесь, что параметры разрешений Azure Digital двойников были правильно установлены для регистрации. Для этого в строке меню выберите *Манифест* , чтобы просмотреть код манифеста регистрации приложения. Прокрутите окно кода вниз и найдите эти поля в разделе `requiredResourceAccess` . Значения должны соответствовать приведенным на снимке экрана ниже:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

Если один или оба этих шага проверки завершились неудачно, повторите попытку создания регистрации приложения с помощью [портала](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) или инструкций [CLI](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) .

## <a name="other-possible-steps-for-your-organization"></a>Другие возможные действия для вашей организации

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вытестируйте отдельные REST API вызовы в экземпляре с помощью команд CLI Azure Digital двойников: 
* [AZ DT Справочник](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Практическое руководство. Использование CLI для Azure Digital Twins*](how-to-use-cli.md)

Или см. раздел как подключить клиентское приложение к экземпляру, написав код проверки подлинности клиентского приложения:
* [*Пошаговое руководство. Написание кода проверки подлинности приложения*](how-to-authenticate-client.md)