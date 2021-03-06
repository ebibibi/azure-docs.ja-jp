---
title: "ASP.NET Web アプリでユーザー認証に Azure Active Directory B2C を使用するチュートリアル"
description: "ASP.NET Web アプリで Azure Active Directory B2C を使用してユーザーをサインインおよびサインアップする方法に関するチュートリアル。"
services: active-directory-b2c
author: PatAltimore
ms.author: patricka
ms.reviewer: saraford
ms.date: 1/23/2018
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory-b2c
ms.openlocfilehash: ee006476f9e40e9d1a6e7213cb1881ca46ea75c2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-authenticate-users-with-azure-active-directory-b2c-in-an-aspnet-web-app"></a>チュートリアル: ASP.NET Web アプリで Azure Active Directory B2C を使用してユーザーを認証する

このチュートリアルでは、ASP.NET Web アプリで Azure Active Directory (Azure AD) B2C を使用してユーザーをサインインおよびサインアップする方法を紹介します。 Azure AD B2C を使用すると、アプリは、オープンな標準プロトコルを使用してソーシャル アカウント、エンタープライズ アカウント、Azure Active Directory アカウントに対して認証することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD B2C テナントにサンプルの ASP.NET Web アプリを登録する。
> * ユーザーのサインアップ、サインイン、プロファイルの編集、パスワードのリセットに関するポリシーを作成する。
> * Azure AD B2C テナントを使用するようにサンプル Web アプリを構成する。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 独自の [Azure AD B2C テナント](active-directory-b2c-get-started.md)を作成する。
* **ASP.NET および Web 開発**のワークロードと共に、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールする。

## <a name="register-web-app"></a>Web アプリの登録

