원문 출처 : https://medium.com/@rpicapstone1/how-to-write-a-pubg-app-using-the-free-pubg-api-4d625729ebac

번역 : juneilsam

# PUBG API란?

무료로 제공되고 있는 PUBG API를 사용하면 PC(Steam, Kakao), Xbox 및 PS4의 PUBG 매치 데이터로 앱과 웹사이트를 만들 수 있다(모바일 데이터는 현재 PUBG API를 통해 사용할 수 없다).

---

# TLDR 주요 기능 + PUBG API 고려사항

1. PUBG API의 주요 목적은 엄청난 양의 PUBG 데이터로 앱을 구축할 수 있도록 하는 것이다.

2. PUBG API에 액세스하려면 고유 API 키가 필요하다. 분당 10 개의 기본 "요청"제한이 있다. 요청은 기본적으로 일부 데이터에 대해 PUBG API를 요청하는 것을 의미한다.

3. 과거 14 일 동안의 경기/플레이어 데이터가 보존된다.

4. 개발자 커뮤니티가 있습니다. 공식 PUBG 개발자 포럼 또는 공식 PUBG 개발자 디스코드 채널이다.

---

# 시작하기

- 다음 단계에 따르고 필요에 따라 추가 설명을 위해 API 문서를 참조.

1. 먼저 API 키 (무료)가 필요하다. 홈페이지에서 "Get your own API key"를 클릭하여 PUBG 개발팀에 액세스 권한을 요청해야한다.

2. 분당 10개가 넘는 요청은 PUBG 개발팀에 특별 액세스를 요청하고, 구체적인 이유를 제공해야한다.

3. 다음으로 몇 가지 기본 API 문서를 검토하고 다양한 데이터 속성 및 설명에 익숙해져야 한다. 관심있는 통계의 종류와 API를 사용하여 가져오려는 통계 유형을 이해해야 한다.

4. API 문서 페이지에는 다양한 유형의 통계에 대한 기본 API 기능을 보여주는 대화형 도구가 있다 .

5. 이제 Github에서 호스팅되는 PUBG repositories를 검토한다.

6. PUBG repositories에는 공식 PUBG 그래픽 (로고, 무기 사진)과 같은 유용한 리소스와 PUBG API 항목 및 무기, 이벤트를 글로 번역하는 데이터 사전이 포함되어 있다.

   예 : "Item_Head_G_01_Lv3_C"= "Spetsnaz 헬멧 (레벨 3)"

7. API에 대한 질문이 있거나 도움이 필요한 경우, 포럼으로 이동하거나 공식 PUBG Developer API Discord 서버에 가입할 수 있다.


## 전제 조건

Python은 웹 기반 애플리케이션을 빌드하는 데 사용할 수 있는 범용 프로그래밍 언어입니다(앱을 온라인에 게시하거나 다른 사용자가 사용하려는 경우 웹 기반이 될 것입니다).

다음으로 Python IDE 또는 편집기를 사용하여 코드를 컴파일하고 실행해야합니다. 여기서는 앱이 웹 기반 이길 원한다고 가정하므로 사용하기에 가장 좋은 프레임 워크는 Django입니다. Django는 웹 기반 애플리케이션에 특히 유용한 무료 오픈소스 Python 프레임워크입니다.

---

# PUBG API 기초

- 직접 앱을 빌드하기 전에 익숙해져야 할 매우 중요한 개념과 용어가 몇 가지 있다. API 문서의 모든 기술을 모두 이해하지 못할 수도 있지만 다음 내용이 도움이 될 것이다.


## 샤드

- 모든 PUBG 데이터를 보관하는 하나의 대규모 데이터베이스는 특정 데이터에 액세스하기에는 너무 복잡하다. PUBG 사람들은 이 데이터베이스를 더 작은 샤드로 분할하여 원하는 데이터를 쉽게 얻을 수 있도록 했다.


- 먼저 데이터 세트를 구성하는 다양한 샤드를 이해해야한다. 샤드는 기본적으로 데이터 처리 속도를 높이기 위해 단일의 대형 데이터베이스에 만들어진 파티션이다. 현재 PUBG API는 특정 플랫폼에 의해 분할 또는 "샤드"된다. 다음은 PUBG API가 플레이어 및 매치 엔드 포인트의 데이터를 분할하는 형식의 예이다(엔드 포인트에 대한 설명).

## 플레이어 + 경기 데이터

형식 : { platform_shard — platform_name}

- psn — PS4

- steam — Steam

- 토너먼트 — 토너먼트

- xbox — Xbox
(각 플랫폼은 게임 전반에 걸쳐 다양한 서버 지역으로 더 분리됩니다.)

형식 : { platform-region ( shard ) — Region_name }

- pc-as — 아시아

- pc-eu — 유럽

- pc-jp — 일본

- pc-kakao — 카카오

- pc-krjp — 한국

- pc-na — 북미

