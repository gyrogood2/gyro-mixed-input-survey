# 패드 + 마우스 자이로 혼합 입력 정리

기준일: 2026-11-10

- 자이로 모드 관련 링크는 이 날짜 기준으로 페이지 존재/설명을 직접 확인했습니다.
- Gyro4All 지원 목록은 저장소의 `docs/GAMES.md`(최신 v1.10.0, 게임 67개 등재 — 일부는 부분 테스트)를 그대로 옮겼습니다.
- Input Labs 혼합 입력 DB(194개 게임)의 등급도 사이트 데이터를 직접 읽어 반영했습니다.

---

## 1. 결론

1. **가장 깔끔한 답은 "자이로 → 게임 카메라 직결"** 입니다. 마우스를 흉내내지 않으니 패드 UI ↔ 키마 UI가 번갈아 뜨는 문제가 원천적으로 사라집니다. 이걸 67개 게임에 한 번에 해주는 게 **Gyro4All**.
2. Gyro4All이 없는 게임은 **혼합 입력 픽스**(동시 입력 허용 + UI를 한쪽으로 고정) 또는 **프롬프트 고정** 모드를 게임별로 씁니다.
3. **온라인 / 안티치트 게임에서는 둘 다 쓰면 안 됩니다.**

| 방식 | 원리 | 장점 | 단점 |
|---|---|---|---|
| **A. 네이티브 자이로** | 자이로 값을 마우스가 아니라 게임 카메라에 직접 주입 | UI 깜빡임 없음, 듀얼센스 햅틱/어트리거 유지 | 게임별 지원 필요, 온라인 불가 |
| **B. 혼합 입력 픽스** | 패드+마우스를 동시에 처리하고 UI가 볼 장치를 한쪽으로 고정 | 프리즈/크래시까지 막는 사례 있음 | 게임 패치에 민감, 로더 필요 |
| **C. 프롬프트/글리프 고정** | 입력은 그대로 두고 버튼 아이콘만 고정 | 가볍고 충돌 적음 | 크래시 방지 효과는 없음 |
| **D. 입력 전환기** | 마지막 입력 장치에 따라 UI/입력 전환 | 범용 | 전환 지연, 입력 드롭 |

A가 있으면 A, 없으면 B, B도 없으면 C/D 순서로 고르면 됩니다.

---

## 2. 왜 깜빡이고, 왜 크래시가 나나

대부분의 엔진은 "마지막으로 입력이 들어온 장치"를 현재 입력 장치로 계속 덮어씁니다. 자이로를 마우스로 매핑하면 매 프레임 패드와 마우스가 번갈아 "내가 마지막 입력"이라고 주장하고, 그때마다 UI 힌트·레이아웃·입력 분기(앉기/회피/상호작용)를 다시 계산합니다.

- **UI 깜빡임** = 혼합 입력을 쓰는 거의 모든 게임에서 발생
- **프리즈/크래시** = UI 갱신 부하가 큰 일부 게임에서만 발생

Cyberpunk 2077이 대표 사례입니다. 2.11 패치 이후 20~60분 플레이 후 화면 프리즈 + 워치독 크래시가 보고됐고, 모드 제작자(Havner)가 재현·측정했습니다. CDPR은 "지원하지 않는 구성"이라며 수정을 거부했습니다.

---

## 3. Gyro4All — 한 번에 해결하는 범용 모드

