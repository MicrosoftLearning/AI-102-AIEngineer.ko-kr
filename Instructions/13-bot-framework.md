---
lab:
  title: Bot Framework SDK를 사용하여 봇 만들기
  module: Module 7 - Conversational AI and the Azure Bot Service
ms.openlocfilehash: 7f2d78bec2ee9fab7bf5fad9ec9ef8ebc76b7fba
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "137819442"
---
# <a name="create-a-bot-with-the-bot-framework-sdk"></a>Bot Framework SDK를 사용하여 봇 만들기

*봇* 은 인간 사용자와의 대화에 참여할 수 있는 소프트웨어 에이전트입니다. Microsoft Bot Framework는 Azure Bot Service를 통해 클라우드 서비스로 제공할 수 있는 봇 빌드용으로 포괄적인 플랫폼을 제공합니다.

이 연습에서는 Microsoft Bot Framework SDK를 사용하여 봇을 만들고 배포합니다.

## <a name="before-you-start"></a>시작하기 전에

먼저 봇 개발을 위한 환경을 준비하겠습니다.

### <a name="update-the-bot-framework-emulator"></a>Bot Framework Emulator 업데이트

Bot Framework SDK를 사용하여 봇을 만들고, Bot Framework Emulator를 사용하여 봇을 테스트합니다. Bot Framework Emulator는 정기적으로 업데이트되므로 최신 버전이 설치되어 있는지 확인해보겠습니다.

> **참고**: 업데이트에는 이 연습의 지침에 영향을 주는 사용자 인터페이스 변경 내용이 포함되어 있을 수 있습니다.

1. **Bot Framework Emulator** 를 시작하고, 업데이트를 설치하라는 메시지가 표시되면 현재 로그인되어 있는 사용자용으로 업데이트를 설치합니다. 자동으로 메시지가 표시되지 않으면 **도움말** 메뉴의 **업데이트 확인** 옵션을 사용하여 메뉴를 확인합니다.
2. 사용 가능한 업데이트를 설치한 후에는 나중에 다시 필요하게 될 때까지 Bot Framework Emulator를 닫습니다.

### <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P 누르기) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="create-a-bot"></a>봇 만들기

Bot Framework SDK를 사용하면 템플릿을 기반으로 봇을 만든 다음 구체적인 요구 사항을 충족하도록 코드를 사용자 지정할 수 있습니다.

> **참고**: 이 연습에서는 **C#** 또는 **Python** 을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **13-bot-framework** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. 선택한 언어의 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다.
3. 터미널에서 다음 명령을 실행하여 필요한 봇 템플릿과 패키지를 설치합니다.

**C#**

```C#
dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
dotnet new -i Microsoft.Bot.Framework.CSharp.CoreBot
dotnet new -i Microsoft.Bot.Framework.CSharp.EmptyBot
```

**Python**

```Python
pip install botbuilder-core
pip install asyncio
pip install aiohttp
pip install cookiecutter==1.7.0
```

4. 템플릿과 패키지를 설치한 후 다음 명령을 실행하여 *EchoBot* 템플릿을 기반으로 봇을 만듭니다.

**C#**

```C#
dotnet new echobot -n TimeBot
```

**Python**

```Python
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

Python을 사용 중인 경우 cookiecutter에서 메시지가 표시되면 다음 세부 정보를 입력합니다.
- **bot_name**: TimeBot
- **bot_description**: A bot for our times
    
5. 터미널 창에서 다음 명령을 입력하여 현재 디렉터리를 **TimeBot** 폴더로 변경한 다음 봇용으로 생성된 코드 파일 목록을 표시합니다.

    ```Code
    cd TimeBot
    dir
    ```

## <a name="test-the-bot-in-the-bot-framework-emulator"></a>Bot Framework Emulator에서 봇 테스트

*EchoBot* 템플릿을 기반으로 봇을 만들었습니다. 이제 Bot Framework Emulator(시스템에 설치되어 있음)를 사용하여 봇을 로컬에서 실행해 테스트할 수 있습니다.

1. 터미널 창에서 현재 디렉터리가 봇 코드 파일이 포함된 **TimeBot** 폴더인지 확인한 후에 다음 명령을 입력하여 로컬에서 봇 실행을 시작합니다.

**C#**

```C#
dotnet run
```

**Python**

```Python
python app.py
```
    
봇이 시작되면 봇이 실행 중인 엔드포인트가 표시되는지 확인합니다. 이는 **http://localhost:3978** 와(과) 유사해야 합니다.

2. Bot Framework Emulator를 시작하고 다음과 같이 **/api/messages** 경로가 추가된 엔드포인트를 지정하여 봇을 엽니다.

    `http://localhost:3978/api/messages`

3. **실시간 채팅** 창에서 대화가 열리면 *Hello and welcome!* 메시지가 표시될 때까지 기다립니다.
4. *Hello* 와 같은 메시지를 입력하고 봇의 응답을 확인합니다. 입력한 메시지가 그대로 다시 표시됩니다.
5. Bot Framework Emulator를 닫고 Visual Studio Code로 돌아온 후 터미널 창에서 **Ctrl+C** 를 입력하여 봇을 중지합니다.