- pc-oc — 오세아니아

- pc-ru — 러시아

- pc-sa — 중남미

- pc-sea — 동남아시아

- pc-tournament — 토너먼트

- psn-as — 아시아

- psn-eu — 유럽

- psn-na — 북미

- psn-oc — 오세아니아

- xbox-as — 아시아

- xbox-eu — 유럽

- xbox-na — 북미

- xbox-oc — 오세아니아

- xbox-sa — 남아메리카

## 엔드 포인트

엔드 포인트는 기본적으로 원하는 데이터의 범위이다. 이 API에 대해 존재하는 엔드 포인트는 아래에 정리되어 있고, PUBG API 개발자 포털에 설명되어 있다.

각 엔드 포인트 유형의 URL 형식을 만드는 것이 중요하다. URL은 PUBG API에서 검색할 데이터의 종류와 표시 방식을 정하는 방법이다. 다른 유형의 PUBG API 데이터에 액세스하려면 다음에 설명 된 "URL 형식"의 주요 부분을 편집해야 한다.

아래에서는 다양한 엔드 포인트에 대해 설명하고 각 엔드 포인트 유형에 대한 URL 형식도 포함한다.

엔드 포인트 형식의 예 :

URL 형식 : https://api.pubg.com/pubg.com/shards/[INSERT SHARD NAME] / [INSERT ENDPOINT]

### 엔드 포인트 유형

