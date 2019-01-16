---
title: Клиент самопроверки для предварительной проверки приложения Azure SaaS | Документация Майкрософт
description: Процедура создания клиента самопроверки для предварительной проверки приложения Azure SaaS.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pbutlerm
ms.openlocfilehash: 4a43d9463f00462b3a68cc82b1896a1392a72037
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082567"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-saas-application"></a>Создание клиента самопроверки для предварительной проверки приложения Azure SaaS

Эта статья служит руководством по созданию службы клиента, которая обращается к API самотестирования. API самотестирования можно использовать для предварительной проверки виртуальной машины на соответствие актуальным требованиям к публикации в Azure Marketplace. Эта служба клиента позволяет протестировать виртуальную машину перед отправкой предложения на сертификацию Майкрософт.

## <a name="development-and-testing-overview"></a>Обзор разработки и тестирования

В рамках процесса самотестирования вы создадите локальный клиент, который подключается к Azure Marketplace для проверки виртуальной машины в подписке Azure. Виртуальная машина может работать под управлением ОС Windows или Linux.

Локальный клиент выполняет скрипт, который выполняет аутентификацию в API самотестирования, отправляет сведения о подключении и получает результаты теста.

Для создания клиента самостоятельного тестирования выполните следующие действия.

1. Выберите для приложения арендатор Azure Active Directory (AAD).
2. Зарегистрируйте клиентское приложение.
3. Создайте маркер для клиентского приложения AAD.
4. Передайте этот маркер в API самотестирования.

Проверьте созданный клиент на тестовой виртуальной машине.

### <a name="self-test-client-authorization"></a>Авторизация клиента самопроверки

На схеме ниже показан процесс авторизации для вызовов одной службы другой с использованием учетных данных клиента (общий секрет или сертификат).

