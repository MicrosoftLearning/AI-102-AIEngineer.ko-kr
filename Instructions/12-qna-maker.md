---
lab:
  title: 질문 답변 솔루션 만들기
  module: Module 6 - Building a QnA Solution
ms.openlocfilehash: 42b3b6e6955188d3d442a4f587ead6afe0f389c6
ms.sourcegitcommit: a879eae298aaf2ab6415fc4fdb67f52f592d907a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2022
ms.locfileid: "146902484"
---
# <a name="create-a-question-answering-solution"></a>질문 답변 솔루션 만들기

가장 흔히 진행되는 대화 시나리오 중 하나는 FAQ(질문과 대답) 기술 자료를 통한 지원 제공입니다. 대다수 조직은 FAQ를 문서나 웹 페이지로 게시합니다. 질문과 대답 쌍의 수가 적은 경우에는 이러한 방식에 문제가 없지만, 문서가 크면 검색이 어려우며 시간이 많이 걸릴 수 있습니다.

**언어** 서비스는 자연어 입력을 사용하여 쿼리할 수 있는 질문과 대답 기술 자료를 만들 수 있는 질문 답변 기능을 포함하며, 사용자가 제출한 질문의 대답을 조회하기 위해 봇이 사용할 수 있는 리소스로 가장 많이 사용됩니다.

> **참고**: 언어 서비스의 질문 답변 기능은 별도의 서비스로 사용할 수 있는 새 버전의 QnA Maker 서비스입니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="create-a-language-resource"></a>Language 리소스 만들기

질문 답변에 대한 기술 자료를 만들고 호스팅하려면 Azure 구독에 **언어 서비스** 리소스가 필요합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고, 언어를 검색하고, **언어 서비스** 리소스를 만듭니다.
3. **사용자 지정 질문 답변** 블록에서 **선택** 을 클릭합니다. 그런 다음, **계속해서 리소스 만들기** 를 클릭합니다. 다음 설정을 입력해야 합니다.
    
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 위치 선택
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S
    - **Azure Search 위치**\*: 언어 리소스와 같은 글로벌 지역에서 위치를 선택합니다.
    - **Azure Search 가격 책정 계층**: 무료(F)(이 계층을 사용할 수 없으면 기본(B) 선택)
    - **약관**: _동의_ 
    - **책임 있는 AI 알림**: _동의_
    
    \*맞춤형 질의응답은 Azure Search를 사용해 질문과 대답 기술 자료를 인덱싱 및 쿼리합니다.

4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.

## <a name="create-a-question-answering-project"></a>질문 답변 프로젝트 만들기

