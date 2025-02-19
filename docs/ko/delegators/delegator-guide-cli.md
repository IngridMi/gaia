# 위임자 가이드라인 (CLI)

이 문서는 위임자가 커맨드라인 인터페이스(CLI, Command-Line Interface)를 통해 코스모스 허브와 소통하기 위해 필요한 모든 정보를 포함하고 있습니다.

또한 계정 관리, 코스모스 펀드레이저로 받은 계정을 복구하는 방법, 그리고 렛저 나노 하드웨어 지갑 사용법 또한 포함되어있습니다.

__중요__: 이 문서에 설명되어있는 모든 단계를 신중하게 진행하십시오. 특정 행동의 실수는 소유하고 있는 아톰의 손실을 초래할 수 있습니다. 진행 전 이 문서에 있는 모든 절차를 자세히 확인하시고 필요시 코스모스 팀에게 연락하십시오. **이 문서는 참고용 정보를 제공하기 위해 번역된 영어 원문의 번역본입니다. 이 문서에 포함되어있는 정보의 완결성은 보장되지 않으며, 개인의 행동에 따른 손실을 책임지지 않습니다. 꼭 영어 원문을 참고하시기 바랍니다. 만약 이 문서의 정보와 영어 원문의 정보가 다른 경우, 영어 문서의 정보가 상위 권한을 가지게 됩니다.**

