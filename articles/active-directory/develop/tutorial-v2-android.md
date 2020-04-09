---
title: Вход, выход пользователей и вызов Microsoft Graph (Android) — платформа удостоверений Майкрософт | Azure
description: Получение маркера доступа и вызов Microsoft Graph или API, которые требуют маркеры доступа от платформы удостоверений Майкрософт (Android)
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5c8bd5accefceee042601c3cf7d71f5e9131e04e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880828"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Руководство по Вход пользователей и вызов Microsoft Graph из приложения Android 

>[!NOTE]
>В этом учебнике представлены упрощенные примеры работы с MSAL для Android. Для простоты здесь используется режим одной учетной записи. Вы также можете открыть репозиторий и клонировать [предварительно настроенный пример приложения](https://github.com/Azure-Samples/ms-identity-android-java/) для изучения более сложных сценариев. Дополнительные сведения о примере приложения, конфигурации и регистрации вы найдете в [этом кратком руководстве](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android). 

Из этого руководства вы узнаете, как интегрировать приложение Android с платформой удостоверений Майкрософт с помощью библиотеки проверки подлинности Майкрософт для Android. Вы потренируетесь обрабатывать вход и выход пользователя, получать маркер доступа для вызова API Microsoft Graph и выполнять запрос к API Graph. 

> [!div class="checklist"]
> * Интеграция приложения Android с платформой удостоверений Майкрософт 
> * Вход пользователя 
> * Получение маркера доступа для вызова API Microsoft Graph 
> * Вызов API Microsoft Graph 
> * Выход пользователя 

Когда вы завершите работу с этим руководством, ваше приложение сможет принимать операции входа с помощью личных учетных записей Майкрософт (включая outlook.com, live.com и др.), а также рабочих или учебных учетных записей из любой компании или организации, в которых используется Azure Active Directory.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="how-this-tutorial-works"></a>Как работает это руководство

![Схема работы примера приложения, создаваемого в этом кратком руководстве](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

В этом руководстве приложение авторизует пользователя и получает данные от его имени. Доступ к этим данным будет осуществляться через защищенный API (в данном случае API Microsoft Graph), который требует авторизации и защищен платформой удостоверений Майкрософт.

В частности:

* Приложение авторизует пользователя через браузер или Microsoft Authenticator и Корпоративный портал Intune.
* Пользователь принимает разрешения, которые запрашивает приложение.
* Приложение выдает маркер доступа для API Microsoft Graph.
* Этот маркер доступа будет включен в HTTP-запрос к веб-API.
* Затем обрабатывается ответ Microsoft Graph.

В этом примере используется библиотека проверки подлинности Майкрософт для Android (MSAL), [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal), которая помогает реализовать проверку подлинности.

 MSAL автоматически обновляет маркеры, обеспечивает единый вход для других приложений на устройстве, а также управляет учетными записями.

### <a name="prerequisites"></a>Предварительные требования

* Для работы с этим учебником требуется Android Studio версии 3.5 или более новой.

## <a name="create-a-project"></a>Создание проекта
Если у вас еще нет приложения Android, выполните следующие действия, чтобы настроить новый проект. 

1. Запустите Android Studio и выберите **Start a new Android Studio project** (Создать проект Android Studio).
2. Выберите **Basic Activity** (Базовое действие) и щелкните **Next** (Далее).
3. Присвойте имя приложению.
4. Сохраните имя пакета. Позже вы укажете его на портале Azure.
5. Измените язык с **Kotlin** на **Java**.
6. Присвойте параметру **Minimum API level** (Минимальный уровень API) задайте **API 19** или выше и щелкните **Finish** (Готово).
7. В представлении проекта в раскрывающемся списке выберите **Project** (Проект), чтобы отобразить исходные и другие файлы проекта, откройте **app/build.gradle** и задайте параметру `targetSdkVersion` значение `28`.

## <a name="integrate-with-microsoft-authentication-library"></a>Интеграция с библиотекой проверки подлинности Майкрософт 

### <a name="register-your-application"></a>Регистрация приложения

1. Перейдите на [портал Azure](https://aka.ms/MobileAppReg).
2. На панели [Регистрация приложений](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) щелкните **+Новая регистрация**.
3. Введите **Имя** приложения, а затем, **не указывая** URI перенаправления, щелкните **Зарегистрировать**.
4. На панели в открывшемся разделе **Управление** выберите **Аутентификация** >  **+Добавить платформу** > **Android**. (Чтобы увидеть этот раздел, возможно, потребуется выбрать Switch to the new experience (Переключиться на новый интерфейс) рядом с верхней частью колонки).
5. Введите имя пакета проекта. Если вы скачали код, это будет значение `com.azuresamples.msalandroidapp`.
6. В разделе **Хэш подписи** на странице **Настройка приложения Android** щелкните **Generating a development Signature Hash** (Создания хэша подписи для разработки) и скопируйте команду KeyTool для использования с вашей платформой.

   > [!Note]
   > KeyTool. exe устанавливается как часть пакета средств разработки Java (JDK). Необходимо также установить средство OpenSSL для выполнения команды KeyTool. Дополнительные сведения см. в [документации Android по созданию ключа](https://developer.android.com/studio/publish/app-signing#generate-key). 

7. Введите **хэш подписи**, созданный с помощью KeyTool.
8. Щелкните `Configure` и сохраните **конфигурацию MSAL**, отображаемую на странице **Настройка Android**, чтобы указать ее при последующей настройке приложения.  Нажмите кнопку **Done**(Готово).

### <a name="configure-your-application"></a>Настройка приложения 

1. На панели проекта Android Studio перейдите к **app\src\main\res**.
2. Щелкните правой кнопкой мыши **res** и выберите **New** (Создать)  > **Directory** (Каталог). Введите `raw` в качестве имени каталога и нажмите кнопку **ОК**.
3. В расположении**app** > **src** > **main** > **res** > **raw** создайте файл JSON с именем `auth_configbn_single_account.json` и вставьте в него ранее сохраненную конфигурацию MSAL. 

    Поместите следующие данные под URI перенаправления: 
    ```json
      "account_mode" : "SINGLE",
    ```
    Теперь файл конфигурации должен выглядеть примерно так: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```
    
   >[!NOTE]
   >В этом учебнике настройка приложения демонстрируется только в режиме одной учетной записи. В документации вы найдете дополнительные сведения [о режимах одной и нескольких учетных записей](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) и [о настройке приложения](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration).
   
4. В **app** > **src** > **main** > **AndroidManifest.xml** добавьте действие `BrowserTabActivity` в код приложения, как показано ниже. Эта запись позволяет корпорации Майкрософт выполнять обратный вызов приложения после завершения проверки подлинности:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Замените имя пакета, зарегистрированного на портале Azure, значением `android:host=`.
    Замените хэш ключа, зарегистрированный на портале Azure, значением `android:path=`. **Недопустимо** применять к хэшу подписи кодирование по правилам для URL-адреса. Убедитесь, что в начале хэша подписи присутствует символ `/`. 
    >[!NOTE]
    >Заполнитель "Package Name" (Имя пакета) вы замените значением `android:host` примерно такого вида: "com.azuresamples.msalandroidapp", а заполнитель "Signature Hash" — значением `android:path` такого вида: "/1wIqXSqBj7w+h11ZifsnqwgyKrY=". Эти же значения можно найти в колонке "Аутентификация" в регистрации приложения. Обратите внимание, что URI перенаправления будет выглядеть следующим образом: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Несмотря на то, что к хэшу подписи в конце этого значения применяется кодирование по правилам для URL-адреса, в значении `android:path` хэш подписи должен быть указан **без** этого кодирования. 

## <a name="use-msal"></a>Использование MSAL 

### <a name="add-msal-to-your-project"></a>Добавление MSAL в проект

1. В окне проекта Android Studio перейдите к **app** > **src** > **build.gradle** и выполните следующее. 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Подробнее о пакете средства разработки Microsoft Graph](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Необходимые операторы import 

Добавьте представленный ниже текст в начало файла **app** > **src** > **main**> **java** > **com.example(yourapp)**  > **MainActivity.java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Создание экземпляра PublicClientApplication
#### <a name="initialize-variables"></a>Инициализация переменных 
```java
private final static String[] SCOPES = {"File.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
В классе `MainActivity` обратитесь к следующему методу OnCreate(), чтобы создать экземпляр MSAL с помощью `SingleAccountPublicClientApplication`.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }
        
        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
Ожидайте нажатия кнопок, а затем вызывайте методы или регистрируйте ошибки соответствующим образом. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Если функция выхода реализована с помощью MSAL, из приложения удаляется вся известная информация о пользователе, но сеанс на устройстве пользователя продолжает быть активным. Если пользователь пытается снова выполнить вход, может отобразиться интерфейс входа, в котором может не потребоваться повторно вводить учетные данные, так как сеанс на устройстве остается активным. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Обратный вызов, используемый для интерактивных запросов.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Обратный вызов, используемый для запросов без уведомления. 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Вызов API Microsoft Graph 

В следующем примере кода показано, как обращаться к GraphAPI через пакет средств разработки Graph. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Добавление пользовательского интерфейса
### <a name="activity"></a>Действие 
Если вы хотите создать пользовательский интерфейс на основе примера из этого учебника, изучите следующие методы для понимания процессов обновления текста и прослушивания кнопок.

#### <a name="updateui"></a>updateUI
Включение и отключение кнопок на основе состояния входа и заданного текста.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Метод для обновления текста в пользовательском интерфейсе в соответствии с выходом. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Макет 

Пример файла `activity_main.xml` для вывода кнопок и текстовых полей. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Тестирование приложения

### <a name="run-locally"></a>Локальный запуск

Выполните сборку и развертывание приложения на тестовом устройстве или в эмуляторе. При этом должен выполняться вход в приложение и получение маркеров для учетных записей Azure AD или личных учетных записей Майкрософт.

После выполнения входа в приложении будут отображаться данные, возвращенные из конечной точки Microsoft Graph `/me`.

### <a name="consent"></a>Согласие на предоставление разрешений

При первом входе любого пользователя в приложение от платформы удостоверений Майкрософт появится запрос на предоставление согласия на запрашиваемые разрешения. Некоторые арендаторы Azure AD отключили согласие пользователей, что предусматривает получение согласия администраторов от имени всех пользователей. Для поддержки этого сценария вам придется создать собственный клиент или получить согласие администратора. 

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ненужный объект приложения, созданный на шаге [Регистрация приложения](#register-your-application).

## <a name="get-help"></a>Получить справку

Если у вас возникли вопросы касательно этого руководства или платформы удостоверений Майкрософт, см. статью, посвященную [справке и поддержке](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options).

Помогите нам улучшить платформу Microsoft Identity. Поделитесь своим мнением, ответив на два вопроса.

> [!div class="nextstepaction"]
> [Опрос по платформе удостоверений Майкрософт](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