언어 리소스에서 질문 답변에 대한 기술 자료를 만들려면 Language Studio 포털을 사용하여 질문 답변 프로젝트를 만들 수 있습니다. 여기서는 [Microsoft Learn](https://docs.microsoft.com/learn) 관련 질문과 대답이 포함된 기술 자료를 만듭니다.

1. 새 브라우저 탭에서 `https://language.azure.com`의 *Language Studio* 포털로 이동하고 Azure 구독과 연결된 Microsoft 계정을 사용하여 로그인합니다.
2. 언어 리소스를 선택하라는 메시지가 표시되면 다음 설정을 선택합니다.
    - **Azure Directory**: 구독이 포함된 Azure Directory입니다.
    - **Azure 구독**: Azure 구독입니다.
    - **언어 리소스**: 이전에 만든 언어 리소스입니다.
3. 언어 리소스를 선택하라는 메시지가 표시되지 않으면 구독에 여러 언어 리소스가 있기 때문일 수 있습니다. 이 경우 다음을 수행합니다.<u></u>
    1. 페이지의 위쪽에 있는 막대에서 **설정(&#9881;)** 단추를 클릭합니다.
    2. **설정** 페이지에서 **리소스** 탭을 봅니다.
    3. 방금 만든 언어 리소스를 선택하고 **리소스 전환** 을 클릭합니다.
    4. 페이지 맨 위에서 **Language Studio** 를 클릭하여 Language Studio 홈페이지로 돌아갑니다.
3. 포털의 위쪽에 있는 **새로 만들기** 메뉴에서 **사용자 지정 질문 답변** 을 선택합니다.
4. ***프로젝트 만들기** 마법사의 **언어 설정 선택** 페이지에서 이 리소스의 모든 프로젝트에 대한 언어를 설정하는 옵션을 선택하고 언어로 **영어** 를 선택합니다. 그런 다음 **다음** 을 클릭합니다.
5. **기본 정보 입력** 페이지에서 다음 세부 정보를 입력하고 **다음** 을 클릭합니다.
    - **이름** LearnFAQ
    - **설명**: Microsoft Learn에 대한 FAQ
    - **답변이 반환되지 않는 경우의 기본 답변**: 죄송합니다. 질문을 이해하지 못했습니다.
6. 검토 및 완료 페이지에서 **만들기** 를 클릭합니다.

## <a name="add-a-sources-to-the-knowledge-base"></a>기술 자료에 원본 추가

기술 자료를 처음부터 만들어도 되지만 일반적으로는 기존 FAQ 페이지나 문서에서 질문과 대답을 가져오는 작업부터 시작합니다. 이 경우 Microsoft Learn을 위한 기존 FAQ 웹 페이지에서 데이터를 가져오고, 미리 정의된 “잡담” 질문과 대답을 가져와 일반적인 대화형 교환을 지원합니다.

1. 질문 답변 프로젝트의 **원본 관리** 페이지의 **&#9547; 원본 추가** 목록에서 **URL** 을 선택합니다. 그런 다음 **URL 추가** 대화 상자에서 **&#9547; URL 추가** 를 클릭하고 다음 URL을 추가한 다음 **모두 추가** 를 클릭하여 기술 자료에 추가합니다.
    - **이름**: `Learn FAQ Page`
    - **URL**: `https://docs.microsoft.com/en-us/learn/support/faq`
2. 질문 답변 프로젝트의 **원본 관리** 페이지의 **&#9547; 원본 추가** 목록에서 **잡담** 을 선택합니다. **잡담 추가** 대화 상자에서 **친숙한** 을 선택하고 **잡담 추가** 를 클릭합니다.

## <a name="edit-the-knowledge-base"></a>지식 베이스 편집

앞에서 만든 기술 자료에는 Microsoft Learn FAQ의 질문과 대답 쌍이 입력되었으며 대화형 *잡담* 질문과 대답 쌍 세트가 추가되었습니다. 질문과 대답 쌍을 더 추가하여 기술 자료를 확장할 수 있습니다.

1. Language Studio의 **LearnFAQ** 프로젝트에서 **기술 자료 편집** 페이지를 선택하여 기존 질문 및 답변 쌍을 확인합니다(일부 팁이 표시되는 경우 팁을 읽고 **확인** 을 클릭하여 해제하거나, **모두 건너뛰기** 클릭).
2. 기술 자료에서 **&#65291; 질문 쌍 추가** 를 선택합니다.
3. **질문** 상자에 `What is Microsoft certification?`를 입력하고 **Enter****를 누릅니다.
4. **&#65291; 대체 구문 추가** 를 선택하고, `How can I demonstrate my Microsoft technology skills?`를 입력하고, **Enter** 키를 누릅니다.
5. **대답** 상자에 `The Microsoft Certified Professional program enables you to validate and prove your skills with Microsoft technologies.`를 입력합니다. 그런 다음 **Enter** 키를 눌러 질문(대체 구문 포함)과 답변을 기술 자료에 추가합니다.

    사용자가 대답을 확인한 후 추가 작업으로 멀티 턴 대화를 작성하면 효율적인 경우도 있습니다. 그러면 사용자가 질문을 여러 번 구체화하여 필요한 대답을 확인할 수 있습니다.

6. 인증 관련 질문에 입력한 대답 아래에서 **&#65291; 후속 프롬프트 추가** 를 선택합니다.
7. **후속 프롬프트** 대화 상자에서 다음 설정을 입력한 다음 **프롬프트 추가** 를 클릭합니다.
    - **사용자에게 프롬프트에 표시되는 텍스트**: `Learn more about certification`.
    - **새 쌍에 대한 링크 만들기** 를 선택하고 다음 텍스트를 입력합니다. `You can learn more about certification on the [Microsoft certification page](https://docs.microsoft.com/learn/certifications/).`
    - **컨텍스트 흐름에서만 표시**: 선택됨 *이 옵션을 선택하면 원래 인증 관련 질문의 후속 질문 컨텍스트에서만 대답이 반환됩니다.*

## <a name="train-and-test-the-knowledge-base"></a>기술 자료 학습 및 테스트

이제 기술 자료가 있으므로, Language Studio에서 이것을 테스트할 수 있습니다.

1. 페이지의 오른쪽 위에서 **변경 내용 저장** 을 클릭합니다.
2. 변경 내용을 저장한 후 **테스트** 를 클릭하여 테스트 창을 엽니다.
3. 테스트 창의 상단에서 짧은 대답 표시 옵션을 선택 취소합니다. 그런 다음 아래쪽에 `Hello`라는 메시지를 입력합니다. 적절한 응답이 반환되어야 합니다.
4. 테스트 창의 맨 아래에 `What is Microsoft Learn?` 메시지를 입력합니다. FAQ에서 적절한 응답이 반환되어야 합니다.
5. `Thanks!` 메시지를 입력합니다. 적절한 잡담 응답이 반환되어야 합니다.
6. `Tell me about certification` 메시지를 입력합니다. 앞에서 작성한 대답이 반환되고 후속 프롬프트 링크가 표시되어야 합니다.
7. **인증에 대한 자세한 정보** 후속 링크를 선택합니다. 인증 페이지 링크가 포함된 후속 질문의 대답이 반환되어야 합니다.
8. 기술 자료 테스트를 마쳤으면 테스트 창을 닫습니다.

## <a name="deploy-and-test-the-knowledge-base"></a>기술 자료 배포 및 테스트

기술 자료는 클라이언트 애플리케이션이 질문의 대답을 찾는 데 사용할 수 있는 백 엔드 서비스를 제공합니다. 이제 기술 자료를 게시하고 클라이언트에서 기술 자료 REST 인터페이스에 액세스할 수 있습니다.

1. Language Studio의 **LearnFAQ** 프로젝트에서 **기술 자료 배포** 페이지를 선택합니다.
2. 페이지 위쪽에서 **배포** 를 클릭합니다. 그런 다음 **배포** 를 클릭하여 기술 자료를 배포할 것인지 확인합니다.
3. 배포가 완료되면 **예측 URL 가져오기** 를 클릭하여 기술 자료에 대한 REST 엔드포인트를 보고 클립보드에 복사합니다(대화 상자를 아직 닫지 않음).
4. Visual Studio Code의 **12-qna** 폴더에서 **ask-question.cmd** 를 엽니다. 이 스크립트는 *Curl* 을 사용하여 질문 답변 엔드포인트의 REST 인터페이스를 호출합니다.
5. 스크립트에서 *YOUR_PREDICTION_ENDPOINT* 를 복사한 예측 엔드포인트로 바꿉니다(따옴표로 묶여 있어야 함).
6. 브라우저로 돌아가서 **예측 URL 가져오기** 대화 상자에서 샘플 요청에 **Ocp-Apim-Subscription-Key** 매개 변수에 대한 값이 포함되어 있는지 확인합니다. 이것은 *ab12c345de678fg9hijk01lmno2pqrs34* 와 유사합니다. 이것은 리소스에 대한 권한 부여 키입니다. 클립보드에 복사한 다음 **확인** 을 클릭하여 대화 상자를 닫습니다.
7. Visual Studio Code로 돌아가서 **ask-question.cmd** 스크립트에서 *YOUR_KEY* 를 복사한 키로 바꿉니다(따옴표로 묶어야 함).
8. 스크립트의 Curl 명령은 **학습 경로란?** 값으로 **question** 매개 변수를 제출합니다.
9. 전체 스크립트가 다음 코드와 유사한지 확인한 다음 파일을 저장합니다.

    ```
    @echo off
    SETLOCAL ENABLEDELAYEDEXPANSION

    rem Set variables
    set prediction_url="https://some-name.cognitiveservices.azure.com/language/......."
    set key="ab12c345de678fg9hijk01lmno2pqrs34"

    curl -X POST !prediction_url! -H "Ocp-Apim-Subscription-Key: !key!" -H "Content-Type: application/json" -d "{'question': 'What is a learning Path?' }"
    ```

10. Visual Studio Code의 탐색기 창에서 **ask-question.cmd** 스크립트를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기** 를 선택합니다.
11. 터미널 창에서 `ask-question.cmd` 명령을 입력하여 스크립트를 실행하고 서비스에서 반환하는 JSON 응답을 확인합니다. 이 응답은 학습 경로란? 질문에 적절한 답을 포함해야 합니다.

## <a name="create-a-bot-for-the-knowledge-base"></a>기술 자료용 봇 만들기

기술 자료에서 대답을 검색하는 데 사용되는 클라이언트 애플리케이션으로는 봇이 가장 흔히 사용됩니다.

1. 브라우저에서 Language Studio로 돌아가서 **기술 자료 배포** 페이지에서 **봇 만들기** 를 선택합니다. 그러면 새 브라우저 탭에서 Azure Portal이 열리므로 Azure 구독에서 웹앱 봇을 만들 수 있습니다(메시지가 표시되면 로그인함).
2. Azure Portal에서 다음 설정을 사용하여 Web App Bot을 만듭니다(대부분의 경우 이는 사용자를 위해 미리 입력됨).

    누락된 값이 있으면 브라우저를 새로 고치세요.  

  - **봇 핸들**: *봇의 고유한 이름*
  - **구독**: ‘Azure 구독’
  - **리소스 그룹**: 언어 리소스가 포함된 리소스 그룹
  - **위치**: *Text Analytics 서비스와 같은 위치*
  - **가격 책정 계층**: F0
  - **앱 이름**: *고유 ID와 *.azurewebsites.net* 이 자동으로 추가된 **봇 핸들** 과 동일
  - **SDK 언어**: *C# 또는 Node.js 중 하나 선택*
  - **QnA 인증 키**: *QnA 기술 자료에 대한 인증 키로 자동 설정되어야 함*
  - **App Service 계획/위치**: 적절한 계획과 위치가 있는 경우 자동 설정될 수 있습니다. 그렇지 않으면 새로 계획을 만듭니다.
  - **Application Insights**: 해제
  - **Microsoft 앱 ID 및 암호**: 앱 ID 및 암호를 자동으로 만듭니다.
3. 봇이 만들어질 때까지 기다립니다. 그런 다음 **리소스로 이동** 로 클릭합니다(또는 홈페이지에서 **리소스 그룹** 을 클릭하고 웹앱 봇을 만든 리소스 그룹을 열고 클릭합니다.)
4. 봇의 블레이드에서 **웹 채팅에서 테스트** 페이지를 보고, 봇이 **안녕하세요. 환영합니다!** 메시지를 표시할 때까지 기다립니다. (초기화하는 데 몇 초 정도 걸릴 수 있습니다.)
5. 테스트 채팅 인터페이스를 사용하여 봇이 기술 자료에서 예상대로 질문에 답변하는지 확인합니다. 예를 들어, `What is Microsoft certification?` 제출을 시도합니다.

## <a name="more-information"></a>추가 정보

언어 서비스에서 질문 답변에 대해 자세히 알아보려면 [언어 서비스 설명서](https://docs.microsoft.com/en-us/azure/cognitive-services/language-service/question-answering/overview)를 참조하세요.
