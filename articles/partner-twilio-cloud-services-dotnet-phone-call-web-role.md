---
title: Телефонные звонки из Twilio (.NET) | Документация Майкрософт
description: Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры программного кода написаны в .NET.
services: ''
documentationcenter: .net
author: georgewallace
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: gwallace
ms.openlocfilehash: 27b4f3cdd8f622a97cfc0853f79bb77d76673dcf
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636139"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Осуществление телефонных звонков с использованием Twilio в веб-роли Azure
В этом руководстве описывается, как выполнять телефонные звонки с веб-страницы, размещенной в Azure, с помощью службы Twilio. Полученное приложение предлагает пользователю осуществить звонок, указав номер и сообщение, как показано на следующем снимке экрана.

![Форма звонка Azure с использованием службы Twilio и ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Предварительные требования
Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1. Получение учетной записи Twilio и маркера проверки подлинности из [консоли Twilio][twilio_console]. Чтобы приступить к работе с Twilio, зарегистрируйтесь [https://www.twilio.com/try-twilio][try_twilio]по адресу. Вы можете оценить цены по [https://www.twilio.com/pricing][twilio_pricing]адресу. Дополнительные сведения об API, предоставляемом Twilio, см [https://www.twilio.com/voice/api][twilio_api]. в разделе.
2. Добавьте *библиотеку Twilio .NET* в свою веб-роль. Дополнительные сведения см. в разделе **Добавление библиотек Twilio в проект веб-роли** далее в этой статье.

Вы должны быть знакомы с созданием простой [веб-роли в Azure][azure_webroles_get_started].

## <a name="howtocreateform"></a>Практическое руководство. Создание веб-формы для выполнения звонка
<a id="use_nuget"></a>Добавление библиотек Twilio в проект веб-роли

1. Откройте решение в Visual Studio.
2. Щелкните правой кнопкой мыши **References**(Ссылки).
3. Выберите **Управление пакетами NuGet...** .
4. Щелкните **Online**(В сети).
5. В поле поиска online введите *twilio*.
6. Щелкните **Install** (Установить) на пакете Twilio.

В следующем коде показано, как создать веб-форму, позволяющую извлечь данные пользователя для выполнения звонка. В этом примере создается веб-роль ASP.NET с именем **TwilioCloud**.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a id="howtocreatecode"></a>Практическое руководство. Создание кода для выполнения звонка
Следующий код вызывается после заполнения формы пользователем, создает сообщение звонка, а затем сам звонок. В этом примере код выполняется в обработчике события onclick для кнопки формы. (Вместо заполнителей `accountSID` и `authToken` в приведенном ниже коде следует указать вашу учетную запись Twilio и маркер проверки подлинности.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

Выполняется звонок. После этого отображаются конечная точка Twilio, версия API-интерфейса и сведения о состоянии звонка. На следующем снимке экрана показаны результаты выполнения примера.

![Ответ на звонок Azure с использованием службы Twilio и ASP.NET][twilio_dotnet_basic_form_output]

Дополнительные сведения о TwiML можно найти по адресу [https://www.twilio.com/docs/api/twiml][twiml]. Дополнительные сведения о &lt;скажите&gt; и других командах Twilio можно найти по адресу [https://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Дальнейшие действия
В этом коде демонстрируются базовые функциональные возможности службы Twilio в веб-роли ASP.NET в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Пример:

* Вместо веб-формы для хранения номеров телефона и текста звонков вы можете использовать хранилище BLOB-объектов Azure или экземпляр Базы данных SQL Azure. Сведения об использовании больших двоичных объектов в Azure см. в статье [Использование службы хранилища BLOB-объектов Azure в .NET][howto_blob_storage_dotnet]. Сведения об использовании базы данных SQL см. [в статье Использование базы данных SQL Azure в приложениях .NET][howto_sql_azure_dotnet].
* С помощью `RoleEnvironment.getConfigurationSettings` вы можете извлечь идентификатор учетной записи Twilio и маркер проверки подлинности из параметров конфигурации развертывания, не прописывая их в форме в виде фиксированных значений. Дополнительные сведения о классе `RoleEnvironment` см. в разделе [пространство имен Microsoft. WindowsAzure. ServiceRuntime][azure_runtime_ref_dotnet].
* Ознакомьтесь с рекомендациями по безопасности [https://www.twilio.com/docs/security][twilio_docs_security]Twilio по адресу.
* Дополнительные сведения о Twilio см [https://www.twilio.com/docs][twilio_docs]. по адресу.

## <a name="seealso"></a>Дополнительные материалы
* [Использование Twilio для поддержки голосовых вызовов и SMS в Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
