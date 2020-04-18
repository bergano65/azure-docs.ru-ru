---
title: Автоматизация развертывания виртуальной машины в Amazon Web Services
description: В этой статье показано, как автоматизировать создание виртуальной машины Amazon Web Service с помощью службы автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 52a887d2d934aa2f7e13f0c2fdb4332066aee0e7
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604827"
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Сценарий службы автоматизации Azure: подготовка виртуальной машины AWS
В этой статье вы узнаете, как с помощью службы автоматизации Azure подготовить виртуальную машину в подписке Amazon Web Service (AWS) и присвоить виртуальной машине конкретное имя, которое в AWS называется "тегом" виртуальной машины.

## <a name="prerequisites"></a>Предварительные требования
Вам необходимо иметь учетную запись Azure Automation и подписку Amazon Web Services (AWS). Дополнительные сведения о настройке учетной записи службы автоматизации Azure и об указании в ней учетных данных подписки AWS см. в статье [Проверка подлинности модулей Runbook с помощью Amazon Web Services](automation-config-aws-account.md). Эта учетная запись должна быть создана или обновлена с вашими учетными данными подписки AWS, прежде чем продолжить, как вы ссылаетесь на эту учетную запись в разделах ниже.

## <a name="deploy-amazon-web-services-powershell-module"></a>Развертывание модуля PowerShell для Amazon Web Services
Для выполнения своей работы в вашем справочном альбоме VM используется модуль AWS PowerShell. Используйте следующие шаги, чтобы добавить модуль в учетную запись Автоматизации, настроенную с учетными данными подписки AWS.  

1. Откройте браузер и перейдите к [коллекции PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/), а затем нажмите кнопку **Deploy to Azure Automation** (Развернуть в службе автоматизации Azure).<br><br> ![Импорт модуля AWS PS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Вы перейдете на страницу входа в Azure. После проверки подлинности откроется следующая страница портала Azure.<br><br> ![Страница импорта модуля](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Выберите учетную запись Автоматизация для использования и нажмите **OK,** чтобы начать развертывание.

   > [!NOTE]
   > Когда Azure Automation импортирует модуль PowerShell, он извлекает смдлеты. Действия не появляются до тех пор, пока Автоматизация полностью не завершит импорт модуля и извлечение cmdlets. Это может занять несколько минут.  
   > <br>

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Нажмите на плитку **активов** и, на панели активов, выберите **модули.**
3. На странице Модули вы увидите в списке модуль **AWSPowerShell**.

## <a name="create-aws-deploy-vm-runbook"></a>Создание модуля Runbook для развертывания виртуальной машины в AWS
После развертывания модуля AWS PowerShell можно создать модуль Runbook для автоматизации подготовки виртуальной машины в AWS с помощью сценария PowerShell. На приведенных ниже шагах показано, как использовать родной скрипт PowerShell в Azure Automation.  

> [!NOTE]
> Дополнительные параметры и сведения, касающиеся этого сценария, можно найти в [коллекции PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Загрузите сценарий PowerShell New-AwsVM из галереи PowerShell, открыв сеанс PowerShell и набрав следующую команду:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. С портала Azure откройте учетную запись Автоматизации и выберите **Runbooks** в рамках **Автоматизации процессов.**  
3. На странице Модули Runbook выберите **Добавить Runbook**.
4. На панели добавления runbook выберите **Быстрый Создать** для создания нового runbook.
5. На панели свойств Runbook введите имя для своей книги.
6. Из списка выпадающих типов **Runbook** выберите **PowerShell,** а затем нажмите **Создать**.<br><br> ![Панель создания модуля Runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. При открытии страницы "Изменение сценария PowerShell для модуля Runbook" скопируйте и вставьте сценарий PowerShell в область создания модуля Runbook.<br><br> ![Сценарий PowerShell для модуля Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > При работе с примером сценария PowerShell имейте в виду следующее:
    > 
    > * Модуль Runbook содержит несколько параметров по умолчанию. Проверьте все значения по умолчанию и при необходимости обновите их.
    > * Если вы сохранили учетные данные AWS в `AWScred`качестве актива учетных данных, названного иначе, вам необходимо обновить сценарий на строке 57, чтобы соответствовать соответствующим образом.  
    > * При работе с командами AWS в PowerShell, особенно в этом примере модуля Runbook, необходимо указывать регион AWS. В противном случае командлеты завершатся с ошибкой. Дополнительные сведения на сайте AWS см. в разделе [Specify AWS Region](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) (Указание региона AWS) документации "AWS Tools for Windows PowerShell" (Инструменты AWS для Windows PowerShell).  
    >

7. Чтобы получить список имен образов из подписки AWS, запустите PowerShell ISE и импортируйте модуль AWS PowerShell. Аутентифицируйте против AWS, `Get-AutomationPSCredential` заменяя `AWScred = Get-Credential`в вашей среде ISE . Это заявление подсказывает ваши учетные данные, и вы можете предоставить свой идентификатор ключа доступа для имени пользователя и ваш секретный ключ доступа к паролю. 

        ```powershell
        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile
        ```
        
    Возвращается следующий результат:<br><br>
   ![Получение образов AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Копировать и вставить одно из имен изображений в переменной автоматизации, как уведомяно в runbook как `$InstanceType`. Поскольку в этом примере вы используете бесплатную многоуровневую подписку AWS, вы используете **t2.micro** для примера runbook.  
9. Сохраните модуль Runbook, нажмите кнопку **Опубликовать**, чтобы опубликовать модуль Runbook, а затем кнопку **Да** в появившемся запросе.

### <a name="testing-the-aws-vm-runbook"></a>Тестирование модуля Runbook для AWS
Прежде чем приступить к тестированию runbook, необходимо проверить несколько вещей:

* Был создан `AWScred` актив, призванный к аутентификации в отношении AWS, или скрипт был обновлен для ссылки на имя ресурса.    
* Модуль AWS PowerShell был импортирован в Azure Automation.  
* Создан новый runbook, и при необходимости значения параметров были проверены и обновлены.  
* **Вход многословные записи** и опционально **записи прогресса журнала** в рамках операции Runbook **Регистрация и трассировка** были установлены **на**.<br><br> ![Runbook Регистрация](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)и отслеживание .  

1. Нажмите **Кнопка Начало,** чтобы начать runbook, а затем нажмите **OK,** когда начало Runbook панели открывается.
2. На панели Start Runbook укажите имя VM. Примите значения по умолчанию для других параметров, которые вы предварительно настроили в скрипте. Нажмите **OK,** чтобы начать работу runbook.<br><br> ![Запуск модуля Runbook: New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Панель вакансий вакансии открыта для созданного вами задания runbook. Закройте эту область.
4. Вы можете просмотреть ход выполнения задания и просмотреть потоки выходных данных, выбрав **все журналы** из панели runbook Job.<br><br> ![Раздел "Поток": выходные данные](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Чтобы подтвердить, что VM готовится, войдите в консоль управления AWS, если вы в настоящее время не вошли в систему.<br><br> ![Консоль AWS: развернутая виртуальная машина](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Дальнейшие действия
* Чтобы начать работу с графических runbooks, [см.](automation-first-runbook-graphical.md)
* Чтобы начать работу с runbooks PowerShell Workflow, [см.](automation-first-runbook-textual.md)
* Чтобы узнать больше о типах runbook [Azure Automation runbook types](automation-runbook-types.md)и их преимуществах и ограничениях, см.
* Для получения дополнительной информации о функции поддержки скриптов PowerShell [см.](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)