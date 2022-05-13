---
lab:
  title: Cognitive Services 모니터링
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: e0e0042421a4f7150fc3b95cef80887c81a78f3a
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2022
ms.locfileid: "137819436"
---
# <a name="monitor-cognitive-services"></a>Cognitive Services 모니터링

Azure Cognitive Services는 전체 애플리케이션 인프라에서 핵심적인 역할을 할 수 있습니다. 그러므로 활동을 모니터링하여 확인이 필요할 수 있는 문제 관련 경고를 받을 수 있어야 합니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P 누르기) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
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
3. 필요한 체크박스를 선택하여 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 나중에 필요하므로 엔드포인트 URI를 적어 둡니다.

## <a name="configure-an-alert"></a>경고 구성

Cognitive Services 리소스의 활동을 감지할 수 있도록 경고 규칙을 정의하는 것으로 모니터링을 시작해 보겠습니다.

1. Azure Portal에서 Cognitive Services 리소스로 이동하여 **경고** 페이지(**모니터링** 섹션에 있음)를 확인합니다.
2. **+ 새 경고 규칙** 을 선택합니다.
3. **경고 규칙 만들기** 페이지의 **범위** 아래에 Cognitive Services 리소스가 나열되어 있는지 확인합니다.
4. **조건** 아래에서 **조건 추가** 를 클릭하고 오른쪽에 표시되는 **신호 선택** 창을 확인합니다. 이 창에서 모니터링할 신호 유형을 선택할 수 있습니다.
5. **신호 유형** 목록에서 **활동 로그** 를 선택하고 필터링된 목록에서 **키 나열** 을 선택합니다.
6. 지난 6시간 동안의 활동을 검토합니다.
7. **작업** 탭을 선택합니다. 작업 그룹을 지정할 수 있습니다. 작업 그룹을 지정하면 경고 발생 시에 수행되는 전자 메일 알림 전송 등의 자동화된 작업을 구성할 수 있습니다. 이 연습에서는 자동화된 작업을 구성하지 않지만 프로덕션 환경에서는 이러한 작업을 구성하면 유용할 수 있습니다.
8. **세부 정보** 탭에서 **경고 규칙 이름** 을 **키 목록 경고** 로 설정합니다.
9. **검토 + 만들기** 를 선택합니다. 
10. 경고에 대한 구성을 검토합니다. **만들기** 를 선택하고, 경고 규칙이 만들어질 때까지 기다립니다.
11. Visual Studio Code에서 **03-monitor** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 후에 다음 명령을 입력하여 Azure CLI를 사용해 Azure 구독에 로그인합니다.

    ```
    az login
    ```

    웹 브라우저 탭이 열리고 Azure에 로그인하라는 메시지가 표시됩니다. Azure에 로그인한 다음 브라우저 탭을 닫고 Visual Studio Code로 돌아옵니다.

    > **팁**: 구독이 여러 개이면 Cognitive Services 리소스가 포함된 구독에서 작업 중인지를 확인해야 합니다.  현재 구독을 확인하려면 다음 명령을 사용합니다.
    >
    > ```
    > az account show
    > ```
    >
    > 구독을 변경해야 하는 경우 *&lt;subscriptionName&gt;* 을 올바른 구독 이름으로 변경하여 다음 명령을 실행합니다.
    >
    > ```
    > az account set --subscription <subscriptionName>
    > ```

10. 이제 다음 명령을 사용하여 Cognitive Services 키 목록을 가져올 수 있습니다. 명령에서 *&lt;resourceName&gt;* 은 Cognitive Services 리소스 이름으로, *&lt;resourceGroup&gt;* 은 해당 리소스를 만든 리소스 그룹 이름으로 바꾸세요.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

이 명령은 Cognitive Services 리소스의 키 목록을 반환합니다.

11. Azure Portal이 표시된 브라우저로 다시 전환하여 **경고 페이지** 를 새로 고칩니다. 표에 **심각도 4** 경고가 나열됩니다(이 경고가 나열되지 않으면 최대 5분까지 기다렸다가 페이지를 다시 새로 고치세요).
12. 경고를 선택하여 세부 정보를 봅니다.

## <a name="visualize-a-metric"></a>메트릭 시각화

Cognitive Services에서는 경고를 정의할 수 있을 뿐 아니라 Cognitive Services 리소스의 메트릭을 확인하여 리소스 사용률을 모니터링할 수도 있습니다.

1. Azure Portal의 Cognitive Services 리소스 페이지에서 **메트릭**(**모니터링** 섹션에 있음)을 선택합니다.
2. 기존 차트가 없으면 **+ 새 차트** 를 선택합니다. 그런 다음 **메트릭** 목록에서 시각화할 수 있는 메트릭을 검토하고 **총 통화** 를 선택합니다.
3. **집계** 목록에서 **개수** 를 선택합니다.  이렇게 하면 Cognitive Service 리소스에 대한 총 호출 수를 모니터링할 수 있습니다. 그러면 일정 기간 동안의 서비스 사용량을 확인하려는 경우 유용합니다.
4. Cognitive Service에 대해 요청을 몇 개 생성하려는 경우 HTTP 요청용 명령줄 도구인 **curl** 을 사용합니다. Visual Studio Code의 **03-monitor** 폴더에서 **rest-test.cmd** 를 열고 이 파일에 포함되어 있는 **curl** 명령(아래에 나와 있음)을 편집합니다. Cognitive Services 리소스의 Text Analytics API를 사용하도록 *&lt;yourEndpoint&gt;* 및 *&lt;yourKey&gt;* 를 엔드포인트 URI와 **Key1** 로 바꾸면 됩니다.

    ```
    curl -X POST "<yourEndpoint>/text/analytics/v3.1/languages?" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <yourKey>" --data-ascii "{'documents':           [{'id':1,'text':'hello'}]}"
    ```

5. 변경 내용을 저장하고 **03-monitor** 폴더의 통합 터미널에서 다음 명령을 실행합니다.

    ```
    rest-test
    ```

이 명령은 입력 데이터에서 감지된 언어(영어) 관련 정보가 포함된 JSON 문서를 반환합니다.

6. **rest-test** 명령을 여러 번 다시 실행하여 호출 활동을 생성합니다( **^** 키를 사용하여 이전 명령을 차례로 다시 실행할 수 있음).
7. Azure Portal의 **메트릭** 페이지로 돌아와 **총 통화** 개수 차트를 새로 고칩니다. *curl* 을 사용하여 수행한 호출이 차트에 반영되려면 몇 분 정도 걸릴 수 있습니다. 해당 호출이 포함되어 차트가 업데이트될 때까지 차트를 계속 새로 고칩니다.

## <a name="more-information"></a>추가 정보

Cognitive Services를 모니터링하는 옵션 중 하나는 *진단 로깅* 을 사용하는 것입니다. 진단 로깅을 사용하도록 설정하면 Cognitive Services 리소스 사용량과 관련된 다양한 정보가 캡처됩니다. 따라서 진단 로깅을 유용한 모니터링 및 디버깅 도구로 활용할 수 있습니다. 진단 로깅을 설정한 후 정보가 생성되기까지는 1시간 넘게 걸릴 수 있습니다. 이러한 이유로 인해 이 연습에서는 진단 로깅 사용법을 살펴보지 않았습니다. [Cognitive Services 설명서](https://docs.microsoft.com/azure/cognitive-services/diagnostic-logging)에서 진단 로깅에 대해 자세히 알아볼 수 있습니다.
