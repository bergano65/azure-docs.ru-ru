---
title: Публикация веб-приложения на виртуальную машину Azure из Visual Studio
description: Публикация веб-приложения ASP.NET на виртуальную машину Azure из Visual Studio
services: virtual-machines-windows
author: ghogen
manager: jillfra
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 6e5db9b4e46019aa386057d51d956ff11d90f498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71970867"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Публикация веб-приложения ASP.NET на виртуальную машину Azure из Visual Studio

В этом документе описывается, как публиковать веб-приложение ASP.NET в виртуальном компьютере Azure (VM) с помощью функции публикации **виртуальных машин Microsoft Azure** в Visual Studio 2019.  

## <a name="prerequisites"></a>Предварительные требования
Чтобы использовать Visual Studio для публикации проекта ASP.NET на виртуальную машину Azure, ее необходимо правильно настроить.

- Настройте виртуальную машину для запуска веб-приложения ASP.NET, а также разверните WebDeploy.

- Настройте на виртуальной машине DNS-имя. Дополнительные сведения см. в статье [Создание полного доменного имени на портале Azure для виртуальной машины Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Публикация веб-приложения ASP.NET на виртуальную машину Azure с помощью Visual Studio
В разделе ниже описывается, как опубликовать имеющееся веб-приложение ASP.NET на виртуальную машину Azure.

1. Откройте свое решение для веб-приложений в Visual Studio 2019.
2. В обозревателе решений щелкните правой кнопкой проект и выберите **Опубликовать...**
3. Щелкните стрелку в правой области страницы, чтобы прокрутить параметры публикации и найти **виртуальные машины Microsoft Azure**.  

   ![Страница публикации. Стрелка в правой области]

4. Выберите значок **Виртуальные машины Microsoft Azure**, а затем — **Опубликовать**.

   ![Страница публикации. Значок "Виртуальная машина Microsoft Azure"]

5. Выберите соответствующую учетную запись (с помощью подписки Azure, подключенной к виртуальной машине).  
   - После входа в Visual Studio список учетных записей заполнится всеми учетными записями, прошедшими аутентификацию.  
   - Если вы не выполнили вход в Visual Studio, или если вы не можете найти в списке нужную учетную запись, выберите "Добавить учетную запись..." и выполните соответствующие действия, чтобы войти.  
   ![Средство выбора учетных записей Azure]  

6. Выберите соответствующую виртуальную машину из списка имеющихся виртуальных машин.

   > [!Note]
   > Заполнение этого списка может занять некоторое время.

   ![Средство выбора виртуальной машины Azure]

7. Нажмите кнопку "ОК", чтобы начать публикацию.

8. При запросе для учетных данных, поставить имя пользователя и пароль учетной записи пользователя на целевой VM, который настроен с правами публикации. Эти учетные данные, как правило, имя пользователя и пароль, используемый при создании VM.  

   ![Вход в WebDeploy]

9. Ознакомьтесь с этим сертификатом безопасности и нажмите кнопку "Принимаю".

   ![Ошибка сертификата]

10. Просмотрите окно вывода, чтобы проверить ход выполнения публикации.

    ![Окно вывода]

11. При успешной публикации запустится браузер и откроется URL-адрес нового опубликованного сайта.

**Успех!**

Вы успешно опубликовали веб-приложение на виртуальную машину Azure.

## <a name="publish-page-options"></a>Параметры на странице публикации

После завершения работы мастера публикации в области документа откроется страница публикации с новым выбранным профилем публикации.

### <a name="re-publish"></a>Повторная публикация

Чтобы опубликовать обновления веб-приложения, нажмите кнопку **Опубликовать** на странице публикации.  
- При появлении запроса введите имя пользователя и пароль.  
- Сразу же запустится процесс публикации.

![Страница публикации — кнопка "Опубликовать"]

### <a name="modify-publish-profile-settings"></a>Изменение параметров профиля публикации

Чтобы просмотреть и изменить параметры профиля публикации, выберите **Параметры...**.  

![Страница публикации — кнопка "Параметры..."]

Параметры должны выглядеть примерно так:  

![Параметры публикации — страница подключения]

#### <a name="save-user-name-and-password"></a>Сохранение имени пользователя и пароля
- Избегайте предоставления информации о проверке подлинности каждый раз, когда вы публикуете. Для этого наполните **поля имени пользователя** и **пароля** и выберите поле **паролей Сохранить.**
- Нажмите кнопку **Проверить подключение**, чтобы подтвердить, что данные введены верно.

#### <a name="deploy-to-clean-web-server"></a>Развертывание для очистки веб-сервера

- Если вы хотите убедиться, что веб-сервер имеет чистую копию веб-приложения после каждой загрузки и что никакие другие файлы не остались от предыдущего развертывания, вы можете проверить **Удалить дополнительные файлы в контрольной** поле назначения во вкладке **Настройки.**

- Предупреждение. При активации этого параметра во время публикации будут удалены все файлы, имеющиеся на веб-сервере (каталог wwwroot). Проверьте состояние компьютера перед непосредственной публикацией с этим параметром. 

![Параметры публикации — страница параметров]

## <a name="next-steps"></a>Дальнейшие действия

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Настройка непрерывной интеграции и непрерывного развертывания для автоматизированного развертывания на виртуальную машину Azure

Чтобы настроить конвейер непрерывной поставки с помощью Azure Pipelines, ознакомьтесь со статьей [Deploy to a Windows Virtual Machine](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups) (Развертывание на виртуальную машину Windows).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Страница публикации. Стрелка в правой области]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Страница публикации. Значок "Виртуальная машина Microsoft Azure"]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Средство выбора учетных записей Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Средство выбора виртуальной машины Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Вход в WebDeploy]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Ошибка сертификата]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Окно вывода]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Страница публикации — кнопка "Опубликовать"]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Страница публикации — кнопка "Параметры..."]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Параметры публикации — страница подключения]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Параметры публикации — страница параметров]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