- GitHub: <https://github.com/gfgerard/Gyro4All>
- 지원 게임 목록: <https://github.com/gfgerard/Gyro4All/blob/main/docs/GAMES.md>
- 구 이름은 GyroUE입니다. (Nexus 도구 페이지: <https://www.nexusmods.com/site/mods/2443>)

**특징**

- `winmm.dll`(충돌 시 `version.dll`로 이름 변경)을 게임 exe 옆에 넣으면 끝
- 자이로가 게임 자체 카메라를 직접 회전 → **UI 깜빡임 없음**, Steam Input 없이 동작
- F10 설정 패널: 감도, 활성화 방식, 반전, 스무딩, 가속 곡선, 보정, ADS/스코프 프로파일
- 플릭 스틱, 카메라 리센터, 줌 배율 보정(Adapt to Zoom)
- 듀얼센스 / 스위치 프로 지원, 필요하면 XInput 에뮬레이션까지 자동

**주의**

- **오프라인 싱글플레이 전용**입니다. 온라인/안티치트 게임에 쓰면 안 됩니다.
- DS4Windows, DSX, reWASD는 종료하고 사용하세요. (Steam Input은 켜둬도 됨)
- 네이티브 자이로 모드를 쓰는 게임에서는 Steam Input 자이로 출력을 **None**으로 꺼야 이중 회전이 안 생깁니다.

### 대표 지원작

| 게임 | 비고 |
|---|---|
| Cyberpunk 2077 | 차량 운전 중 자동 off |
| Red Dead Redemption 2 | 스토리 모드 전용 |
| Control (Ultimate, DX12) | DX11은 미테스트 |
| DOOM 2016 / Eternal / The Dark Ages | 캠페인 전용 |
| Resident Evil 2·3·4 리메이크 / 7 / 빌리지 / 레퀴엠 | RE Engine, 일부 REFramework 필요 |
| Battlefield 4 / Hardline | 캠페인 전용, EA 오버레이 끄기 |
| Halo: MCC | 안티치트 끄고 실행 |
| Hogwarts Legacy | 커스텀 프로파일 |
| Indiana Jones and the Great Circle | |
| Silent Hill 2 (리메이크) | |
| Warhammer 40K: Space Marine 2 | 솔로/프라이빗 + EAC off |
| Returnal | |
| Mass Effect Legendary Edition 1·2·3 | |
| Metro 2033 Redux / Last Light Redux / Exodus EE | |
| Titanfall 2 | 캠페인 전용 |
| Days Gone / Dead Space (2023) / Dead Island 2 | |
| Dishonored DE / Wolfenstein TNO·TNC / Hitman 3 | |
| Crysis (2007) / Crysis Remastered / Crysis 2 Remastered | 3는 목록에 없음 |
| Gears of War: E-Day | 캠페인 + EAC off |
| Kingdom Come: Deliverance (1) | `version.dll`로 설치 |

**그 외 지원작(67개 중)**: 007 First Light, Alan Wake 2, Alan Wake Remastered, Battlefield Hardline, Cronos: The New Dawn, Ghostrunner 1·2, Ghostwire: Tokyo, Kena: Bridge of Spirits, Life is Strange Remastered, Luto, Mass Effect: Andromeda, Monster Hunter Wilds(부분 테스트), Mortal Shell, Out of Sight, Onimusha: Way of the Sword, Pacific Drive, PAYDAY 3, Pragmata, Remnant: From the Ashes, Remnant 2, RoboCop: Rogue City, Severed Steel, Star Wars Jedi: Fallen Order, Terminator: Resistance, The Outer Worlds: Spacer's Choice, Valor Mortis, Visage, Zombie Army 4.

게임별 정확한 설치 경로와 주의사항은 [GAMES.md](https://github.com/gfgerard/Gyro4All/blob/main/docs/GAMES.md)에 있습니다.

### GyroLib

<https://www.nexusmods.com/site/mods/2418> — 게임에 바로 붙는 모드가 아니라, 모더가 자이로 모드를 만들 때 쓰는 SDK입니다. Monster Hunter Rise, Dark Souls Remastered, Returnal 등의 모드가 이걸 씁니다.

---

## 4. 게임별 모드

한 모드가 여러 표에 중복되지 않도록 게임별로 한 줄씩만 정리했습니다.

| 게임 | 방식 | 모드 | 비고 |
|---|---|---|---|
| Alien: Isolation | 혼합 입력 | [Mixed Input](https://www.nexusmods.com/alienisolation/mods/196) | 프롬프트 버전 선택 가능 |
| Borderlands 4 | 네이티브 | [BL4_Gyro](https://github.com/SalustLab/BL4_Gyro) | oak2-mod-manager |
| Control | 혼합 입력 | [Control Mixed Input](https://www.nexusmods.com/control/mods/156) | DLL 하나, DX11/12, 프롬프트·럼블 유지 |
| Cyberpunk 2077 | 네이티브 | [Native Gyro by Gyro4All](https://www.nexusmods.com/cyberpunk2077/mods/37770) | 가장 안전. RED4ext 있으면 `version.dll`로 |
| Cyberpunk 2077 | 혼합 입력 | [Mixed Input Fix](https://www.nexusmods.com/cyberpunk2077/mods/31759) | 2.11+ 프리즈/크래시 + 힌트 깜빡임 |
| Cyberpunk 2077 | 프롬프트 고정 | [GlyphLock](https://www.nexusmods.com/cyberpunk2077/mods/35732) | 2.50용, 위 Mixed Input Fix와 **택1** |
| Cyberpunk 2077 | 부가 | [Atmos Fix](https://www.nexusmods.com/cyberpunk2077/mods/34082) / [GyroGate](https://www.nexusmods.com/cyberpunk2077/mods/33190) | 오디오 끊김 / 차량·메뉴에서 자이로 차단 |
| Cyberpunk 2077 | 프롬프트 고정(구형) | [Hide Keyboard Prompts and Mouse Cursor](https://www.nexusmods.com/cyberpunk2077/mods/16693) | 프롬프트·커서를 투명하게 |
| Dark Souls Remastered | 네이티브 | [Gyro Aim](https://www.nexusmods.com/darksoulsremastered/mods/1470) | 활/쌍안경/마법 조준, Steam Input 모션 사용 |
| Deus Ex: Human Revolution | 혼합 입력 | [Simultaneous Mouse and Controller Input Fix](https://www.nexusmods.com/deusexhumanrevolution/mods/73) | Java 11+ 필요 |
| DOOM: The Dark Ages | 프롬프트 고정 | [Kaibz Mod](https://www.nexusmods.com/doomthedarkages/mods/27) | Input Tweaker에서 프롬프트 강제 |
| Dying Light 2 | 혼합 입력 | [Gamepad and mouse mixed input fix](https://www.nexusmods.com/dyinglight2/mods/1904) | 인젝터 방식, 업데이트 시 깨질 수 있음 |
| Fallout 4 | 혼합 입력 | [WattzIO - Auto Input Swap](https://www.nexusmods.com/fallout4/mods/109070) | F4SE, 글리프 고정 + 입력 지연 최소화 |
| Horizon Forbidden West | 혼합 입력 | [Mixed Input Fix](https://www.nexusmods.com/horizonforbiddenwest/mods/211) | Steam판 전용 |
| Kingdom Come: Deliverance II | 프롬프트 고정 | [Always Xbox Prompts](https://www.nexusmods.com/kingdomcomedeliverance2/mods/3472) | ASI 로더 필요 |
| Mass Effect Legendary Edition | HUD/프롬프트 | [HUD Enhancements LE1](https://www.nexusmods.com/masseffectlegendaryedition/mods/1894) | 혼합 입력 픽스가 아니라 HUD 확장 |
| Monster Hunter Rise | 네이티브 | [Gyro Aim](https://www.nexusmods.com/monsterhunterrise/mods/3997) | REFramework + GyroLib |
| NieR Replicant | 혼합 입력 | [Simultaneous Mouse and Controller Input Fix](https://www.nexusmods.com/nierreplicant/mods/123) | `winmm.dll`/ASI |
| No Man's Sky | 네이티브(해금) | [Native Gyro on PC](https://www.nexusmods.com/nomanssky/mods/2665) | 숨겨진 자이로 옵션 해금 + DS4Windows 패스스루 |
| Oblivion Remastered | 혼합 입력 | [Oblivions MILF](https://www.nexusmods.com/oblivionremastered/mods/5641) | UE4SS 버전 맞춰 설치 |
| Onimusha: Way of the Sword | 프롬프트 고정 | [Force Button Prompts](https://www.nexusmods.com/onimushawayofthesword/mods/120) | REFramework, KBM/패드/Off 선택 |
| Phantom Blade Zero | 프롬프트 고정 | [Gyro Friendly Prompts](https://www.nexusmods.com/phantombladezero/mods/184) | 메뉴에서는 마우스 커서 유지 |
| Red Dead Redemption 2 | 네이티브 | [Native gyro by Gyro4All](https://www.nexusmods.com/reddeadredemption2/mods/11554) | 스토리 모드 전용 |
| Red Dead Redemption 2 | 프롬프트 고정 | [Lock Controller Prompts (Gyro Friendly)](https://www.nexusmods.com/reddeadredemption2/mods/10853) | F8/F9 토글 |
| Returnal | 네이티브 | [Native Gyro and Flickstick](https://www.nexusmods.com/returnal/mods/101) | 햅틱/어트리거 유지 + 플릭 스틱 |
| Rise of the Tomb Raider | 혼합 입력 | [RotTR Mixed Input fix](https://www.nexusmods.com/riseofthetombraider/mods/294) | 마우스가 프롬프트를 못 바꾸게 |
| S.T.A.L.K.E.R. 2 | 혼합 입력 | [Mixed input Fix](https://www.nexusmods.com/stalker2heartofchornobyl/mods/2816) | Steam Input 자이로 + 패드 동시 사용 |
| Skyrim SE | 혼합 입력 | [SICKM](https://www.nexusmods.com/skyrimspecialedition/mods/50016) | controlmap 기반, 패드+키보드+마우스 |
| Starfield | 혼합 입력 | [Simultaneous Mouse and Gamepad](https://www.nexusmods.com/starfield/mods/17467) | SFSE + Address Library |
| Subnautica / Below Zero | 혼합 입력 | [Mixed Input (BepInEx)](https://www.nexusmods.com/subnautica/mods/1219) | BepInEx |
| Valheim | 혼합 입력 | [Mixed Input](https://www.nexusmods.com/valheim/mods/3652) | 핫바/툴팁 깜빡임 방지(키마 UI 고정) |
| Valheim | 입력 고정 | [Lock Gamepad UI](https://www.nexusmods.com/valheim/mods/3683) | 반대로 패드 UI 고정, 위 모드와 **택1** |
| Warhammer 40K: Darktide | 혼합 입력 | [Mixed Input CKBM](https://www.nexusmods.com/warhammer40kdarktide/mods/713) | 게임 내부 `combined` 모드 활성화 |
| The Witcher 3 | UI 고정 | [Force Specific Controller UI](https://www.nexusmods.com/witcher3/mods/7203) | `LockControlScheme=1` |

---

## 5. 모드 없이 되는 게임 (Input Labs 혼합 입력 DB)

<https://inputlabs.io/mixed-input/db> — 194개 게임을 혼합 입력 품질로 등급화한 DB. 등급 정의는 <https://inputlabs.io/mixed-input/tiers>.

| 등급 | 의미 | 대표작 |
|---|---|---|
| **Platinum** | 동시 입력 지원, UI 깜빡임 없음 | Elden Ring, Sekiro, Armored Core VI, FFXIV, Overwatch, Kingdom Come: Deliverance, DCS World, Selaco, Elden Ring Nightreign |
| **Platinum↓** | 동시 입력 지원, UI 잠금 설정에 제약이 있음 | Doom (1993), Amid Evil, Fortnite, Marvel Rivals, Ghost Recon Breakpoint |
| **Gold** | 동시 입력 지원, UI 깜빡임은 있으나 성능 영향 없음 | Cyberpunk 2077, RDR2, The Witcher 3, Death Stranding, Doom 2016/Eternal/Dark Ages, Battlefield 1·3·4·5·6, Halo Infinite, Helldivers 2, Resident Evil 4, Silent Hill 2, STALKER 2, Titanfall 2, Warframe, Crimson Desert |
| **Silver** | 설정을 많이 만져야 만족 | Days Gone, Deep Rock Galactic, KCD2, Palworld, Stellar Blade, No Man's Sky, Minecraft Bedrock, Resident Evil Requiem |
| **Bronze** | 모드/파일 수정이 사실상 필요 | Avowed, GTA V, Skyrim, Space Marine 2, Returnal, Star Wars Outlaws, Dune: Awakening, Ace Combat 7, MS Flight Simulator, Tomb Raider I–III Remastered |
| **No support** | 한 번에 한 장치만 | Baldur's Gate 3, Control, Apex Legends, Destiny 2, Dying Light 2, Deathloop, AC Origins, Horizon Zero Dawn/Forbidden West, Ghost of Tsushima, Metro Exodus, Resident Evil 2 (2019), Valheim, Vermintide 2 |

읽는 법 두 가지:

- **Cyberpunk이 Gold인데 크래시가 나는 이유**: DB는 "입력/UI 품질"만 평가하고, 2.11+ 프리즈 버그는 별도 이슈입니다.
- **Metro Exodus, HFW, Valheim이 "No support"인데 위 표에 모드가 있는 이유**: DB는 바닐라 기준입니다. A안(네이티브 자이로)이나 B안 모드를 쓰면 해결됩니다.

---

## 6. 실전: Cyberpunk 2077

1. **최우선: Native Gyro by Gyro4All** ([모드](https://www.nexusmods.com/cyberpunk2077/mods/37770))
   - 마우스 에뮬레이션이 없어 UI 혼동/크래시 위험이 가장 낮음
   - RED4ext가 `winmm.dll`을 쓰므로 Gyro4All은 **`version.dll`로 이름 변경**
   - 설치 위치: `bin\x64` (Cyberpunk2077.exe 옆)
2. **Steam Input 자이로→마우스를 꼭 쓰겠다면**
   - `Mixed Input Fix`(크래시 + 상태 고정) **또는** `GlyphLock`(2.50 프롬프트 락) 중 **하나만**
   - 오디오 크래클/Atmos 끊김이 있으면 `Atmos Fix`
   - 차량에서 자이로가 계속 돌면 `GyroGate`
3. 듀얼센스 햅틱/어트리거를 살리고 싶으면 A안이 정답입니다. Steam Input 자이로→마우스는 햅틱을 죽이는 경우가 많습니다.

---

## 7. 주의사항

1. **온라인/안티치트 금지** — Gyro4All은 공식적으로 오프라인 싱글 전용입니다. Battlefield 멀티, Halo MCC 안티치트, Space Marine 2 온라인, PAYDAY 3, GTA Online, RDR Online에서 쓰지 마세요.
2. **게임 패치에 민감** — 네이티브 자이로/혼합 입력 픽스는 게임 업데이트로 주소가 바뀌면 깨집니다. Nexus의 "Last updated"와 게임 버전을 확인하세요.
3. **로더 충돌** — Cyberpunk: RED4ext(`version.dll`로 회피) / Oblivion: UE4SS / KCD2: ASI 로더 / Valheim·Subnautica: BepInEx / Onimusha: REFramework.
4. **Steam Input** — 네이티브 자이로 모드를 쓰면 Steam Input 자이로 출력을 None으로.
5. **컨트롤러 도구 충돌** — Gyro4All 사용 시 DS4Windows, DSX, reWASD 종료.
6. **Cyberpunk 모드 중복 금지** — `Mixed Input Fix`와 `GlyphLock`은 동시 설치 금지.
7. **모드가 없는 게임** — Baldur's Gate 3, God of War Ragnarok, Spider-Man 2, The Last of Us Part II 등은 현재 자이로/혼합 입력 픽스 모드가 사실상 없습니다. Steam Input 자이로→오른쪽 스틱(또는 마우스)으로 타협하거나 게임 자체 지원을 기다리는 게 낫습니다.

---

## 8. 링크

- Gyro4All: <https://github.com/gfgerard/Gyro4All>
- Gyro4All 지원 게임: <https://github.com/gfgerard/Gyro4All/blob/main/docs/GAMES.md>
- GyroLib SDK: <https://www.nexusmods.com/site/mods/2418>
- Input Labs Mixed Input DB: <https://inputlabs.io/mixed-input/db>
- Input Labs 등급 설명: <https://inputlabs.io/mixed-input/tiers>
- PCGamingWiki: <https://www.pcgamingwiki.com/>
- r/GyroGaming: <https://www.reddit.com/r/GyroGaming/>