1. 플레이어 통계(Players, [선수 통계 바로가기](https://documentation.pubg.com/en/players-endpoint.html))

이 엔드 포인트는 검색된 플레이어의 가장 최근 경기 (14일 간)에 대한 통계를 제공한다. 아래와 같이 플레이어 이름과 계정 ID로 플레이어 데이터를 추출 할 수 있다.

샤드 이름은 플랫폼 및 지역코드(위에서 설명한대로)이다.

- 단일 플레이어의 최근 경기를 검색하려면 다음 URL을 사용한다.

URL 공식 : https://api.pubg.com/shards/steam/players?filter[playerNames]=PlayerName1

- 또한 쉼표로 구분하여 여러 플레이어 이름을 입력하여 여러 플레이어 정보를 검색 할 수 있다. 예를 들면 :

URL 공식 : https://api.pubg.com/shards/steam/players?filter[playerNames]=PlayerName1,PlayerName2

2. 시즌 통계(Season Stats, [시즌 통계 바로가기](https://documentation.pubg.com/en/seasons-endpoint.html))

시즌 통계는 계산 된 URL을 사용하여 플랫폼 또는 서버를 기반으로 검색 할 수 있다. 계정 ID와 시즌 ID가있는 특정 플레이어의 시즌 데이터이다.

URL 형식 : https://api.pubg.com/shards/steam / players / {accountId} / seasons / {SEASONID}

    * 참고
    - PC 및 PS4의 경우 : 2018년 9월 이전 및 2018년 9월을 포함한 시즌의 시즌 통계에 액세스하려면 'platform-region'샤드를 사용한다.
                        2018년 9월 이후 시즌의 시즌 통계에 액세스하려면 'platform'샤드를 사용한다.
    - Xbox의 경우 : 2018년 8월 이전 및 2018년 8월을 포함한 시즌의 시즌 통계에 액세스하려면 'platform-region'샤드를 사용한다.
                    2018년 8월 이후 시즌의 시즌 통계에 액세스하려면 'platform'샤드를 사용한다.
   
여기에 있는 변수에는 입힌 피해, 처치 및 치료와 같은 기본 정보가 포함된다. 또한 가장 최근의 주간 킬 및 승리와 같은 일부 집계 변수도 여기에서 검색할 수 있다.

3. 원격 측정 통계(Telemetry, [원격 측정 통계 바로가기](https://documentation.pubg.com/en/telemetry.html))

원격 측정 데이터는 복잡하지만 매우 상세하다. 엔드 포인트에는 gzip 파일로 압축된 대규모 데이터 및 정보가 포함되어 있으며, 이는 경기에서 발생한 모든 이벤트의 기록과 3차원 지도에서의 좌표를 유지한다.

Telemetry 엔드 포인트 내에서 사용 가능한 데이터에는 지도 유형 및 날씨가 포함되며, 플레이어에 대한 공격, 무기 사용, 공격자와 대상 사이의 거리도 기록된다. 또한, 무기, 회복템 및 차량과 같은 지도에있는 모든 항목의 위치 정보 기록을 유지한다. 플레이어의 움직임, 무기 파밍, 차량 사용 및 사망 현장은 모두 telemetry 데이터에 측정된다.

Telemetry 데이터는 상세하고 세부적이므로, 가져오는 데 시간이 많이 걸릴 수 있다. 하지만 히트맵과 같은 전체 경기 플레이 시각 자료, 또는 플레이어 이동 시각 자료를 포함하여 엄청난 시각화가 가능하다.

URL 공식 : {약간 복잡함, 나중에}

검색 및 캐싱은 하드웨어의 성능 기능에 따라 많은 시간이 걸립니다. Telemetry 데이터로 계산 또는 집계된 필드를 만드는 데 오래 걸릴 수 있다. 우수한 서버와 하드웨어는 데이터 수집 프로세스의 속도를 높인다.

4. 매치 통계(Matches, [경기 통계 바로가기](https://documentation.pubg.com/en/matches-endpoint.html))

매치 데이터에는 플레이한 게임 모드, 매치 기간 또는 사용된 플랫폼(이것이 샤드임을 기억)과 같은 완료된 PUBG 매치에 대한 정보가 포함된다.

Match 데이터에는 네 가지의 개체가 있다 : 매치 개체, 팀원 개체, 참가자 개체 및 유용한 개체 

- 매치 개체는 매치 기본 정보에 대한 데이터를 포함한다. (예 : 경기의 게임 모드, 맵 이름 및 참여하는 플레이어 이름)

- 팀원 개체는 각 참가 그룹의 점수에 따른 것이므로 게임 내 흐름에서만 의미가 있어, 독립형 리소스가 될 수 없다.

- 참가자 개체의 데이터는 경기 중 플레이어의 행동에 대한 추가 인사이트를 제공합니다. 사망한 적의 수(DBNO), 치료, 어시스트, 생존 시간, 도로상의 거리 등에 대한 통계가 참가자 개체에 포함된다.

- 마지막으로 유용한 개체는 사용자와 매치에 대한 더 많은 인사이트를 얻을 수 있는 telemetry 정보를 포함하는 JSON 파일에 연결하는 url이다.

      * 참고 :
      매치 데이터를 검색하는 데는 속도 제한이 없으므로 권한(authorization)이 필요하지 않다.
    
MatchID는 특정 매치를 검색하는 데 사용되는 36글자의 문자열이다. 다음은 매치 데이터에 액세스하는 데 사용되는 URL 형식이다. 플레이어 엔드 포인트를 사용하여 MatchID에 액세스 할 수 있다.

URL 형식 : https://api.pubg.com/shards/steam/matches/{ID}

MatchID의 예는 다음과 같다.

URL 형식 : https://api.pubg.com/shards/steam/matches/2b667ba8-3c18-458b-be4e-eecaf9b010ee

매치 데이터는 다른 플랫폼 (샤드)과 URL (예 : https://api.pubg.com/shards/tournament) 을 사용하여 토너먼트를 통해 액세스 할 수도 있다.

전체 데이터 사전은 사이트에서 찾을 수 있다.

5. 생애 통계(Lifetime, [생애 통계 바로가기](https://documentation.pubg.com/en/lifetime-stats.html))

이것은 별다른 설명없이도 된다! 이 엔드 포인트를 사용하면 단일 플레이어가 참여한 모든 시즌이 요약된 생애 통계에 액세스 할 수 있다. 이 엔드 포인트 내에서 찾을 수있는 통계에는 해당 시즌의 총 어시스트, 치료, 킬, 패배, 부활 등이 포함된다.

URL 형식 : https://api.pubg.com/shards/steam/ players / {ACCOUNTID} / seasons / lifetime

6. 상위권 통계(LeaderBoard, [상위권 통계 바로가기](https://documentation.pubg.com/en/leaderboards-endpoint.html))

상위권 통계에는 특정 게임 모드 별 상위 1000명의 현재 순위가 포함된다. 상위권은 게임 모드(솔로, 듀오, 스쿼드)에 따라 다르다. 리더 보드 엔드 포인트는 각각 500개씩 두 개의 "페이지"로 분리되기 때문에, 1000개를 모두 얻기 위해서는 두 번의 요청이 필요하다. 0은 처음 500명에 대한 첫 페이지이고, 1은 나머지 500명에 대한 두 번째 페이지이다.

URL 공식 : https://api.pubg.com/shards / steam / leaderboards / {GAMEMODE}? page [number] = 0

https://api.pubg.com/shards / steam / leaderboards / {GAMEMODE}? page [number] = 1

    * 참고 :
    “리더 보드 통계는 PC 플레이어만 사용할 수 있다. 리더 보드는 2시간마다 업데이트된다.”— PUBG API 문서
    
7. 토너먼트 통계(Tournaments, [토너먼트 통계 바로가기](https://documentation.pubg.com/en/tournaments-endpoint.html))

토너먼트 엔드 포인트는 다음 두 가지 목적에 부합한다 : 현재 가능한 토너먼트 리스트에 액세스하는 것, 단일 토너먼트에서 데이터를 추출하는 것.

매치 ID에 대한 정보는 토너먼트에서 추출 할 수 있습니다.

- 가능한 토너먼트

   URL 형식 : https://api.pubg.com/tournaments

- 단일 토너먼트의 세부 사항

   URL 형식 : https://api.pubg.com/tournaments/{id}
