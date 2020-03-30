---
title: Развертывание инструмента управления для Windows Virtual Desktop с использованием основного сервиса - Azure
description: Как развернуть инструмент управления для Windows Virtual Desktop с помощью PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127797"
---
# <a name="deploy-a-management-tool-with-powershell"></a>Развертывание инструмента управления с PowerShell

В этой статье будет показан способ развертывания инструмента управления с помощью PowerShell.

## <a name="important-considerations"></a>Важные сведения

Подписка каждого клиента Azure Active Directory (Azure AD) нуждается в отдельном развертывании инструмента управления. Этот инструмент не поддерживает сценарии бизнес-бизнеса Azure AD (B2B). 

Это средство управления является примером. Майкрософт предоставит важные обновления безопасности и исправления. [Исходный код доступен в GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Независимо от того, являетесь ли вы клиентом или партнером, мы рекомендуем вам настроить инструмент для удовлетворения потребностей вашего бизнеса.

Следующие браузеры совместимы с инструментом управления:

- Google Chrome версии 68 и выше
- Microsoft Edge версии 40.15063 и выше
- Mozilla Firefox версии 52.0 и выше
- Safari версии 10 и выше (только macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Что необходимо для развертывания средства управления

Перед развертыванием средства управления потребуется пользователь Azure Active Directory (Azure AD), чтобы создать регистрацию приложения и развернуть пользовательский интерфейс управления. Этот пользователь должен:

- получить разрешение на создание ресурсов в своей подписке Azure;
- получить разрешение на создание приложения Azure AD. Чтобы проверить, есть ли у пользователя необходимые разрешения, выполните действия, перечисленные в разделе [Необходимые разрешения](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Для успешного развертывания и настройки инструмента управления сначала необходимо загрузить следующие скрипты PowerShell из [репо RDS-Templates GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts) и сохранить их в той же папке на локальной машине.

  - createWdMgmtUxAppРегистрация.ps1
  - updateWvdMgmtApiurl.ps1

После развертывания и настройки средства управления рекомендуется попросить пользователя запустить пользовательский интерфейс управления, чтобы убедиться в том, что все работает. Пользователю, запускающему пользовательский интерфейс управления, должна быть назначена роль, которая позволяет просматривать или изменять клиент виртуальных рабочих столов Windows.

## <a name="set-up-powershell"></a>Настройка PowerShell

Начало, войдем в модули Az и Azure AD PowerShell. Вот как войти в систему:

1. Откройте PowerShell в качестве администратора и перейдите в каталог, где вы сохранили скрипты PowerShell.
2. Восвадайте в Azure с учетной записью с разрешениями владельца или участника в подписке Azure, которую вы планируете использовать для создания инструмента управления, запустив следующее cmdlet:

    ```powershell
    Login-AzAccount
    ```

3. Запустите следующий cmdlet, чтобы войти в Azure AD с той же учетной записью, что и для модуля Az PowerShell:

    ```powershell
    Connect-AzureAD
    ```

4. После этого перейдите к папке, где вы сохранили два сценария PowerShell от РЕпо RDS-Templates GitHub.

Храните окно PowerShell, которое вы использовали для входной регистрации, для запуска дополнительных cmdlets PowerShell при входе в систему.

## <a name="create-an-azure-active-directory-app-registration"></a>Создание регистрации приложения Active Directory Azure

Запустите следующие команды для создания регистрации приложения с требуемыми разрешениями API:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Теперь, когда вы завершили регистрацию приложения Azure AD, можно развернуть инструмент управления.

## <a name="deploy-the-management-tool"></a>Развертывание средства управления

Выполнить следующие команды PowerShell, чтобы развернуть инструмент управления и связать его с только что созданным сервисом:
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

После создания веб-приложения необходимо добавить перенаправление URI в приложение Azure AD, чтобы успешно войти в систему пользователей.

## <a name="set-the-redirect-uri"></a>Установите перенаправление URI

Выполнить следующие команды PowerShell, чтобы получить URL веб-приложения и установить его в качестве проверки подлинности перенаправить URI (также называемый url ответа):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

Теперь, когда вы добавили перенаправление URI, вам необходимо обновить URL-адрес API, чтобы инструмент управления мог взаимодействовать с службой резервного копирования API.

## <a name="update-the-api-url-for-the-web-application"></a>Обновление URL-адреса API для веб-приложения

Выполнить следующий скрипт для обновления конфигурации API URL в передней части веб-приложения:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Теперь, когда вы полностью настроили веб-приложение с помощью инструментов управления, пришло время проверить приложение Azure AD и дать согласие.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>Проверка приложения Azure AD и предоставление согласия

Чтобы проверить конфигурацию приложения Azure AD и предоставить согласие:

1. Откройте свой интернет-браузер и вопийте на [портал Azure](https://portal.azure.com/) с помощью административной учетной записи.
2. Из панели поиска в верхней части портала Azure вынани **теили регистрации приложений** и выберите элемент в рамках **Службы.**
3. Выберите **все приложения** и ищите уникальное имя приложения, которое вы предоставили для сценария PowerShell, в [создании регистрации приложения Azure Active Directory.](#create-an-azure-active-directory-app-registration)
4. В панели на левой стороне браузера выберите **аутентификацию** и убедитесь, что перенаправление URI совпадает с URL-адресом веб-приложения для инструмента управления, как показано на следующем изображении.
   
   [![Страница аутентификации с](media/management-ui-redirect-uri-inline.png) введенным перенаправлением URI](media/management-ui-redirect-uri-expanded.png#lightbox)

5. В левой панели выберите **разрешения API,** чтобы подтвердить, что разрешения были добавлены. Если вы являетесь глобальным амином, выберите **согласие админа `tenantname` Гранта на** кнопку и следуйте запросам диалога, чтобы предоставить согласие админа для вашей организации.
    
    [![Страница](media/management-ui-permissions-inline.png) разрешений API](media/management-ui-permissions-expanded.png#lightbox)

Теперь вы можете начать использовать инструмент управления.

## <a name="use-the-management-tool"></a>Использование средства управления

Теперь, когда вы настроили инструмент управления в любое время, вы можете запустить его в любое время, в любом месте. Вот как запустить инструмент:

1. Откройте URL-адрес веб-приложения в веб-браузере. Если вы не помните URL-адрес, вы можете войти в Azure, найти службу приложения, развернутую для инструмента управления, а затем выбрать URL.
2. Войдите в систему, используя свои учетные данные для Виртуального рабочего стола Windows.
   
   > [!NOTE]
   > Если вы не дали согласие на админ при настройке инструмента управления, каждый пользователь, который впишется, должен будет предоставить свое согласие пользователя, чтобы использовать инструмент.

3. При выборе группы клиентов выберите **группу по умолчанию** из списка выпадающих.
4. После выбора **Default Tenant Group** (Группа клиентов по умолчанию) в левой части окна должно появиться меню. В этом меню найдите и выберите имя группы клиентов.
   
   > [!NOTE]
   > Если у вас есть настраиваемая группа клиентов, введите имя вручную вместо выбора из раскрывающегося списка.

## <a name="report-issues"></a>Сообщение о проблемах

Если у вас возникли проблемы со средством управления или другими средствами Виртуального рабочего стола Windows, следуйте указаниям в [шаблонах Azure Resource Manager для службы удаленных рабочих столов](https://github.com/Azure/RDS-Templates/blob/master/README.md), чтобы сообщить о них в GitHub.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы научились развертывать средство управления и подключаться к нему, ознакомьтесь со сведениями об использовании Работоспособности служб Azure для мониторинга проблем служб и рекомендаций по работоспособности. Дополнительные сведения см. в руководстве [Настройка оповещений служб](./set-up-service-alerts.md).