![Процесс авторизации клиента](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>API для клиента самопроверки

Этот API имеет следующую структуру.

```
Uri:             https://isvapp.azurewebsites.net/selftest
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }

```

В следующей таблице описаны поля API.


|      Поле         |    ОПИСАНИЕ    |
|  ---------------   |  ---------------  |
|        Авторизация            |         Строка "Bearer xxxx-xxxx-xxxx-xxxxx" содержит маркер клиента AAD, который можно создать с помощью PowerShell.          |
|          DNSName          |         DNS-имя тестируемой виртуальной машины.          |
|          Пользователь          |          Имя пользователя для подключения к виртуальной машине (имя для входа).         |
|           Пароль         |         Пароль для подключения к виртуальной машине (пароль для входа).          |
|          ОС          |         Операционная система виртуальной машины: Linux или Windows.          |
|         PortNo           |         Номер открытого порта для подключения к виртуальной машине. Обычно используются порт 22 для Linux и 5986 для Windows.          |

## <a name="consuming-the-api"></a>Использование API

Вы можете обращаться к API самотестирования с помощью PowerShell или cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Использование PowerShell для работы с API в операционной системе Linux

Чтобы вызвать API в PowerShell, выполните следующие действия.

1. Используйте команду `Invoke-WebRequest` для вызова API.
2. Укажите метод Post и тип содержимого в формате JSON, как показано ниже в примере кода и на снимке экрана.
3. Создайте параметры Body в формате JSON.

В следующем примере кода показан вызов API из PowerShell.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers; 
$Content = $res | ConvertFrom-Json
```

На следующем снимке экрана показан пример для вызова API из PowerShell.

![Вызов API с помощью PowerShell для ОС Linux](./media/stclient-call-api-ps-linuxvm.png)

Выполнив предыдущий пример, вы получите ответ в формате JSON, анализ которого даст вам следующие сведения.

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++) 
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

На следующем снимке экрана демонстрируется содержимое `$res.Content` с результатами теста в формате JSON.

![Результаты вызова PowerShell для Linux в формате JSON](./media/stclient-pslinux-rescontent-json.png)

На следующем снимке экрана показан пример результата теста в формате JSON, открытый в браузерном средстве просмотра JSON.
(Это может быть [Code Beautify](https://codebeautify.org/jsonviewer), [JSON Viewer](https://jsonformatter.org/json-viewer) и т. п.)

![Результаты вызова PowerShell для виртуальной машины Linux в формате JSON](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Использование PowerShell для работы с API в операционной системе Windows

Чтобы вызвать API в PowerShell, выполните следующие действия.

1. Используйте команду `Invoke-WebRequest` для вызова API.
2. Укажите метод Post и тип содержимого в формате JSON, как показано ниже в примере кода и на снимке экрана.
3. Создайте параметры Body в формате JSON.

В следующем примере кода показан вызов API из PowerShell.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

На следующем снимке экрана показан пример для вызова API из PowerShell.

![Вызов API с помощью PowerShell для ОС Windows](./media/stclient-call-api-ps-windowsvm.png)

Выполнив предыдущий пример, вы получите ответ в формате JSON, анализ которого даст вам следующие сведения.

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++) 
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}

```

На следующем снимке экрана демонстрируется содержимое `$res.Content` с результатами теста в формате JSON.

![Результаты вызова PowerShell для Windows в формате JSON](./media/stclient-pswindows-rescontent-json.png)

На следующем снимке экрана показан результат теста, открытый в браузерном средстве просмотра JSON.
(Это может быть [Code Beautify](https://codebeautify.org/jsonviewer), [JSON Viewer](https://jsonformatter.org/json-viewer) и т. п.)

![Результаты вызова PowerShell для виртуальной машины Windows в формате JSON](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Использование cURL для работы с API в операционной системе Linux

Чтобы создать вызов к API в cURL, выполните следующие действия.

1. Используйте команду curl для вызова API.
2. Укажите метод Post и тип содержимого JSON, как показано в фрагменте кода ниже.

```
CURL POST -H "Content-Type:application/json" 
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest 
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'

```
На следующем снимке экрана показано использование curl для вызова API.

![Вызов API с помощью команды curl](./media/stclient-consume-api-curl.png)

На следующем снимке экрана показаны результаты JSON, полученные с помощью вызова curl.

![Результаты вызова curl в формате JSON](./media/stclient-consume-api-curl-json.png)

## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Выбор арендатора Azure Active Directory для приложения

С помощью следующей процедуры выберите арендатор Azure AD, в котором нужно создать приложение.

1. Войдите на [портале Azure](https://portal.azure.com/).
2. На панели меню сверху выберите учетную запись, а затем в списке "Каталог" выберите арендатор Active Directory, в котором нужно зарегистрировать приложение. Также можно щелкнуть значок **Каталог и подписка**, чтобы просмотреть фильтр глобальных подписок. На следующем снимке экрана показан пример такого фильтра.

   ![Выбор фильтра подписок](./media/stclient-subscription-filter.png)

3. На панели навигации слева щелкните **Все службы**, а затем выберите **Azure Active Directory**.

   При выполнении дальнейших действий может потребоваться имя арендатора (или имя каталога) либо идентификатор арендатора (идентификатор каталога).

   **Чтобы получить сведения об арендаторе, сделайте следующее.**
  
   В разделе **Azure Active Directory Overview** (Обзор Azure Active Directory) выполните поиск по запросу "Свойства" и выберите результат **Свойства**. На следующем снимке экрана вы видите пример этой страницы свойств.

   - **Имя** — имя арендатора или имя каталога.
   - **Идентификатор каталога** — идентификатор арендатора или идентификатор каталога. Также раздел "Свойства" можно найти с помощью полосы прокрутки.

   ![Страница свойств Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Регистрация клиентского приложения

Чтобы зарегистрировать клиентское приложение, выполните следующие шаги.

1. На панели навигации слева щелкните **Все службы**, а затем выберите **Регистрация приложений**.
2. В разделе **Регистрация приложений** выберите **+ Регистрация нового приложения**.
3. В разделе **Создать** укажите обязательную информацию для следующих полей.

   - **Имя** — введите понятное имя для приложения. Например, SelfTestClient.
   - **Тип приложения** — выберите вариант **Web App/API** (Веб-приложение или API).
   - **URL-адрес входа** — введите "https://isvapp.azurewebsites.net/selftest".

4. Нажмите кнопку **Создать**.
5. В разделе **Регистрация приложений** или **Зарегистрированное приложение** скопируйте значение параметра **Идентификатор приложения**.

   ![Получение идентификатора приложения](./media/stclient-app-id.png)

6. На панели инструментов для зарегистрированного приложения выберите **Параметры**.
7. Выберите **Требуемые разрешения**, чтобы настроить разрешения для приложения.
8. В области **Требуемые разрешения** щелкните **+ Добавить**.
9. В области **Добавить доступ через API** выберите **Выбор API**.
10. В разделе **Выбор API** введите "Windows Azure classic deployment model" (Классическая модель развертывания Azure), чтобы найти нужный API.
11. В результатах поиска выберите элемент **Windows Azure classic deployment model** (Классическая модель развертывания Azure) и щелкните **Выбрать**.

    ![Настройка поддержки нескольких арендаторов для приложения](./media/stclient-select-api.png)

12. В области **Добавить доступ через API** выберите **Выбор разрешений**.
13. Выберите **Access “Windows Azure Service Management API”** (Доступ к API управления службами Windows Azure).

    ![Включение доступа к API для приложения](./media/stclient-enable-api-access.png)

14. Нажмите кнопку **Выбрать**.
15. Нажмите кнопку **Готово**.
16. В разделе **Параметры** выберите **Свойства**.
17. В разделе **Свойства** прокрутите вниз до раздела **Мультитенантный**. Выберите **Да**.  

    ![Настройка поддержки нескольких арендаторов для приложения](./media/stclient-yes-multitenant.png)

18. Щелкните **Сохранить**.
19. В разделе **Параметры** выберите **Ключи**.
20. Создание секретный ключ, выбрав текстовое поле **Описание** для ключа. Задайте значения в следующих полях:

    - Введите имя ключа. Например, selftestclient.
    - В раскрывающемся списке **Истекает** выберите вариант "через один год".
    - Щелкните **Сохранить**, чтобы создать ключ.
    - В поле **Значение** скопируйте ключ.

     >[!Important]
     >После выхода из формы "Ключи" вы не сможете увидеть значение этого ключа.

    ![Форма со значением ключа](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Создание маркера для клиентского приложения

Для создания и получения маркера через OAuth REST API вы можете использовать любую из следующих программ:

- postman
- cURL в Linux;
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Создание и получение маркера с помощью Postman

 Чтобы получить через Auth0 маркеры для любых авторизованных приложений, выполните запрос POST к конечной точке [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) с полезными данными в следующем формате:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```
В тексте запроса передайте следующие параметры:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

В заголовке запроса передайте следующие параметры:

```
Content-Type: application/x-www-form-urlencoded
```

На следующем снимке экрана показан пример получения маркера с помощью Postman.

![Получение маркера с помощью Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Создание и получение маркера с помощью cURL в Linux

Чтобы получить через Auth0 маркеры для любых авторизованных приложений, выполните запрос POST к конечной точке [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) с полезными данными в следующем формате:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

На следующем снимке экрана показан пример получения маркера с помощью команды curl.

![Получение маркера с помощью команды curl](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Создание и получение маркера с помощью C#

Чтобы получить через Auth0 маркеры для любых авторизованных приложений, выполните запрос POST к конечной точке [https://soamtenant.auth0.com/oauth/token](https://soamtenant.auth0.com/oauth/token) с полезными данными в следующем формате:

```
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Создание и получение маркера с помощью PowerShell

Чтобы получить через Auth0 маркеры для любых авторизованных приложений, выполните запрос POST к конечной точке [https://soamtenant.auth0.com/oauth/token](https://soamtenant.auth0.com/oauth/token) с полезными данными в следующем формате:

```
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken

```

## <a name="pass-the-client-app-token-to-the-api"></a>Передача маркера клиентского приложения в API

Передайте маркер в API самотестирования, добавив следующий код в заголовок авторизации:

```
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest’
$accesstoken = ‘place your token here’

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
echo 'Test Results:'
$result.Content

```

## <a name="test-your-self-test-client"></a>Тестирование клиента самопроверки

Чтобы протестировать клиент, выполните следующие действия.

1. Разверните виртуальную машину, которую вы будете проверять.
2. Вызовите API самотестирования, используя маркер авторизации для вашего клиентского приложения.
3. Получите результаты в формате JSON.

### <a name="test-result-examples"></a>Примеры результатов теста

В следующих фрагментах показаны результаты теста в формате JSON.

**Результаты теста для виртуальной машины Windows:**

```
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Результаты теста для виртуальной машины Linux:**

```
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Дополнительная информация

Успешно завершив тестирование приложения Azure SaaS, вы можете [опубликовать предложение](./cpp-publish-offer.md).