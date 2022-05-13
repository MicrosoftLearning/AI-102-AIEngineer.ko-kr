---
lab:
  title: Cognitive Services 시작
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: 4baba38b03c6d7bb5fe04fa5e73bb606e970550b
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2022
ms.locfileid: "137819510"
---
# <a name="get-started-with-cognitive-services"></a>Cognitive Services 시작

이 연습에서는 Azure 구독에서 **Cognitive Services** 리소스를 만든 후 클라이언트 애플리케이션에서 해당 리소스를 사용하는 것으로 Cognitive Services 사용을 시작합니다. 이 연습의 목표는 특정 서비스 관련 전문 지식을 습득하는 것이 아니라 개발자가 Cognitive Services를 프로비전 및 사용하는 일반적인 패턴을 파악하는 것입니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P 누르기) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

Azure Cognitive Services는 애플리케이션에 통합할 수 있는 인공 지능 기능이 캡슐화된 클라우드 기반 서비스입니다. 특정 API용 개별 Cognitive Services 리소스(예: **Language** 또는 **Computer Vision**)를 프로비저닝할 수도 있고, 엔드포인트와 키 하나를 통해 여러 Cognitive Services API에 액세스할 수 있는 일반 **Cognitive Services** 리소스를 프로비저닝할 수도 있습니다. 여기서는 단일 **Cognitive Services** 리소스를 사용합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *cognitive services* 를 검색한 후에 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 이 페이지에는 리소스에 연결하여 직접 개발한 애플리케이션에서 해당 리소스를 사용하려면 필요한 정보가 포함되어 있습니다. 특히 다음 사항에 주의하세요.
    - 클라이언트 애플리케이션이 요청을 보낼 수 있는 HTTP *엔드포인트*
    - 인증에 사용할 수 있는 *키* 2개(클라이언트 애플리케이션은 두 키 중 하나를 사용하여 인증할 수 있음)
    - 리소스가 호스트되는 *위치*. 일부 API(모든 API는 아님)에 요청을 보내려면 이 위치가 필요합니다.

## <a name="use-a-rest-interface"></a>REST 인터페이스 사용

Cognitive Services API는 REST를 기반으로 하므로 HTTP를 통해 JSON 요청을 제출하여 API를 사용할 수 있습니다. 이 연습에서는 **Language** REST API를 사용해 언어 감지를 수행하는 콘솔 애플리케이션을 살펴봅니다. 하지만 Cognitive Services 리소스가 지원하는 모든 API에는 동일한 기본 원칙이 적용됩니다.

> **참고**: 이 연습에서는 **C#** 또는 **Python** 의 REST API 사용을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **01-getting-started** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **rest-client** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 Cognitive Service 리소스용 **엔드포인트** 및 인증 **키** 를 반영하여 해당 파일에 포함된 구성 값을 업데이트합니다. 변경 내용을 저장합니다.
4. **rest-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: rest-client.py

    코드 파일을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - HTTP 통신을 수행할 수 있도록 여러 네임스페이스를 가져옵니다.
    - **Main** 함수의 코드가 Cognitive Services 리소스용 엔드포인트와 키를 검색합니다. 이러한 엔드포인트와 키를 사용하여 Text Analytics 서비스로 REST 요청을 전송합니다.
    - 프로그램이 사용자 입력을 수락한 다음 **GetLanguage** 함수를 사용하여 Cognitive Services 엔드포인트용 Text Analytics 언어 감지 REST API를 호출해 입력한 텍스트의 언어를 감지합니다.
    - API로 전송되는 요청이 입력 데이터가 들어 있는 JSON 개체로 구성되어 있습니다. 여기서 입력 데이터는 **document** 개체 컬렉션이며 각 개체에는 **id** 와 **text** 가 포함되어 있습니다.
    - 클라이언트 애플리케이션 인증을 위한 서비스용 키가 요청 헤더에 포함되어 있습니다.
    - 서비스의 응답이 클라이언트 애플리케이션에서 구문 분석할 수 있는 JSON 개체입니다.
5. **rest-client** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 후 다음 언어별 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python rest-client.py
    ```

6. 메시지가 표시되면 텍스트를 입력하고 서비스가 감지하는 언어를 검토합니다. 이 언어가 JSON 응답에서 반환됩니다. 예를 들어 "Hello", "Bonjour", "Hola" 등을 입력해 봅니다.
7. 애플리케이션 테스트를 완료한 후 "quit"을 입력하여 프로그램을 중지합니다.

## <a name="use-an-sdk"></a>SDK 사용

Cognitive Services REST API를 직접 사용하는 코드를 작성할 수도 있지만 Microsoft C#, Python, Node.js 등의 널리 사용되는 프로그래밍 언어용 SDK(소프트웨어 개발 키트)를 사용할 수도 있습니다. SDK를 활용하면 Cognitive Services를 사용하는 애플리케이션을 매우 간편하게 개발할 수 있습니다.

1. Visual Studio Code의 **탐색기** 창 **01-getting-started** 폴더에서 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **sdk-client** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 다음 언어 기본 설정에 적합한 명령을 실행하여 Text Analytics SDK 패키지를 설치합니다.

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.1.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.1.0
    ```

3. **sdk-client** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 Cognitive Service 리소스용 **엔드포인트** 및 인증 **키** 를 반영하여 해당 파일에 포함된 구성 값을 업데이트합니다. 변경 내용을 저장합니다.
    
4. **sdk-client** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: sdk-client.py

    코드 파일을 열고 포함되어 있는 코드를 검토하여 다음 세부 정보를 확인합니다.
    - 설치한 SDK의 네임스페이스를 가져왔습니다.
    - **Main** 함수의 코드가 Cognitive Services 리소스용 엔드포인트와 키를 검색합니다. SDK에서 이러한 엔드포인트와 키를 사용하여 Text Analytics 서비스용 클라이언트를 만듭니다.
    - **GetLanguage** 함수가 SDK를 사용해 서비스용 클라이언트를 만든 다음 해당 클라이언트를 사용하여 입력한 텍스트의 언어를 감지합니다.
5. **sdk-client** 폴더의 통합 터미널로 돌아와서 다음 명령을 입력하여 프로그램을 실행합니다.

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python sdk-client.py
    ```

6. 메시지가 표시되면 텍스트를 입력하고 서비스가 감지하는 언어를 검토합니다. 예를 들어 "Goodbye", "Au revoir", "Hasta la vista" 등을 입력해 봅니다.
7. 애플리케이션 테스트를 완료한 후 "quit"을 입력하여 프로그램을 중지합니다.

> **참고**: 유니코드 문자 집합이 필요한 일부 언어는 이 간단한 콘솔 애플리케이션에서 인식되지 않을 수도 있습니다.

## <a name="more-information"></a>추가 정보

Azure Cognitive Services에 대한 자세한 내용은 [Cognitive Services 설명서](https://docs.microsoft.com/azure/cognitive-services/what-are-cognitive-services)를 참조하세요.
