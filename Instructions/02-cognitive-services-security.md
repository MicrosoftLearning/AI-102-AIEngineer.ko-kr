---
lab:
  title: Cognitive Services 보안 관리
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: 9c8de44265ffa0846b6860fd7d416bb3be547ed9
ms.sourcegitcommit: 5ffc20f6a590fe643c2b695b8dc04589411be36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2022
ms.locfileid: "145951185"
---
# <a name="manage-cognitive-services-security"></a>Cognitive Services 보안 관리

모든 애플리케이션에서 보안은 중요한 고려 사항입니다. 개발자는 Cognitive Services 등의 리소스가 필요한 사용자만 해당 리소스에 액세스할 수 있도록 제한해야 합니다.

일반적으로는 인증 키를 통해Cognitive Services 액세스를 제어합니다. 인증 키는 Cognitive Services 리소스를 처음 만들 때 생성됩니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

구독에 아직 없는 경우 **Cognitive Services** 리소스를 프로비전해야 합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *cognitive services* 를 검색한 후에 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.

## <a name="manage-authentication-keys"></a>인증 키 관리

Cognitive Services 리소스를 만들면 인증 키 2개가 생성됩니다. 이러한 키는 Azure Portal에서 관리할 수도 있고 Azure CLI(명령줄 인터페이스)를 사용하여 관리할 수도 있습니다.

1. Azure Portal에서 Cognitive Services 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 이 페이지에는 리소스에 연결하여 직접 개발한 애플리케이션에서 해당 리소스를 사용하려면 필요한 정보가 포함되어 있습니다. 특히 다음 사항에 주의하세요.
    - 클라이언트 애플리케이션이 요청을 보낼 수 있는 HTTP *엔드포인트*
    - 인증에 사용할 수 있는 *키* 2개(클라이언트 애플리케이션은 두 키 중 하나를 사용할 수 있습니다. 일반적으로는 개발과 프로덕션용으로 키를 하나씩 사용합니다. 개발자가 작업을 완료하고 나면 리소스에 계속 액세스할 수 없도록 개발 키를 쉽게 다시 생성할 수 있습니다.)
    - 리소스가 호스트되는 *위치*. 일부 API(모든 API는 아님)에 요청을 보내려면 이 위치가 필요합니다.
2. Visual Studio Code에서 **02-cognitive-security** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 후에 다음 명령을 입력하여 Azure CLI를 사용해 Azure 구독에 로그인합니다.

    ```
    az login
    ```

    웹 브라우저 탭이 열리고 Azure에 로그인하라는 메시지가 표시됩니다. Azure에 로그인한 다음 브라우저 탭을 닫고 Visual Studio Code로 돌아옵니다.

    > **팁**: 구독이 여러 개이면 Cognitive Services 리소스가 포함된 구독에서 작업 중인지를 확인해야 합니다.  다음 명령을 사용하여 현재 구독을 확인합니다. 반환되는 JSON의 **id** 값이 구독의 고유 ID입니다.

    > **경고**: `az login`에 대한 인증서 꼭짓점 오류가 발생하는 경우 몇 분 정도 기다렸다가 다시 시도하세요.
    >
    > ```
    > az account show
    > ```
    >
    > 구독을 변경해야 하는 경우 *&lt;Your_Subscription_Id&gt;* 를 올바른 구독 ID로 변경하여 다음 명령을 실행합니다.
    >
    > ```
    > az account set --subscription <Your_Subscription_Id>
    > ```
    >
    > 이후에 실행하는 각 Azure CLI 명령에서 명시적으로 구독 ID를 *--subscription* 매개 변수로 지정할 수도 있습니다.

3. 이제 다음 명령을 사용하여 Cognitive Services 키 목록을 가져올 수 있습니다. 명령에서 *&lt;resourceName&gt;* 은 Cognitive Services 리소스 이름으로, *&lt;resourceGroup&gt;* 은 해당 리소스를 만든 리소스 그룹 이름으로 바꾸세요.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

이 명령은 Cognitive Services 리소스의 키 목록을 반환합니다. 목록에는 **key1** 및 **key2** 의 2개 키가 포함되어 있습니다.

4. Cognitive Service를 테스트하려는 경우 HTTP 요청용 명령줄 도구인 **curl** 을 사용하면 됩니다. **02-cognitive-security** 폴더에서 **rest-test.cmd** 를 열고 이 파일에 포함되어 있는 **curl** 명령(아래에 나와 있음)을 편집합니다. Cognitive Services 리소스의 Text Analytics API를 사용하도록 *&lt;yourEndpoint&gt;* 및 *&lt;yourKey&gt;* 를 엔드포인트 URI와 **Key1** 키로 바꾸면 됩니다.

    ```
    curl -X POST "<yourEndpoint>/text/analytics/v3.0/languages?" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <yourKey>" --data-ascii "{'documents':[{'id':1,'text':'hello'}]}"
    ```