Azure Active Directory から[アクセス トークン](../active-directory/develop/active-directory-dev-glossary.md#access-token)を受け取ることができるように、アプリケーションをテナントに[登録](../active-directory/develop/active-directory-dev-glossary.md#application-registration)しておく必要があります。 アプリの登録によって、テナント内のアプリの[アプリケーション ID](../active-directory/develop/active-directory-dev-glossary.md#application-id-client-id) が作成されます。 

Azure AD B2C テナントの全体管理者として、[Azure Portal](https://portal.azure.com/) にログインします。

[!INCLUDE [active-directory-b2c-switch-b2c-tenant](../../includes/active-directory-b2c-switch-b2c-tenant.md)]

1. Azure Portal のサービス一覧から **[Azure AD B2C]** を選択します。

2. B2C の設定で、**[アプリケーション]** をクリックし、**[追加]** をクリックします。

    テナントにサンプル Web アプリを登録するには、以下の設定を使用します。

    ![新しいアプリの追加](media/active-directory-b2c-tutorials-web-app/web-app-registration.png)

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | My Sample Web App | 使用者がアプリの機能を把握できる**名前**を入力します。 | 
    | **Web アプリ/Web API を含める** | [はい] | Web アプリの場合は **[はい]** を選択します。 |
    | **暗黙的フローを許可する** | [はい] | アプリでは [OpenID Connect サインイン](active-directory-b2c-reference-oidc.md)が使用されるため、**[はい]** を選択します。 |
    | **応答 URL** | `https://localhost:44316` | 応答 URL は、アプリが要求したトークンを Azure AD B2C が返すエンドポイントです。 このチュートリアルでは、サンプルはローカル (localhost) で実行され、ポート 44316 でリッスンします。 |
    | **ネイティブ クライアント** | いいえ  | これはネイティブ クライアントではなく Web アプリのため、[いいえ] を選択します。 |

3. **[作成]** をクリックしてアプリを登録します。

登録されたアプリは、Azure AD B2C テナントのアプリケーション一覧に表示されます。 一覧から Web アプリを選択します。 Web アプリのプロパティ ウィンドウが表示されます。

![Web アプリのプロパティ](./media/active-directory-b2c-tutorials-web-app/b2c-web-app-properties.png)

**[アプリケーション クライアント ID]** をメモします。 この ID はアプリを一意に識別するため、このチュートリアルの後半でアプリを構成する際に必要になります。

### <a name="create-a-client-password"></a>クライアント パスワードの作成

Azure AD B2C では、[クライアント アプリケーション](../active-directory/develop/active-directory-dev-glossary.md#client-application)に OAuth2 承認が使用されます。 Web アプリは [Confidential クライアント](../active-directory/develop/active-directory-dev-glossary.md#web-client)であり、クライアント シークレット (パスワード) を必要とします。 アプリケーション クライアント ID とクライアント シークレットは、Web アプリが Azure Active Directory で認証されるときに使用されます。 

1. 登録されている Web アプリの [キー] ページを選択し、**[キーの生成]** をクリックします。

2. **[保存]** をクリックしてキーを表示します。

    ![アプリの [キーの生成] ページ](media/active-directory-b2c-tutorials-web-app/app-general-keys-page.png)

このキーはポータルに 1 回表示されます。 重要なのは、このキー値をコピーして保存することです。 この値は、アプリを構成するために必要です。 キーを安全に保管してください。 キーを公開しないでください。

## <a name="create-policies"></a>ポリシーの作成

Azure AD B2C ポリシーでは、ユーザー ワークフローを定義します。 たとえば、サインイン、サインアップ、パスワードの変更、プロファイルの編集は一般的なワークフローです。

### <a name="create-a-sign-up-or-sign-in-policy"></a>サインアップまたはサインイン ポリシーを作成する

Web アプリにアクセスしてサインインするためにユーザーのサインアップを実行するには、**サインアップまたはサインイン ポリシー**を作成します。

1. Azure AD B2C ポータル ページから、**[サインアップまたはサインイン ポリシー]** を選択し、**[追加]** をクリックします。

    ポリシーを構成するには、次の設定を使用します。

    ![サインアップまたはサインイン ポリシーの追加](media/active-directory-b2c-tutorials-web-app/add-susi-policy.png)

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | SiUpIn | ポリシーの**名前**を入力します。 ポリシー名の先頭には **b2c_1_** が付きます。 このサンプル コードでは、完全なポリシー名 **b2c_1_SiUpIn** を使用します。 | 
    | **ID プロバイダー** | 電子メールのサインアップ | ユーザーを一意に識別するために使用される ID プロバイダー。 |
    | **サインアップ属性** | 表示名、郵便番号 | サインアップ中にユーザーから収集する属性を選択します。 |
    | **アプリケーション要求** | 表示名、郵便番号、新規ユーザー、ユーザーのオブジェクト ID | [アクセス トークン](../active-directory/develop/active-directory-dev-glossary.md#access-token)に含める[要求](../active-directory/develop/active-directory-dev-glossary.md#claim)を選択します。 |

2. **[作成]** をクリックしてポリシーを作成します。 

### <a name="create-a-profile-editing-policy"></a>プロファイル編集ポリシーを作成する

ユーザーが自身でユーザー プロファイル情報をリセットできるようにするには、**プロファイルの編集ポリシー**を作成します。

1. Azure AD B2C ポータルページから、**[プロファイルの編集ポリシー]** を選択し、**[追加]** をクリックします。

    ポリシーを構成するには、次の設定を使用します。

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | SiPe | ポリシーの**名前**を入力します。 ポリシー名の先頭には **b2c_1_** が付きます。 このサンプル コードでは、完全なポリシー名 **b2c_1_SiPe** を使用します。 | 
    | **ID プロバイダー** | ローカル アカウント サインイン | ユーザーを一意に識別するために使用される ID プロバイダー。 |
    | **プロファイル属性** | 表示名、郵便番号 | ユーザーがプロファイルの編集中に変更できる属性を選択します。 |
    | **アプリケーション要求** | 表示名、郵便番号、新規ユーザー、ユーザーのオブジェクト ID | プロファイル編集が成功した後に[アクセス トークン](../active-directory/develop/active-directory-dev-glossary.md#access-token)に含める[要求](../active-directory/develop/active-directory-dev-glossary.md#claim)を選択します。 |

2. **[作成]** をクリックしてポリシーを作成します。 

### <a name="create-a-password-reset-policy"></a>パスワードのリセット ポリシーを作成する

アプリケーションでパスワードのリセットを有効にするには、**パスワードのリセット ポリシー**を作成する必要があります。 このポリシーでは、パスワードのリセット時のコンシューマーのエクスペリエンスと、正常に完了したときにアプリケーションが受け取るトークンの内容を記述します。

1. Azure AD B2C ポータル ページから、**[パスワードのリセット ポリシー]** を選択し、**[追加]** をクリックします。

    ポリシーを構成するには、次の設定を使用します。

    | Setting      | 推奨値  | Description                                        |
    | ------------ | ------- | -------------------------------------------------- |
    | **名前** | SSPR | ポリシーの**名前**を入力します。 ポリシー名の先頭には **b2c_1_** が付きます。 このサンプル コードでは、完全なポリシー名 **b2c_1_SSPR** を使用します。 | 
    | **ID プロバイダー** | Reset password using email address (電子メール アドレスを使用してパスワードをリセットする) | これは、ユーザーを一意に識別するために使用される ID プロバイダーです。 |
    | **アプリケーション要求** | ユーザーのオブジェクト ID | パスワードのリセットが成功した後に[アクセス トークン](../active-directory/develop/active-directory-dev-glossary.md#access-token)に含める[要求](../active-directory/develop/active-directory-dev-glossary.md#claim)を選択します。 |

2. **[作成]** をクリックしてポリシーを作成します。 

## <a name="update-web-app-code"></a>Web アプリ コードの更新

Web アプリの登録とポリシーの作成が完了したら、Azure AD B2C テナントを使用するようアプリを構成する必要があります。 このチュートリアルでは、サンプル Web アプリを構成します。 

[ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)するか、GitHub からサンプル Web アプリを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

サンプル ASP.NET Web アプリは、To Do リストを作成および更新するための簡単なタスク リスト アプリです。 このアプリでは [Microsoft OWIN ミドルウェア コンポーネント](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/)を使用して、ユーザーがサインアップして Azure AD B2C テナントでアプリを使用できるようにします。 Azure AD B2C ポリシーを作成することにより、ユーザーはソーシャル アカウントを使用したり、アプリにアクセスするための ID として使用するアカウントを作成したりできます。 

サンプル ソリューションには 2 つのプロジェクトがあります。

**Web アプリのサンプル アプリ (TaskWebApp):** タスク リストを作成および編集するための Web アプリ。 この Web アプリでは、**サインアップまたはサインイン** ポリシーを使用して、メール アドレスでユーザーをサインアップまたはサインインします。

**Web API のサンプル アプリ (TaskService):** タスク リストの作成、読み取り、更新、削除機能をサポートする Web API。 この Web API は Azure AD B2C によって保護されており、Web アプリによって呼び出されます。

テナントでアプリの登録を使用するには、アプリを変更する必要があります。 また、作成したポリシーを構成する必要もあります。 サンプル Web アプリでは、Web.config ファイルでアプリの設定として構成値を定義します。 アプリの設定を変更するには、次の手順に従います。

1. Visual Studio で **B2C-WebAPI-DotNet** ソリューションを開きます。

2. **TaskWebApp** Web アプリ プロジェクトで、**Web.config** ファイルを開き、次の更新を行います。

    ```C#
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    
    <add key="ida:ClientId" value="The Application ID for your web app registered in your tenant" />
    
    <add key="ida:ClientSecret" value="Client password (client secret)" />
    ```
3. ポリシーの作成時に生成された名前で、ポリシーの設定を更新します。

    ```C#
    <add key="ida:SignUpSignInPolicyId" value="b2c_1_SiUpIn" />
    <add key="ida:EditProfilePolicyId" value="b2c_1_SiPe" />
    <add key="ida:ResetPasswordPolicyId" value="b2c_1_SSPR" />
    ```

## <a name="run-the-sample-web-app"></a>サンプル Web アプリの実行

ソリューション エクスプローラーで、**TaskWebApp** プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックします。

**F5** キーを押して Web アプリを起動します。 既定のブラウザーで、ローカルの Web サイトアドレス `https://localhost:44316/` が開かれます。 

このサンプル アプリでは、サインアップ、サインイン、プロファイルの編集、パスワードのリセットがサポートされています。 ユーザーがアプリを使用するためにメール アドレスでサインアップする方法を次に示します。 ご自身で他のシナリオを試すこともできます。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

1. 上部のバナーにある **[Sign up / Sign in]\(サインアップ/サインイン\)** リンクをクリックして、Web アプリのユーザーとしてサインアップします。 これは、前の手順で定義した **b2c_1_SiUpIn** ポリシーを使用します。

2. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、**[今すぐサインアップ]** リンクをクリックします。 

3. サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ポリシーで定義されているユーザーのパスワードと要求された属性も収集されます。

    有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。 

    ![サインアップ ワークフロー](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.png)

4. **[作成]** をクリックして、Azure AD B2C テナントにローカル アカウントを作成します。

これで、ユーザーはメールアドレスを使用してサインインし、Web アプリを使用できるようになりました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

他の Azure AD B2C チュートリアルを試す場合は、Azure AD B2C テナントを使用できます。 不要になったら、[Azure AD B2C テナントを削除する](active-directory-b2c-faqs.md#how-do-i-delete-my-azure-ad-b2c-tenant)ことができます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure AD B2C テナントを作成し、ポリシーを作成して、Azure AD B2C テナントを使用するようにサンプル Web アプリを更新する方法について学習しました。 Azure AD B2C テナントで保護されている ASP.NET Web API の登録、構成、呼び出しを行う方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C を使用して ASP.NET Web API を保護する](active-directory-b2c-tutorials-web-api.md)
