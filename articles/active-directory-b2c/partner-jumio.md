---
title: Руководство по настройке Azure Active Directory B2C с помощью Жумио
titleSuffix: Azure AD B2C
description: Руководство по настройке Azure Active Directory B2C с помощью Жумио для автоматической проверки ИДЕНТИФИКАТОРов, защиты данных клиентов
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817804"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Руководство по настройке Жумио с помощью Azure Active Directory B2C

В этом примере учебника мы предоставляем рекомендации по интеграции Azure AD B2C с [жумио](https://www.jumio.com/). Жумио — это служба проверки ИДЕНТИФИКАТОРов, которая обеспечивает автоматическую проверку кода в режиме реального времени, защищая данные клиентов.

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, вам потребуется:

- Подписка Azure AD. Если у вас нет подписки, вы можете получить [бесплатную учетную запись](https://azure.microsoft.com/free/).

- [Клиент Azure AD B2C](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Клиент связан с вашей подпиской Azure.

## <a name="scenario-description"></a>Описание сценария

Интеграция Жумио включает следующие компоненты:

- Azure AD B2C — сервер авторизации, отвечающий за проверку учетных данных пользователя, также известный как поставщик удостоверений.

- Жумио — служба, которая принимает сведения об ИДЕНТИФИКАТОРе, предоставленные пользователем, и проверяет ее.

- Промежуточный API-интерфейс RESTful — этот API реализует интеграцию между Azure AD B2C и службой Жумио.

- Хранилище BLOB-объектов — используется для предоставления пользовательских файлов пользовательского интерфейса политикам Azure AD B2C.

Реализация показана на следующей схеме архитектуры.

![Снимок экрана для жумио — схема архитектуры](./media/partner-jumio/jumio-architecture-diagram.png)

|Шаг | Описание |
|:-----| :-----------|
| 1. | Пользователь прибывает на странице входа. Пользователи настроили регистрацию для создания новой учетной записи и ввода данных на странице. Azure AD B2C собирает атрибуты пользователя.
| 2. | Azure AD B2C вызывает API среднего уровня и передает атрибуты пользователя.
| 3. | API среднего уровня собирает атрибуты пользователя и преобразует их в формат, который может использовать API Жумио. Затем после отправки его в Жумио.
| 4. | После того как Жумио потребляет информацию и обрабатывает ее, она возвращает результат в API среднего уровня.
| 5. | API среднего уровня обрабатывает сведения и отправляет в Azure AD B2C соответствующие сведения.
| 6. | Azure AD B2C получает сведения от API среднего уровня. Если отображается ответ на сбой, пользователю выдается сообщение об ошибке. Если отображается ответ об успешном выполнении, пользователь проходит проверку подлинности и записывается в каталог.

## <a name="onboard-with-jumio"></a>Подключение с помощью Жумио

1. Чтобы создать учетную запись Жумио, обратитесь в [жумио](https://www.jumio.com/contact/)

2. После создания учетной записи сведения используются в конфигурации API. В следующих разделах описывается процесс.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Настройка Azure AD B2C с помощью Жумио

### <a name="part-1---deploy-the-api"></a>Часть 1. Развертывание API

Разверните предоставленный [код API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) в службе Azure. Код можно опубликовать из Visual Studio, следуя этим [инструкциям](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Вам потребуется URL-адрес развернутой службы, чтобы настроить Azure AD с использованием требуемых параметров.

### <a name="part-2---deploy-the-client-certificate"></a>Часть 2. Развертывание сертификата клиента

1. Вызов API Жумио защищен сертификатом клиента. Создайте самозаверяющий сертификат с помощью примера кода PowerShell, упомянутого ниже.

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. Затем сертификат будет экспортирован в расположение, указанное для { ``your-local-path`` }.

3. Следуя инструкциям, изложенным в этом [документе](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate), импортируйте сертификат в службу приложений Azure.

### <a name="part-3---create-a-signingencryption-key"></a>Часть 3. Создание ключа подписи или шифрования

Создайте случайную строку длиной более 64 символов, которая содержит только буквы или цифры.

Пример: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Используйте приведенный ниже сценарий PowerShell, чтобы создать буквенно-цифровое значение длиной 64 символов.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>Часть 4. Настройка API

Параметры приложения можно [настроить в службе приложений в Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). С помощью этого метода параметры можно безопасно настроить без их возврата в репозиторий. Необходимо предоставить следующие параметры для API-интерфейса RESTful:

| Параметры приложений | Источник | Примечания |
| :-------- | :------------| :-----------|
|Жумиосеттингс: Аусусернаме | Конфигурация учетной записи жумио |     |
|Жумиосеттингс: Ауспассворд | Конфигурация учетной записи жумио |     |
|AppSettings: Сигнингцертсумбпринт|Отпечаток самозаверяющего сертификата, созданного|  |
|AppSettings: Идтокенсигнингкэй| Создание ключа подписывания с помощью PowerShell | |
| AppSettings: Идтокененкриптионкэй |Ключ шифрования, созданный с помощью PowerShell
| AppSettings: Идтокениссуер | Издатель, используемый для маркера JWT (предпочтительно значение идентификатора GUID) |
| AppSettings: Идтокенаудиенце  | Аудитория, используемая для маркера JWT (предпочтительно значение идентификатора GUID) |
|AppSettings: Басередиректурл | Базовый URL-адрес политики B2C | HTTPS://{имя вашего клиента}. b2clogin. com/{ваш-Application-ID}|
| WEBSITE_LOAD_CERTIFICATES| Отпечаток самозаверяющего сертификата, созданного |

### <a name="part-5---deploy-the-ui"></a>Часть 5. Развертывание пользовательского интерфейса

1. Настройте [контейнер хранилища BLOB-объектов в учетной записи хранения](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Храните файлы пользовательского интерфейса из [папки пользовательского интерфейса](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) в контейнер больших двоичных объектов.

#### <a name="update-ui-files"></a>Обновление файлов пользовательского интерфейса

1. В файлах пользовательского интерфейса перейдите в папку [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. Откройте каждый HTML-файл.

3. Найдите и замените {My-UI-BLOB-Container-URL} URL-адресом контейнера больших двоичных объектов.

4. Найдите и замените {ваш-Intermediate-API-URL} URL-адресом промежуточной службы приложений API.

>[!NOTE]
> Рекомендуется, чтобы клиенты добавили уведомление о согласия на страницу коллекции атрибутов. Уведомлять пользователей о том, что сведения будут отправляться сторонним службам для проверки личности.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Часть 6. Настройка политики Azure AD B2C

1. Перейдите к [политике Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) в папке политики.

2. Чтобы скачать [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) , следуйте этому [документу](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack)

3. Настройте политику для клиента Azure AD B2C.

>[!NOTE]
>Обновите указанные политики, чтобы они были связаны с конкретным клиентом.

## <a name="test-the-user-flow"></a>Тестирование потока пользователя

1. Откройте клиент Azure AD B2C и в разделе политики выберите **инфраструктура процедур идентификации**.

2. Выберите ранее созданный **сигнупсигнин**.

3. Выберите **запустить поток пользователя** и выберите параметры:

   а. **Приложение**: выберите зарегистрированное приложение (пример — JWT)

   b. **URL-адрес ответа**: выберите **URL-адрес перенаправления**

   c. Выберите **Выполнить поток пользователя**.

4. Последовательно выберите процесс регистрации и создайте учетную запись.

5. Служба жумио будет вызываться во время потока, после создания пользовательского атрибута. Если поток неполон, убедитесь, что пользователь не сохранен в каталоге.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях:

- [Пользовательские политики в Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Приступая к работе с пользовательскими политиками в Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