5. 변경 내용을 저장하고 **02-cognitive-security** 폴더의 통합 터미널에서 다음 명령을 실행합니다.

    ```
    rest-test
    ```

이 명령은 입력 데이터에서 감지된 언어(영어) 관련 정보가 포함된 JSON 문서를 반환합니다.

6. 키가 손상되었거나 키를 소유한 개발자가 더 이상 리소스에 액세스할 필요가 없는 경우 Azure Portal에서 Azure CLI를 사용하여 키를 다시 생성할 수 있습니다. 다음 명령을 실행하여 **key1** 키를 다시 생성합니다( *&lt;resourceName&gt;* 및 *&lt;resourceGroup&gt;* 은 리소스 관련 정보로 바꿔야 함).

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

Cognitive Services 리소스용 키 목록이 반환됩니다. **key1** 이 마지막으로 검색한 이후로 변경되었음을 확인할 수 있습니다.

7. 이전 키를 사용하여 **rest-test** 명령을 다시 실행하고( **^** 키를 사용하여 이전 명령을 차례로 다시 실행할 수 있음) 이번에는 명령 실행이 실패함을 확인합니다.
8. **rest-test.cmd** 의 *curl* 명령을 편집하여 키를 새 **key1** 값으로 바꾸고 변경 내용을 저장합니다. 그런 다음 **rest-test** 명령을 다시 실행하여 정상적으로 실행됨을 확인합니다.

> **팁**: 이 연습에서는 **--resource-group** 과 같은 Azure CLI 매개 변수의 전체 이름을 사용했습니다.  **-g** 등의 더 짧은 이름을 사용하면 명령을 더 간단하게 작성할 수도 있습니다(하지만 명령을 이해하기는 좀 더 어려울 수도 있음).  각 Cognitive Services CLI 명령용 매개 변수 옵션의 목록은 [Cognitive Services CLI 명령 참조](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest)에 나와 있습니다.

## <a name="secure-key-access-with-azure-key-vault"></a>Azure Key Vault를 사용하여 키 액세스 보호

인증용 키를 사용하면 Cognitive Services를 사용하는 애플리케이션을 개발할 수 있습니다. 하지만 이 경우에는 애플리케이션 코드가 키를 가져올 수 있어야 합니다. 애플리케이션이 배포되는 구성 파일이나 환경 변수에 키를 저장하는 옵션을 사용할 수도 있습니다. 그러나 이 방식을 사용하면 키가 무단 액세스에 취약해집니다. Azure에서 애플리케이션을 개발할 때 사용 가능한 더 효율적인 방식은 Azure Key Vault에 키를 안전하게 저장한 다음 *관리 ID*(애플리케이션 자체에 사용되는 사용자 계정)를 통해 키 액세스 권한을 제공하는 것입니다.

### <a name="create-a-key-vault-and-add-a-secret"></a>키 자격 증명 모음 만들기 및 비밀 추가

먼저 키 자격 증명 모음을 만들고 Cognitive Services 키의 *비밀* 을 추가해야 합니다.

1. Azure Cognitive Search 리소스의 **key1** 값을 적어 둡니다(클립보드에 복사해도 됨).
2. Azure Portal **홈** 페이지에서 **&#65291;리소스 만들기** 단추를 선택하고 *Key Vault* 를 검색한 후에 다음 설정을 사용하여 **Key Vault** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: *Cognitive Service 리소스와 같은 리소스 그룹*
    - **Key Vault 이름**: 고유한 이름 입력
    - **지역**: Cognitive Service 리소스와 같은 지역
    - **가격 책정 계층**: 표준
3. 배포가 완료될 때까지 기다렸다가 키 자격 증명 모음 리소스로 이동합니다.
4. 왼쪽 탐색 창의 설정 섹션에서 **비밀** 을 선택합니다.
5. **+ 생성/가져오기** 를 선택하고 다음 설정을 사용하여 새 비밀을 추가합니다.
    - **업로드 옵션**: 수동
    - **이름**: Cognitive-Services-Key *(뒷부분에서 이 이름을 기준으로 비밀을 검색하는 코드를 실행할 것이므로 이 이름을 정확하게 사용해야 함)*
    - **값**: **key1** Cognitive Services 키

### <a name="create-a-service-principal"></a>서비스 주체 만들기

키 자격 증명 모음의 비밀에 액세스하려면 애플리케이션이 비밀 액세스 권한이 있는 서비스 주체를 사용해야 합니다. Azure CLI(명령줄 인터페이스)를 사용하여 서비스 주체를 만들고, 해당 개체 ID를 찾고, Azure Vault의 비밀에 대한 액세스 권한을 부여합니다.