CLI를 사용하는 위임자는 매우 실험적인 블록체인 기술이 사용되고 있는 코스모스 허브를 사용하게됩니다. 코스모스 허브는 우수한 기술을 기반으로 다수의 보안 감사를 진행했으나 문제, 업데이트 그리고 버그가 존재할 수 있습니다. 또한 블록체인 기술을 사용하는 것은 상당한 기술적 배경을 필요로 하며, 공식 팀의 컨트롤 밖에 있는 리스크가 따릅니다. 유저는 이 소프트웨어를 사용함으로써 암호학 기반 소프트웨어를 사용하는 리스크를 인지하고 있음을 인정하는 것입니다. (참고 문서: [인터체인 코스모스 펀드레이저 약관](https://github.com/cosmos/cosmos/blob/master/fundraiser/Interchain%20Cosmos%20Contribution%20Terms%20-%20FINAL.pdf)).

인터체인 재단(Interchain Foundation)과 텐더민트 팀은 소프트웨어 사용으로 발생하는 모든 손실에 대해서 책임을 지지 않습니다. Apache 2.0 라이선스 기반의 오픈소스 소프트웨어를 사용하는 것은 각 개인의 책임이며, 소프트웨어는 그 어떤 보증과 조건이 없는 'As Is(있는 그대로)' 기반으로 제공됩니다.

모든 절차는 신중하게 진행하시기 바랍니다.

## 목차

- [`gaiad` 설치하기](#installing-gaiad)
- [코스모스 계정](#cosmos-accounts)
    + [펀드레이저 계정 복구하기](#restoring-an-account-from-the-fundraiser)
    + [계정 생성하기](#creating-an-account)
- [코스모스 허브 네트워크 액세스하기](#accessing-the-cosmos-hub-network)
    + [자체 풀노드 운영하기](#running-your-own-full-node)
    + [원격 풀노드 연결하기](#connecting-to-a-remote-full-node)
- [`gaiad` 설정하기](#setting-up-gaiad)
- [상태(state) 조회하기](#querying-the-state)
- [트랜잭션 전송하기](#sending-transactions)
    + [가스(Gas)와 수수료 관련 정보](#a-note-on-gas-and-fees)
    + [아톰 위임 및 위임 보상 수령하기](#bonding-atoms-and-withdrawing-rewards)
    + [거버넌스에 참여하기](#participating-in-governance)
    + [오프라인 컴퓨터에서 트랜잭션 서명하기](#signing-transactions-from-an-offline-computer)

## `gaiad` 설치하기

`gaiad`: `gaiad`는 `gaiad` 풀노드와 소통하기 위해 사용되는 명령어 기반 인터페이스입니다. 

::: 경고
**추가적인 행동을 진행하기 전 최신 `gaiad` 클라이언트를 다운로드 하셨는지 확인하십시오**
:::

[**바이너리 설치하기**]

[**소스에서 설치하기**](https://cosmos.network/docs/gaia/installation.html)

::: tip `gaiad`는 터미널 환경에서 사용됩니다. 다음과 같이 터미널을 실행하세요:

- **윈도우**: `시작` > `모든 프로그램` > `악세서리` > `명령 프롬프트`
- **맥OS**: `파인더` > `애플리케이션` > `유틸리티` > `터미널`
- **리눅스**: `Ctrl` + `Alt` + `T`:::

## 코스모스 계정

모든 코스모스 계정에는 12개 또는 24개의 단어로 이루어진 '시드(Seed)'가 할당됩니다. 이 시드 단어(또는 시드 키)를 기반으로 다수의 코스모스 계정을 생성할 수 있습니다 (예를들어: 다수의 프라이빗 키/퍼블릭 키 쌍). 이런 형태의 월렛은 HD(Hierarchical deterministic) 월렛이라고 불립니다 (HD 월렛에 대한 자세한 정보는 [BIP32](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki)를 참고하세요).

```
        계정 0                             계정 1                              계정 2

+------------------+              +------------------+               +------------------+
|                  |              |                  |               |                  |
|       주소 0      |              |       주소 1      |               |       주소 2      |
|        ^         |              |        ^         |               |        ^         |
|        |         |              |        |         |               |        |         |
|        |         |              |        |         |               |        |         |
|        |         |              |        |         |               |        |         |
|        +         |              |        +         |               |        +         |
|     퍼블릭 키 0    |              |     퍼블릭 키 1     |               |     퍼블릭 키 2    |
|        ^         |              |        ^         |               |        ^         |
|        |         |              |        |         |               |        |         |
|        |         |              |        |         |               |        |         |
|        |         |              |        |         |               |        |         |
|        +         |              |        +         |               |        +         |
|    프라이빗 키 0    |              |     프라이빗 키 1   |               |     프라이빗 키 2   |
|        ^         |              |        ^         |               |        ^         |
+------------------+              +------------------+               +------------------+
         |                                 |                                  |
         |                                 |                                  |
         |                                 |                                  |
         +--------------------------------------------------------------------+
                                           |
                                           |
                                 +---------+---------+
                                 |                   |
                                 |   Mnemonic (시드)  |
                                 |                   |
                                 +-------------------+
```

특정 계좌에 보관된 자산은 프라이빗 키에 의해 관리됩니다. 이 프라이빗 키는 시드의 일방적 기능(one-way function)을 통해 생성됩니다. 프라이빗 키를 분실한 경우, 시드 키를 사용하여 프라이빗 키를 다시 복구하는 것이 가능합니다. 하지만 시드 키를 분실한 경우, 모든 프라이빗 키에 대한 사용권을 잃게 됩니다. 누군가 본인의 시드 키를 가진 경우, 해당 키와 연관된 모든 계정의 소유권을 가진 것과 동일합니다.

::: warning
**12 단어 시드키를 분실하거나 그 누구와도 공유하지 마세요. 자금 탈취와 손실을 예방하기 위해서는 다수의 시드키 사본을 만드시고 금고 같이 본인만이 알 수 있는 안전한 곳에 보관하는 것을 추천합니다. 누군가 시드키를 가지게 된 경우, 관련 프라이빗 키와 모든 계정의 소유권을 가지게 됩니다.**
:::

주소는 특정 계정을 구분하는 용도로 사용되며, 단어로 이루어진 특정 프리픽스(예, cosmos10)와 스트링 값을 조합한 값입니다 (예, `cosmos10snjt8dmpr5my0h76xj48ty80uzwhraqalu4eg`). 주소는 누군가 자산을 특정 계정으로 전송할때 사용되며, 퍼블릭키를 사용해 프라이빗 키를 추출하는 것은 불가능합니다.

### 펀드레이저 계정 복구하기

::: tip
*참고: 이 항목은 코스모스 펀드레이저 참가자만을 위한 정보입니다*
:::

코스모스 펀드레이저에 참가한 인원은 12개의 단어로 구성된 시드키를 부여받습니다. 새로 생성된 시드키는 24개 단어로 이루어졌으나, 12개 단어로 이루어진 시드키 또한 모든 코스모스가 제공하는 도구에서 호환됩니다.

#### 렛저(Ledger) 기기 사용하기

모든 렛저 기기에는 (코스모스 허브를 포함한) 다수의 블록체인에서 계정을 생성하기 위해 사용되는 시드키가 있습니다. 통상 시드키는 렛저 기기를 처음 활성화 할때 생성하지만, 유저가 시드키를 직접 입력하는 것 또한 가능합니다. 이제 펀드레이저를 통해 받은 시드키를 어떻게 렛저 하드웨어 지갑에 입력하는지 알아보겠습니다.

::: warning
*참고: 이번 단계를 진행하실때 **신규 기기를 사용하는 것을 권장합니다**. 한 렛저 기기에는 하나의 시드키만을 입력할 수 있습니다. 만약 이미 사용하시던 하드웨어 지갑을 사용하시기를 바라는 경우, `Settings`>`Device`>`Reset All`를 통해 리셋을 진행한 후 펀드레이저 시드를 입력할 수 있습니다. **렛저 기기를 리셋할 경우, 기존에 사용했던 시드키는 기기에서 삭제됩니다. 리셋을 진행하기 전 기존 기기의 시드키를 백업하셨는지 확인하신 후 진행하시기 바랍니다.** 백업 되지 않은 상태로 기기를 리셋하는 경우, 관련 계정의 자산을 잃을 수 있습니다.*
:::


다음 단계는 신규 렛저 기기 또는 초기화 된 렛저 기기에서 진행되어야 합니다: 

1. USB를 사용해 렛저 기기를 컴퓨터에 연결하세요
2. 두개의 버튼을 동시에 누르세요
3. "Restore Configuration"을 선택하세요. **"Config as a new device"를 선택하시면 안됩니다**
4. 원하시는 핀 번호를 입력하세요
5. 12-words 옵션을 선택하세요
6. 코스모스 펀드레이저에서 부여 받은 시드키를 차례대로 정확하게 입력하세요 

이제 렛저 하드웨어 지갑 기기가 펀드레이저 시드로 활성화되었습니다. 기존의 펀드레이저 시드를 파기하지 마십시오! 만약 렛저 기기가 고장나거나 분실된 경우, 동일한 시드키를 이용해 복구가 가능합니다.

이제 [여기](#using-a-ledger-device)를 클릭하여 계정을 생성하는 방법을 확인하세요.

#### 컴퓨터 사용하기

::: warning
**참고: 다음 행동은 오프라인 상태인 컴퓨터에서 진행하는 것이 더욱 안전합니다.**
::: 

컴퓨터를 이용해 펀드레이저 시드키를 복구하시고 컴퓨터에 프라이빗 키를 저장사기 위해서는 다음 명령어를 실행하세요:

```bash
gaiad keys add <키_명칭(YourKeyName)> --recover
```

명령어를 입력하셨다면 프로그램이 지금 생성(복구)하시는 계정의 프라이빗 키를 암호화할때 사용될 비밀번호를 입력할 것을 요청합니다. 해당 계정을 이용해 트랜잭션을 보낼때마다 이 비밀번호를 입력하셔야 합니다. 만약 비밀번호를 잃어버리셨다면 시드키를 사용해 계정을 다시 복구할 수 있습니다.

- `<yourKeyName>` 은 계정의 이름입니다. 이는 시드키로부터 키 페어를 파생할때 레퍼런스로 사용됩니다. 이 이름은 토큰을 전송할때 보내는 계정을 구분하기 위해서 사용됩니다.
- 추가적인 선택 사항으로 명령어에 `--account` 플래그를 추가해 특정 패스(`0`, `1`, `2`, 등)를 지정할 수 있습니다. 기본적으로 `0`을 사용하여 계정이 생성됩니다.

### 계정 생성하기

새로운 계정을 생성하기 위해서는 `gaiad`를 설치해야합니다. 신규 계정을 생성하기 전, 프라이빗 키를 어디에 저장하고 어떻게 불러올지 미리 인지를 하셔야 합니다. 프라이빗 키를 보관하기 가장 좋은 곳은 오프라인 컴퓨터 또는 렛저 하드웨어 월렛 기기입니다. 흔히 사용되는 온라인 컴퓨터에 프라이빗 키를 보관하게 될 경우, 인터넷을 통해 컴퓨터를 침투한 공격자가 프라이빗 키를 탈취할 수 있기 때문에 상당한 리스크가 존재합니다.

#### 렛저(Ledger) 하드웨어 월렛 기기 사용하기

::: warning
**새로 주문한 렛저 기기 또는 신뢰할 수 있는 렛저 기기만을 사용하세요**
:::

렛저 기기를 처음 활성화할때 24개 단어로 구성된 시드키가 생성되고 기기에 저장됩니다. 렛저 기기의 시드키는 코스모스와 코스모스 계정과 호환이 되며, 해당 시드키를 기반으로 계정을 생성할 수 있습니다. 렛저 기기는 `gaiad`와 호환될 수 있게 설정이 되어야 합니다. 렛저 기기를 설정하는 방법은 다음과 같습니다:

1. [Ledger Live 앱](https://www.ledger.com/pages/ledger-live) 다운로드
2. 렛저 기기를 USB로 연결한 후 최신 펌웨어 버전으로 업데이트
3. Ledger Live 앱스토어로 이동한 후, "Cosmos" 애플리케이션 다운로드. (이 단계는 다소 시간이 걸릴 수 있습니다)
4. 렛저 기기에서 코스모스 앱 선택

계정을 생성하기 위해서는 다음 명령어를 실행하십시오:

```bash
gaiad keys add <키_명칭(yourKeyName)> --ledger 
```

- `<yourKeyName>` 은 계정의 이름입니다. 이는 시드키로부터 키 페어를 파생할때 레퍼런스로 사용됩니다. 이 이름은 토큰을 전송할때 보내는 계정을 구분하기 위해서 사용됩니다.
- 추가적인 선택 사항으로 명령어에 `--account` 플래그를 추가해 특정 패스(`0`, `1`, `2`, 등)를 지정할 수 있습니다. 기본적으로 `0`을 사용하여 계정이 생성됩니다. 

#### 컴퓨터 사용하기

::: warning
**참고: 다음 행동은 오프라인 상태인 컴퓨터에서 진행하는 것이 더욱 안전합니다.**
:::

계정을 생성하기 위해서는 다음 명령어를 입력하세요:

```bash
gaiad keys add <키_명칭(yourKeyName)>
```

위 명령어는 새로운 24단어로 구성된 시드키를 생성하고, 계정 `0`의 프라이빗 키와 퍼블릭 키를 저장합니다. 이후, 디스크에 저장될 계정 `0`의 프라이빗 키를 암호화할때 사용될 비밀번호를 입력할 것을 요청합니다. 해당 계정을 이용해 트랜잭션을 보낼때마다 이 비밀번호를 입력하셔야 합니다. 만약 비밀번호를 잃어버리셨다면 시드키를 사용해 계정을 다시 복구할 수 있습니다.

::: danger
**경고: 12 단어 시드키를 분실하거나 그 누구와도 공유하지 마세요. 자금 탈취와 손실을 예방하기 위해서는 다수의 시드키 사본을 만드시고 금고 같이 본인만이 알 수 있는 안전한 곳에 보관하는 것을 추천합니다. 누군가 시드키를 가지게 된 경우, 관련 프라이빗 키와 모든 계정의 소유권을 가지게 됩니다.**
::: 

::: warning
시드키를 안전하게 보관하셨다면 (두번 세번씩이라도 정확하게 작성되었는지 확인하셔야 합니다!) 커맨드 라인의 기록을 다음과 같이 삭제하시면 됩니다:

```bash
history -c
rm ~/.bash_history
```
:::

- `<yourKeyName>` 은 계정의 이름입니다. 이는 시드키로부터 키 페어를 파생할때 레퍼런스로 사용됩니다. 이 이름은 토큰을 전송할때 보내는 계정을 구분하기 위해서 사용됩니다.
- 추가적인 선택 사항으로 명령어에 `--account` 플래그를 추가해 특정 패스(`0`, `1`, `2`, 등)를 지정할 수 있습니다. 기본적으로 `0`을 사용하여 계정이 생성됩니다. 


동일한 시드키로 추가적인 계정을 생성하기 원한다면, 다음 명령어를 사용하세요:

```bash
gaiad keys add <키_명칭(yourKeyName)> --recover --account 1
```

해당 명령어는 비밀번호와 시드키를 입력할 것을 요청할 것입니다. 이 외에 추가적인 계정을 생성하시기 원한다면 account 플래그의 번호를 바꾸십시오.


## 코스모스 허브 네트워크 사용하기

블록체인의 상태(state)를 확인하거나 트랜잭션을 전송하기 위해서는 직접 풀노드를 운영하거나 다른 사람이 운영하는 풀노드에 연결할 수 있습니다.

::: danger
**경고: 12개 단어 / 24개 단어 시드키를 그 누구와도 공유하지 마세요. 시드키는 본인만이 알고있어야 합니다. 특히 이메일, 메시지 등의 수단으로 블록체인 서비스 지원을 사칭해 시드키를 요청할 수 있으니 주의를 바랍니다. 코스모스 팀, 텐더민트 팀 그리고 인터체인 재단은 절대로 이메일을 통해 개인 정보 또는 시드키를 요청하지 않습니다.**.
::: 

### 직접 풀노드 운영하기

이 방법이 가장 안전한 방법이지만, 대량의 리소스를 필요로 합니다. 풀노드를 직접 운영하기 위해서는 우수한 인터넷 대역폭과 최소 1TB 상당의 하드디스크 용량을 필요로 합니다.

[풀노드를 운영하는 절차](https://cosmos.network/docs/gaia/join-mainnet.html)와 [`gaiad`를 설치하는 방법](https://cosmos.network/docs/gaia/installation.html)은 첨부된 링크를 확인하세요.

### 원격 풀노드 연결하기

만약 본인이 직접 풀노드를 운영하는 것을 원하지 않는다면 다른 사람의 풀노드에 연결을 할 수 있습니다. 이 과정에서는 신뢰할 수 있는 풀노드 운영자에만 연결하세요. 악의적인 풀노드 운영자는 트랜잭션을 막거나 틀린 정보를 전달할 가능성이 있습니다. 하지만 프라이빗 키는 당신의 컴퓨터/렛저 기기에 저장되어 있기 때문에 풀노드 운영자는 절대로 자금을 탈취할 수 없습니다. 검증된 검증인, 월렛 제공자, 거래소 등의 풀노드에만 연결하는 것을 추천드립니다.

풀노드에 연결하기 위해서는 다음과 같은 형식의 주소가 필요합니다: `https://77.87.106.33:26657` (*이는 예시를 위한 주소이며 실제 풀노드 주소가 아닙니다*). 이 계정은 신뢰할 수 있는 풀노드 운영자에게서 직접 받으시기 바랍니다. 이 주소는 [다음 항목](#setting-up-gaiad)에서 사용됩니다.

## `gaiad` 설정하기

::: warning
**`gaiad`의 최신 스테이블 버전을 사용하고 있는지 확인해주세요**
:::

`gaiad`는 코스모스 허브 네트워크에서 운영되고 있는 노드와 소통할 수 있게 하는 도구입니다. 풀노드는 본인이 직접 운영하거나, 타인이 운영하는 풀노드를 사용할 수 있습니다. 이제 `gaiad`의 설정을 진행하겠습니다.

`gaiad`을 설정하기 위해서는 다음 명령어를 실행하세요:

```bash
gaiad config <플래그(flag)> <값(value)>
```

해당 명령어는 각 플래그에 대한 값을 설정할 수 있게 합니다. 우선 연결하고 싶은 풀노드의 주소를 입력하겠습니다:


```bash
gaiad config node <호스트(host)>:<포트(port)>

// 예시: gaiad config node https://77.87.106.33:26657
```

만약 풀노드를 직접 운영하시는 경우, `tcp://localhost:26657`을 주소 값으로 입력하세요.

이제 `--trust-node` 플래그의 값을 설정하겠습니다:

```bash
gaiad config trust-node false

// 만약 라이트 클라이언트 노드를 운영하고 싶으신 경우 `true` 값을 입력하세요. 그렇지 않은 경우 `false`를 입력하세요
```

마지막으로 소통하고 싶은 블록체인의 `chain-id`를 입력하겠습니다:

```bash
gaiad config chain-id gos-3
```

## 블록체인 상태 조회하기

[`gaiad`](https://cosmos.network/docs/gaia/gaiad.html)는 계정 잔고, 스테이킹 중인 토큰 수량, 지급 가능한 보상, 거버넌스 프로포절 등 블록체인과 관련된 모든 정보를 확인할 수 있게 합니다. 다음은 위임자에게 유용한 명령어들입니다. 다음 명령어를 실행하기 전 [gaiad 설정](#setting-up-gaiad)을 진행하세요.

```bash
// 계정 잔고와 계정 관련 정보 조회
gaiad query account

// 검증인 목록 조회
gaiad query validators

// 검증인 주소로 (예시: cosmos10snjt8dmpr5my0h76xj48ty80uzwhraqalu4eg) 검증인 정보 조회
gaiad query validator <검증인_주소(validatorAddress)>

// 위임자 주소로 (예시: cosmos10snjt8dmpr5my0h76xj48ty80uzwhraqalu4eg) 계정의 모든 위임 기록 조회
gaiad query delegations <위임자_주소(delegatorAddress)>

// 위임자가 특정 검증인에게 위임한 기록 조회
gaiad query delegations <위임자_주소(delegatorAddress)> <검증인_주소(validatorAddress)>

// 위임자 주소로 (예시: cosmos10snjt8dmpr5my0h76xj48ty80uzwhraqalu4eg) 위임자 리워드 조회
gaiad query distribution rewards <위임자_주소(delegatorAddress)> 

// 보증금(deposit)을 대기중인 모든 프로포절 조회
gaiad query proposals --status deposit_period

// 투표가 가능한 모든 프로포절 조회
gaiad query proposals --status voting_period

// 특정 프로포절 ID로 프로포절 정보 조회
gaiad query proposal <proposalID>
```

더 많은 명령어를 확인하기 위해서는 다음 명령어를 실행하세요:

```bash
gaiad query
```

각 명령어에는 `-h` 또는 `--help`를 추가하여 관련 정보를 확인하실 수 있습니다.

## 트랜잭션 전송하기

::: warning
코스모스 메인넷에서는 `uatom` 단위가 표준 단위로 사용됩니다. `1atom = 1,000,000uatom`으로 환산됩니다.
:::

### 가스와 수수료에 대해서

코스모스 허브 네트워크는 트랜잭션 처리를 위해 트랜잭션 수수료를 부과합니다. 해당 수수료는 트랜잭션을 실행하기 위한 가스로 사용됩니다. 공식은 다음과 같습니다:


```
수수료(Fee) = 가스(Gas) * 가스 값(GasPrices)
```

위 공식에서 `gas`는 전송하는 트랜잭션에 따라 다릅니다. 다른 형태의 트랜잭션은 각자 다른 `gas`량을 필요로 합니다. `gas` 수량은 트랜잭션이 실행될때 계산됨으로 사전에 정확한 값을 확인할 수 있는 방법은 없습니다. 다만, `gas` 플래그의 값을 `auto`로 설정함으로 예상 값을 추출할 수는 있습니다. 예상 값을 수정하기 위해서는 `--gas-adjustment` (기본 값 `1.0`) 플래그 값을 변경하셔서 트랜잭션이 충분한 가스를 확보할 수 있도록 하십시오.

`gasPrice`는 각 `gas` 유닛의 가격입니다. 각 검증인은 직접 최소 가스 가격인 `min-gas-price`를 설정하며, 트랜잭션의 `gasPrice`가 설정한 `min-gas-price`보다 높을때 트랜잭션을 처리합니다.

트랜잭션 피(`fees`)는 `gas` 수량과 `gasPrice`를 곱한 값입니다. 유저는 3개의 값 중 2개의 값을 입력하게 됩니다. `gasPrice`가 높을수록 트랜잭션이 블록에 포함될 확률이 높아집니다.

::: tip
메인넷 권장 `gas-prices`는 `0.0025uatom` 입니다.
:::

### 토큰 전송하기

::: tip **아톰을 위임하거나 위임 보상을 수령하기 전에 `gaiad`를 설치하시고 계정을 만드셔야 합니다**:::

:::warning 참고: 다음 명령어는 온라인 상태인 컴퓨터에서 실행되어야 합니다. 해당 명령은 렛저 하드웨어 월렛 기기를 사용해 실행하는 것을 추천드립니다. 오프라인으로 트랜잭션을 발생하는 방법을 확인하기 위해서는 [여기](#signing-transactions-from-an-offline-computer)를 참고하세요

```bash
//특정 수량의 토큰을 지정한 주소로 전송하기
//파라미터 값 예시(실제 토큰 전송시 사용하지 마세요!): <수신자_주소_예시>=cosmos16m93fezfiezhvnjajzrfyszml8qm92a0w67ntjhd3d0 <수량_예시>=1000000uatom
//플래그 값 예시: <가스_가격(gasPrice)>=0.0025uatom

gaiad tx bank send <수신자_주소> <보내는_수량> --from <키_이름> --gas auto --gas-adjustment 1.5 --gas-prices <가스_가격(gasPrice)>
```

### 아톰 위임하기 / 리워드 수령하기


::: tip **아톰을 위임하거나 위임 보상을 수령하기 전에 `gaiad`를 설치하시고 계정을 만드셔야 합니다**:::

::: warning
**아톰을 위임하기 전에 [위임자 faq](https://cosmos.network/resources/delegators)를 먼저 확인하시고 위임에 따르는 책임과 위험을 사전에 인지하시기 바랍니다**
:::

::: warning
**참고: 다음 명령어는 온라인 상태인 컴퓨터에서 실행되어야 합니다. 해당 명령은 렛저 하드웨어 월렛 기기를 사용해 실행하는 것을 추천드립니다. 오프라인으로 트랜잭션을 발생하는 방법을 확인하기 위해서는 [여기](#signing-transactions-from-an-offline-computer)를 참고하세요.**
::: 

```bash
// 특정 검증인에게 아톰 위임하기 
// 플래그 값 예시: <검증인_주소(validatorAddress)>= cosmosvaloper18thamkhnj9wz8pa4nhnp9rldprgant57pk2m8s, <위임할_수량(amountToBound)>=10000000uatom, <가스_가격(gasPrice)>=0.0025uatom

gaiad tx staking delegate <검증인_주소(validatorAddress> <위임할_수량(amountToBond)> --from <위임자_키_명칭(delegatorKeyName)> --gas auto --gas-adjustment 1.5 --gas-prices <가스_가격(gasPrice)>

// 위임된 아톰을 다른 검증인에게 재위임 하기
// 이미 검증인에게 위임이 된 상태에서만 사용하실 수 있습니다
// 재위임은 즉시 반영됩니다. 재위임 대기 기간은 없습니다
// 재위임을 진행하신 후, 동일한 아톰에 대한 재위임은 3주 후 가능합니다.
// 플래그 값 예시: <기존_검증인_주소(srcValidatorAddress)>=cosmosvaloper18thamkhnj9wz8pa4nhnp9rldprgant57pk2m8s, <재위임할_수량>=100000000uatom, <가스_가격(gasPrice)>=0.0025uatom

gaiad tx staking redelegate <기존_검증인_주소(srcValidatorAddress)> <이동할_검증인_주소(destValidatorAddress)> <재위임할_수량(amountToRedelegate)> --from <위임자_키_명칭(delegatorKeyName)> --gas auto --gas-adjustment 1.5 --gas-prices <가스_가격(gasPrice)>

// 모든 리워드 수령하기
// 플래그 값 예시: <가스_가격(gasPrice)>=0.0025uatom

gaiad tx distribution withdraw-all-rewards --from <위임자_키_명칭(delegatorKeyName)> --gas auto --gas-adjustment 1.5 --gas-prices <가스_가격(gasPrice)>

// 특정 검증인으로 부터 위임 취소하기
// 위임 취소가 완료되기 위해서는 3주의 기간이 걸리며, 위임 취소가 진행중인 기간에는 해당 아톰을 전송하실 수 없습니다.
// 플래그 값 예시: <검증인_주소(validatorAddress)>=cosmosvaloper18thamkhnj9wz8pa4nhnp9rldprgant57pk2m8s, <위임_취소할_수량(amountToUnbond)>=10000000uatom, <가스_가격(gasPrice)>=0.0025uatom

gaiad tx staking unbond <검증인_주소(validatorAddress)> <위임_취소할_수량(amountToUnbond)> --from <위임자_키_명칭(delegatorKeyName)> --gas auto --gas-adjustment 1.5 --gas-prices <가스_가격(gasPrice)>

::: tip
렛저 기기를 사용해 트랜잭션을 발생하시는 경우, 렛저 기기에서 트랜잭션을 확인하는 과정이 추가적으로 발생됩니다. 컴퓨터에 연결되어 있는 기기에서 트랜잭션을 서명하셔야 네트워크로 전파됩니다.
::: 

해당 트랜잭션이 성공적으로 진행된 것을 확인하기 위해서는 다음 조회 명령어를 사용하세요:

```bash
// 아톰을 위임하거나 리워드를 수령하신 후 계정 잔고가 달라집니다 (계정 잔고 확인 명령어)
gaiad query account

// 위임을 진행하셨다면 스테이킹 잔고가 표시됩니다 (스테이킹 확인 명령어)
gaiad query delegations <위임자_주소(delegatorAddress)>

// 트랜잭션이 블록체인에 포함되었으면 해당 tx 정보를 전달합니다
// 트랜잭션을 생성하셨을때 표시되었던 tx hash를 입력하세요 (트랜잭션 확인 명령어)
gaiad query tx <트랜잭션_해시(txHash)>

```

만약 원격 풀노드를 사용해 트랜잭션을 전송하신 경우, 블록 익스플로러를 통해 트랜잭션을 확인하십시오.

## 거버넌스 참가하기

### 거버넌스에 대해서

코스모스 허브는 아톰을 스테이킹 한 위임자들이 투표를 할 수 있는 시스템이 내장되어있습니다. 프로포절의 종류는 3개가 있으며 다음과 같습니다:

- `텍스트 프로포절(Text Proposals)`: 가장 기본적인 형태의 프로포절입니다. 특정 주제에 대한 네트워크의 의견을 확인하기 위해서 사용됩니다.
- `파라미터 프로포절(Parameter Proposals)`: 네트워크의 기존 파라미터 값을 변경하는 것을 제안하기 위해서 사용됩니다.
- `소프트웨어 업그레이드 프로포절(Software Upgrade Proposal)`: 코스모스 허브의 소프트웨어를 업그레이드 하는 것을 제안하기 위해서 사용됩니다.

모든 아톰 보유자는 프로포절을 제안할 수 있습니다. 특정 프로포절의 투표가 활성화되기 위해서는 `minDeposit`값에 정의된 보증금 보다 높은 `deposit` 비용이 예치되어야 합니다. `deposit`은 프로포절 제안자 외에도 보증금을 추가할 수 있습니다. 만약 제안자가 필요한 보증금 보다 낮은 보증금을 입금한 경우, 프로포절은 `deposit_period` 상태로 들어가 추가 보증금 입금을 대기합니다. 모든 아톰 보유자는 `depositTx` 트랜잭션을 통해 보증금을 추가할 수 있습니다.

프로포절의 `deposit`이 `minDeposit`을 도달하게 되면 해당 프로포절의 2주 간의 `voting_period`(투표 기간)이 시작됩니다. **위임된 아톰**의 보유자는 해당 프로포절에 투표를 행사할 수 있으며, `Yes`, `No`, `NoWithVeto` 또는 `Abstain` 표를 선택할 수 있습니다. 각 표는 투표자의 위임된 아톰 수량을 반영하게 됩니다. 만약 위임자가 직접 투표를 진행하지 않은 경우, 위임자는 검증인의 표를 따르게 됩니다. 하지만 모든 위임자는 직접 투표를 행사하여 검증인의 표와 다른 표를 행사할 수 있습니다.

투표 기간이 끝난 후, 프로포절이 50% 이상의 `Yes`표를 받았고 (`Abstain` 표를 제외하고) `NoWithVeto` (`Abstain` 표를 제외하고) 표가 33.33% 이하일 경우 통과하게 됩니다.

### 거버넌스 참여하기

::: warning
**참고: 다음 명령어는 온라인 상태인 컴퓨터에서만 진행이 가능합니다. 해당 명령은 렛저 하드웨어 월렛 기기를 사용해 실행하는 것을 추천드립니다. 오프라인으로 트랜잭션을 발생하는 방법을 확인하기 위해서는 [여기](#signing-transactions-from-an-offline-computer)를 참고하세요.**
::: 

```bash
// 프로포절 제안하기
// <프로포절_종류(type)>=text/parameter_change/software_upgrade
// 플래그 값 예시: <가스_가격(gasPrice)>=0.0025uatom

gaiad tx gov submit-proposal --title "Test Proposal" --description "My awesome proposal" --type <프로포절_종류(type)> --deposit=10000000uatom --gas auto --gas-prices <가스_가격(gasPrice)> --from <위임자_키_명칭(delegatorKeyName)>

// 프로포절의 보증금 추가하기
// 프로포절의 proposalID 조회: $gaiad query gov proposals --status deposit_period
// 파라미터 값 예시: <보증금(deposit)>=10000000uatom

gaiad tx gov deposit <프로포절_ID(proposalID)> <추가할_보증금(deposit)> --gas auto --gas-prices <가스_가격(gasPrice)> --from <위임자_키_명칭(delegatorKeyName)>

// 프로포절에 투표하기
// 프로포절의 proposalID 조회: $gaiad query gov proposals --status voting_period 
// <표_선택(option)>=yes/no/no_with_veto/abstain

gaiad tx gov vote <프로포절_ID(proposalID)> <표_선택(option)> --gas auto --gas-prices <가스_가격(gasPrice)> --from <위임자_키_명칭(delegatorKeyName)>
```

## 오프라인 컴퓨터에서 트랜잭션 서명하기

렛저 기기가 없거나 오프라인 컴퓨터에서 프라이빗 키를 관리하고 싶으신 경우, 다음 절차를 따라하세요. 우선 **온라인** 컴퓨터에서 미서명 트랜잭션을 다음과 같이 생성하십시오 (다음 예시에서는 위임 트랜잭션을 예시로 사용합니다):

```bash
// 아톰 본딩하기 
// 플래그 값 예시: <본딩할 수량(amountToBond)>=10000000uatom, <위임할 검증인의 bech32 주소(bech32AddressOfValidator)>=cosmosvaloper18thamkhnj9wz8pa4nhnp9rldprgant57pk2m8s, <가스 가격(gasPrice)>=0.0025uatom

gaiad tx staking delegate <검증인_주소(validatorAddress)> <위임할_수량(amountToBond)> --from <위임자_주소(delegatorAddress)> --gas auto --gas-adjustment 1.5 --gas-prices <가스_가격(gasPrice)> --generate-only > unsignedTX.json
```

서명을 진행하기 위해서는 `chain-id`, `account-number`, 그리고 `sequence` 값이 필요합니다. `chain-id`는 트랜잭션을 전송할 블록체인의 고유 식별 번호입니다. `account-number`는 계정이 처음 자산을 받을 때 생성되는 고유 번호입니다. `sequence`는 리플레이 공격을 방지하기 위해 전송한 트랜잭션의 수량을 기록하는데 사용됩니다.

체인 아이디(chain-id) 값은 해당 블록체인의 제네시스 파일에서 받으실 수 있습니다 (현재 기준 코스모스 허브는 `cosmoshub-2`). account-number와 sequence는 계정 조회 명령어를 사용해 확인하실 수 있습니다.

```bash
gaiad query account <계정_주소(yourAddress)> --chain-id cosmoshub-2
```

이후 서명이 진행되지 않은 `unsignedTx.json` 파일을 복사하신 후 (USB 등을 이용하여) 오프라인 컴퓨터로 이동하십시오. 만약 오프라인 컴퓨터에 아직 계정을 생성하지 않으셨을 경우, [이 항목](#using-a-computer)을 참고하여 오프라인 컴퓨터에서 계정을 생성하세요. 안전을 위해서 서명하기 전에 다음 명령어를 실행해 트랜잭션의 파라미터를 한번 더 확인하십시오:

```bash
cat unsignedTx.json
```

이제 다음 명령어를 실행해 트랜잭션을 서명합니다:

```bash
gaiad tx sign unsignedTx.json --from <위임자_키_명칭(delegatorKeyName)> --offline --chain-id cosmoshub-2 --sequence <시퀀스(sequence)> --account-number <계정_번호(accountNumber)> > signedTx.json
```

서명된 `signedTx.json` 파일을 복사하시고 다시 온라인 컴퓨터로 이동하세요. 다음 명령어를 실행해 해당 트랜잭션을 네트워크에 전파하세요:

```bash
gaiad tx broadcast signedTx.json
```
