---
title: The Conditional Access What If tool - Azure Active Directory
description: Learn how you can understand the impact of your Conditional Access policies on your environment.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5846934a8ad8455ca375b4bc54fc46d45aba1cd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379979"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Troubleshoot using the What If tool in Conditional Access

[Conditional Access](../active-directory-conditional-access-azure-portal.md) is a capability of Azure Active Directory (Azure AD) that enables you to control how authorized users access your cloud apps. How do you know what to expect from the Conditional Access policies in your environment? To answer this question, you can use the **Conditional Access What If tool**.

This article explains how you can use this tool to test your Conditional Access policies.

## <a name="what-it-is"></a>Описание

The **Conditional Access What If policy tool** allows you to understand the impact of your Conditional Access policies on your environment. Вместо того чтобы тестировать политики, несколько раз выполняя вход вручную, с помощью этого средства вы можете оценить имитированный вход пользователя. При имитации оценивается влияние входа на политики и создается отчет об имитации. The report does not only list the applied Conditional Access policies but also [classic policies](policy-migration.md#classic-policies) if they exist.    

The **What If** tool provides a way to quickly determine the policies that apply to a specific user. Эти сведения можно использовать, например, если необходимо устранить проблему.    

## <a name="how-it-works"></a>Принципы работы

In the **Conditional Access What If tool**, you first need to configure the settings of the sign-in scenario you want to simulate. К числу этих параметров относятся следующие:

- пользователь, которого необходимо проверить; 
- облачные приложения, к которым пользователь попытается получить доступ;
- условия, при которых осуществляется доступ к настроенным облачным приложениям.
     
В качестве следующего шага можно запустить имитацию, чтобы проверить выбранные параметры. В оценке участвуют только включенные политики.

По завершении оценки инструмент создает отчет о задействованных политиках.

## <a name="running-the-tool"></a>Запуск инструмента

You can find the **What If** tool on the **[Conditional Access - Policies](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** page in the Azure portal.

To start the tool, in the toolbar on top of the list of policies, click **What If**.

![What If](./media/what-if-tool/01.png)

Прежде чем можно будет выполнить оценку, необходимо настроить параметры.

## <a name="settings"></a>Настройки

В этом разделе приводятся сведения о параметрах имитации.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Пользователь

Можно выбрать только одного пользователя. Это единственное обязательное для заполнения поле.

### <a name="cloud-apps"></a>Облачные приложения

Значение по умолчанию для этого параметра — **Все облачные приложения**. При значении по умолчанию выполняется оценка всех доступных политик в вашей среде. Можно сузить область оценки до политик, влияющих на конкретные облачные приложения.

### <a name="ip-address"></a>IP-адрес

IP-адрес — это отдельный IPv4-адрес, необходимый для имитации [условия расположения](location-condition.md). Адрес представляет веб-адрес устройства, используемого пользователем для входа. Можно проверить IP-адрес устройства, например, перейдя по ссылке [What is my IP address](https://whatismyipaddress.com) (Определить мой IP-адрес).    

### <a name="device-platforms"></a>Платформы устройств

Этот параметр имитирует [условие платформы устройства](conditions.md#device-platforms) и представляет эквивалент параметра **Все платформы (в том числе и неподдерживаемые)** . 

### <a name="client-apps"></a>Клиентские приложения

Этот параметр имитирует [условие клиентских приложений](conditions.md#client-apps).
По умолчанию этот параметр вызывает оценку всех политик, для которых выбран параметр **Браузер** или **Мобильные приложения и настольные клиенты** (или оба эти параметра). Он также позволяет обнаружить политики, которые принудительно используют **Exchange ActiveSync (EAS)** . Область этого параметра можно сузить, выбрав:

- **Браузер** для оценки всех политик, для которых выбран по крайней мере параметр **Браузер**; 
- **Мобильные приложения и настольные клиенты** для оценки всех политик, для которых выбран по крайней мере параметр **Мобильные приложения и настольные клиенты**. 

### <a name="sign-in-risk"></a>Риск при входе

Этот параметр имитирует [условие риска при входе](conditions.md#sign-in-risk).   

## <a name="evaluation"></a>Оценка 

You start an evaluation by clicking **What If**. После оценки предоставляется отчет, содержащий: 

![What If](./media/what-if-tool/03.png)

- индикатор существования классических политик в вашей среде;
- политики, которые применяются к вашему пользователю;
- политики, которые не применяются к вашему пользователю.

Если для выбранных облачных приложений существуют [классические политики](policy-migration.md#classic-policies), то отчет содержит соответствующий индикатор. Щелкнув этот индикатор, можно перейти на страницу классических политик. На странице классических политик можно перенести классическую политику или просто отключить ее. Закрыв эту страницу, можно вернуться к результату оценки.

В списке политик, применяемых к выбранному пользователю, можно найти список [элементов управления разрешениями](controls.md#grant-controls) и элементов управления [сеансами](controls.md#session-controls), которым должен соответствовать пользователь.

В списке политик, которые не применяются к пользователю, можно также найти причины, по которым эти политики не применяются. Для каждой из перечисленных политик причина представляет первое условие, которое не было выполнено. Возможной причиной для политики, которая не применяется, является отключение политики ввиду того, что она в дальнейшем не оценивается.   

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, как настроить политику условного доступа, см. статью [Краткое руководство. Требование Многофакторной идентификации для конкретных приложений с помощью условного доступа Azure Active Directory](app-based-mfa.md).
- Если вы готовы к настройке политик условного доступа для своей среды, см. статью [Best practices for Conditional Access in Azure Active Directory](best-practices.md) (Рекомендации по работе с условным доступом в Azure Active Directory). 
- Если вы хотите перенести классические политики, прочитайте раздел [Перенос классических политик на портале Azure](policy-migration.md).  