1. Visual Studio Code로 돌아와 **02-cognitive-security** 폴더의 통합 터미널에서 다음 Azure CLI 명령을 실행합니다. 이때 *&lt;spName&gt;* 은 애플리케이션 ID에 적합한 이름(예: *ai-app*)으로 바꿉니다. 그리고 *&lt;subscriptionId&gt;* 및 *&lt;resourceGroup&gt;* 도 구독 ID, 그리고 Cognitive Services 및 키 자격 증명 모음 리소스가 포함된 리소스 그룹의 올바른 값으로 바꿉니다.

    > **팁**: 구독 ID를 모르는 경우 **az account show** 명령을 실행하여 구독 정보를 검색합니다. 출력의 **id** 특성이 구독 ID입니다.

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

이 명령의 출력에는 새 서비스 주체 관련 정보가 포함되어 있습니다. 다음과 같이 표시됩니다.

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "ai-app",
        "name": "http://ai-app",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

**appId**, **password** 및 **tenant** 값은 뒷부분에서 필요하므로 적어 두세요(이 터미널을 닫으면 암호를 검색할 수가 없으므로 값을 지금 적어 두어야 합니다. 나중에 필요한 값을 찾을 수 있도록 Visual Studio Code에서 새 텍스트 파일에 출력을 붙여넣을 수 있습니다).

2. 서비스 주체의 **개체 ID** 를 가져오려면 다음 Azure CLI 명령을 실행하여 *&lt;appId&gt;* 를 서비스 주체의 앱 ID 값으로 바꿉니다.

    ```
    az ad sp show --id <appId> --query objectId --out tsv
    ```

3. 새 서비스 주체에 Key Vault의 비밀 액세스 권한을 할당하려면 다음 Azure CLI 명령을 실행합니다. 이때 *&lt;keyVaultName&gt;* 은 Azure Key Vault 리소스의 이름으로, *&lt;objectId&gt;* 는 서비스 주체의 개체 ID 값으로 바꿉니다.

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### <a name="use-the-service-principal-in-an-application"></a>애플리케이션에서 서비스 주체 사용

이제 애플리케이션에서 서비스 주체 ID를 사용할 수 있습니다. 이 ID를 사용하면 애플리케이션이 키 자격 증명 모음의 비밀 Cognitive Services 키에 액세스한 다음 Cognitive Services 리소스에 연결하는 데 해당 키를 사용할 수 있습니다.

> **참고**: 이 연습에서는 애플리케이션 구성에 서비스 주체 자격 증명을 저장한 다음 애플리케이션 코드의 **ClientSecretCredential** ID를 인증하는 데 사용합니다. 개발 및 테스트 애플리케이션에서는 이러한 방식을 사용해도 되지만 실제 프로덕션 애플리케이션에서는 관리자가 애플리케이션에 *관리 ID* 를 할당합니다. 그러면 애플리케이션이 암호를 저장하거나 캐시하지 않고 서비스 주체 ID를 사용해 리소스에 액세스할 수 있습니다.

1. Visual Studio Code에서 **02-cognitive-security** 폴더를 확장하고 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **keyvault-client** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 다음 언어 기본 설정에 적합한 명령을 실행하여 Cognitive Services 리소스에서 Azure Key Vault 및 Text Analytics API를 사용하는 데 필요한 패키지를 설치합니다.

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.1.0
    dotnet add package Azure.Identity --version 1.5.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.2.0-beta.3
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.1.0
    pip install azure-identity==1.5.0
    pip install azure-keyvault-secrets==4.2.0
    ```

3. **keyvault-client** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 포함되어 있는 구성 값을 업데이트하여 다음 설정을 반영합니다.
    
    - Cognitive Services 리소스의 **엔드포인트**
    - **Azure Key Vault** 리소스의 이름
    - 서비스 주체의 **테넌트**
    - 서비스 주체의 **appId**
    - 서비스 주체의 **암호**

     변경 내용을 저장합니다.
4. **keyvault-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: keyvault-client.py

    코드 파일을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 SDK의 네임스페이스를 가져왔습니다.
    - **Main** 함수의 코드가 애플리케이션 구성 설정을 검색하며 서비스 주체 자격 증명을 사용하여 키 자격 증명 모음에서 Cognitive Services 키를 가져옵니다.
    - **GetLanguage** 함수가 SDK를 사용해 서비스용 클라이언트를 만든 다음 해당 클라이언트를 사용하여 입력한 텍스트의 언어를 감지합니다.
5. **keyvault-client** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. 메시지가 표시되면 텍스트를 입력하고 서비스가 감지하는 언어를 검토합니다. 예를 들어, “Hello”, “Bonjour”, “Hola” 등을 입력해 봅니다.
7. 애플리케이션 테스트를 완료한 후 "quit"을 입력하여 프로그램을 중지합니다.

## <a name="more-information"></a>추가 정보

Cognitive Services를 보호하는 방법에 대한 자세한 내용은 [Cognitive Services 보안 설명서](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security)를 참조하세요.