## <a name="modify-the-bot-code"></a>봇 코드 수정

사용자 입력을 그대로 다시 사용자에게 표시하는 봇을 만들었습니다. 이 봇은 별로 유용하지는 않지만 대화의 기본 흐름을 확인할 수는 있습니다. 봇과의 대화는 *활동*,시퀀스로 구성됩니다. 활동에서는 텍스트, 그래픽 또는 사용자 인터페이스 *카드* 를 사용하여 정보를 교환합니다. 봇은 인사말을 사용하여 대화를 시작합니다. 이는 사용자가 봇을 사용하여 채팅 세션을 초기화할 때 트리거되는 대화 업데이트 활동의 결과입니다. 그런 다음 대화는 사용자와 봇이 메시지를 보내기 위해 차례로 수행하는 일련의 추가 활동으로 구성됩니다.

1. Visual Studio Code에서 봇의 다음 코드 파일을 엽니다.
    - **C#** : TimeBot/Bots/EchoBot.cs
    - **Python**: TimeBot/bot.py

    이 파일의 코드는 *활동 처리기* 함수로 구성되어 있습니다. 함수 중 하나는 *구성원이 추가되었습니다.* 대화 업데이트 활동(채팅 세션에 다른 사람이 참가하는 경우)용이고 다른 하나는 *메시지* 활동(메시지가 수신되는 경우)용입니다. 대화는 *턴* 이라는 개념을 기반으로 진행됩니다. 각 턴은 봇이 활동을 수신하여 처리하고 응답을 하는 상호 작용을 나타냅니다. *턴 컨텍스트* 를 사용하여 현재 턴에서 처리 중인 활동 관련 정보를 추적합니다.

2. 코드 파일 맨 위에 다음 네임스페이스 가져오기 문을 추가합니다.

**C#**

```C#
using System;
```

**Python**

```Python
from datetime import datetime
```

3. *메시지* 활동용 활동 처리기 함수를 다음 코드와 일치하도록 수정합니다.

**C#**

```C#
protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    string inputMessage = turnContext.Activity.Text;
    string responseMessage = "Ask me what the time is.";
    if (inputMessage.ToLower().StartsWith("what") && inputMessage.ToLower().Contains("time"))
    {
        var now = DateTime.Now;
        responseMessage = "The time is " + now.Hour.ToString() + ":" + now.Minute.ToString("D2");
    }
    await turnContext.SendActivityAsync(MessageFactory.Text(responseMessage, responseMessage), cancellationToken);
}
```

**Python**

```Python
async def on_message_activity(self, turn_context: TurnContext):
    input_message = turn_context.activity.text
    response_message = 'Ask me what the time is.'
    if (input_message.lower().startswith('what') and 'time' in input_message.lower()):
        now = datetime.now()
        response_message = 'The time is {}:{:02d}.'.format(now.hour,now.minute)
    await turn_context.send_activity(response_message)
```
    
4. 변경 내용을 저장하고 터미널 창에서 현재 디렉터리가 봇 코드 파일이 포함된 **TimeBot** 폴더인지 확인한 후에 다음 명령을 입력하여 로컬에서 봇 실행을 시작합니다.

**C#**

```C#
dotnet run
```

**Python**

```Python
python app.py
```

앞에서와 마찬가지로 봇이 시작되면 봇이 실행 중인 엔드포인트가 표시되는지 확인합니다.

5. Bot Framework Emulator를 시작하고 다음과 같이 **/api/messages** 경로가 추가된 엔드포인트를 지정하여 봇을 엽니다.

    `http://localhost:3978/api/messages`

6. **실시간 채팅** 창에서 대화가 열리면 *Hello and welcome!* 메시지가 표시될 때까지 기다립니다.
7. *Hello* 와 같은 메시지를 입력하고 봇의 응답을 확인합니다. *Ask me what the time is* 가 응답으로 표시됩니다.
8. *What is the time?* 을 입력하고 응답을 확인합니다.

    이제 봇은 “지금 몇 시인가요?” 쿼리에 응답합니다. 봇이 실행되는 현지 시간을 표시합니다. 다른 쿼리를 입력하는 경우에는 시간을 물어보라는 메시지가 사용자에게 표시됩니다. 현재 이 봇은 매우 제한적인 기능만 제공하며, Language Understanding 서비스와 추가 사용자 지정 코드를 통합하면 봇의 기능을 개선할 수 있습니다. 하지만 템플릿에서 만든 봇을 확장하는 방식을 통해 Bot Framework SDK를 사용하여 솔루션을 빌드하는 방법의 실제 예제를 이 봇에서 확인할 수 있습니다.

9. Bot Framework Emulator를 닫고 Visual Studio Code로 돌아온 후 터미널 창에서 **Ctrl+C** 를 입력하여 봇을 중지합니다.

## <a name="more-information"></a>추가 정보

Bot Framework에 대해 자세히 알아보려면 [Bot Framework 설명서](https://docs.microsoft.com/azure/bot-service/index-bf-sdk)를 참조하세요.
