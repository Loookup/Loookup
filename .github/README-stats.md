# 비공개 저장소 통계 설정

이 저장소는 public입니다. 토큰은 GitHub Actions Secret에만 등록하고, 코드·README·이미지 URL·로컬 파일에는 넣지 않습니다. `.gitignore`는 실수로 로컬 환경 파일을 추가하는 것을 막는 보조 장치이며, 이미 커밋한 비밀값을 보호하지는 않습니다.

## 최초 설정

1. GitHub 계정의 **Settings → Developer settings → Personal access tokens → Tokens (classic)**에서 이 통계 전용 토큰을 만듭니다. 만료일을 지정하고 `repo`, `read:user`를 선택합니다. 공식 도구가 비공개 통계에 안내하는 권한이며, **classic PAT의 `repo`는 읽기 전용이 아니고 접근 가능한 저장소 전반의 쓰기 권한도 포함합니다.** `workflow`, 관리자 권한 등은 추가하지 않습니다.
2. `Loookup/Loookup`의 **Settings → Secrets and variables → Actions → New repository secret**에서 이름을 `STATS_TOKEN`으로 지정하고 토큰 값을 등록합니다. 토큰을 채팅이나 커밋으로 전달하지 않습니다.
3. 이 변경을 기본 브랜치에 반영한 뒤 **Actions → Update README stats → Run workflow**를 실행합니다. 기본 브랜치를 선택합니다.

두 카드가 모두 생성되면 `profile/stats.svg`, `profile/top-langs.svg`와 README의 이미지 경로가 함께 커밋됩니다. 최초 성공 전에는 기존 공개 API 이미지 경로가 유지됩니다. 이후에는 매일 한국 시간 06:17에 갱신을 시도하며, GitHub 사정에 따라 실행이 지연될 수 있습니다. 토큰이 만료되면 Secret 값을 갱신하고 워크플로를 다시 실행합니다.

Fine-grained PAT는 저장소 선택과 읽기 전용 권한 제한이 가능하지만, 이 도구의 공식 문서는 **커밋 통계가 공개 저장소로 제한된다**고 안내합니다. 비공개 커밋 포함을 위해 classic PAT를 사용하며, fine-grained PAT로 같은 결과가 나온다고 가정하지 않습니다. 조직 저장소 접근에는 조직의 토큰 정책과 SSO 승인이 추가로 적용될 수 있습니다.

## 공개되는 데이터와 실행 범위

- 생성 대상은 통계 카드와 언어 도넛 카드 두 개입니다. 정상 카드에는 사용자명·활동 수치·등급·언어명·비율이 표시되며, 비공개 저장소명·소스 코드·원본 API 응답을 저장하는 단계를 두지 않았습니다. 비공개 활동의 집계 수치와 언어 비율 자체는 방문자에게 공개됩니다.
- 비공개 데이터는 토큰을 사용하는 생성 작업에서 처리합니다. 게시 작업에는 `STATS_TOKEN`을 전달하지 않으며, 현재 public 저장소에만 쓰기 가능한 `GITHUB_TOKEN`으로 지정된 SVG 두 개와 README만 커밋합니다. 생성 작업의 `GITHUB_TOKEN`은 읽기 전용입니다. 이 제한은 별도로 전달되는 classic PAT의 권한을 줄이지는 않습니다.
- 워크플로는 원본 저장소의 기본 브랜치에서 수동·예약 실행만 허용합니다. PR이나 fork에서 실행하지 않습니다. 토큰이 없거나 카드 생성이 실패하면 게시 작업도 실행하지 않아 기존 카드를 유지합니다.
- 직접 사용하는 Actions는 커밋 SHA로, 통계 라이브러리는 `2.2.1`로 고정했습니다. 생성 SVG 두 개만 artifact로 전달하고 보관 기간은 하루로 지정했습니다. 생성 도구와 그 의존성은 토큰을 처리하므로 신뢰가 필요한 외부 코드이며, 버전을 변경할 때도 검토가 필요합니다.

언어 카드는 기본적으로 본인 소유의 fork가 아닌 저장소를 집계하고 최대 100개 저장소를 조회합니다. 본인이 작성한 코드만 구분하는 지표는 아닙니다. 커밋 카드는 기존 기본 설정처럼 올해 통계를 표시합니다.

## 참고

- [GitHub Readme Stats Action 설정과 토큰 권한](https://github.com/stats-organization/github-readme-stats-action#inputs)
- [공식 PAT 설정 및 fine-grained PAT의 커밋 통계 제한](https://github-stats-extended.vercel.app/frontend/docs/deploy/#first-step-get-your-personal-access-token-pat)
- [GitHub Actions Secret 등록 방법](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets#creating-secrets-for-a-repository)
