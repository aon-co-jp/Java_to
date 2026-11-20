# Java_to 開発メモ
- Rust 製 CLI。`lexer.rs`(字句)→`parse.rs`(宣言レベルの構文)→`frameworks.rs`(台帳と判定)→`endpoints.rs`(ルート抽出)→`body.rs`(式・文の Python 翻訳)→`pygen.rs`(Python 出力)/`rsgen.rs`(Poem・RPoem 出力)。`java_rt.py` は生成物に同梱する実行時部品。
- 方針: 訳せないものは黙って落とさず TODO。REST は作らない(API 方針は runo/CLAUDE.md)。
- RPoem の出力形は 2Poem の emit.py に合わせた(`#[open_runo_poem_compat_macro::handler] async fn x(_req: Request, params: Params) -> Response`)。
- 試験は WSL: `wsl -d Ubuntu -- bash /mnt/f/Java_to/tests/run-all.sh`。`bash -c "…$PATH…"` はホスト側で展開されるのでスクリプトファイル経由で。
- 台帳(58件)は調査(日英の検索+GitHub)に基づく目安。公開年は概数で、未検証の項目がある。

> このリポジトリの変換機能は `pl2pl` に統合され、組み合わせ選択は pl2pl が最優先で進めます。/ The conversion features of this repository are being merged into `pl2pl`, which now carries the selectable-stack feature as top priority. https://github.com/aon-co-jp/pl2pl

## 最優先機能 / Top-priority feature: 組み合わせを選べる変換 (`pl2pl web --backend ...`) (2026-11-19, 10 languages)

```
pl2pl web <dir> --backend <rust-axum|rust-poem|fastapi|mfastapi|rpoem|rporea>
                [--frontend bootstrap-ts|rust-wasm|none] [--db none|mysql|mariadb|postgres|sqlite] --out <dir>
```
COBOL は変換器で、C / C++ / Fortran は「そのままビルドして呼ぶ橋渡し」(`legacy/build.sh`)で、選んだ組み合わせのアプリの骨組みになります。Axum 版は実際に起動して呼び出しの成功を確認済みです(COBOL と C/C++)。RPoem / RPoRea は Bootstrap・TypeScript なし(Rust→WASM)。

### 日本語
やさしく言うと、「古いプログラム(COBOL・C・C++・Fortran)を、今のウェブのしくみに乗せる」機能です。サーバーは Axum・Poem・FastAPI・RPoem・RPoRea から、画面は HTML5・CSS3・TypeScript・Bootstrap から、データベースは MySQL・MariaDB・PostgreSQL・SQLite から、好きな組み合わせを選べます。COBOL は書き換えて変換します。C・C++・Fortran は元のまま動かし、ウェブから呼べるようにします(まだ Rust への書き換えはしません)。メインフレーム・UNIX・Linux・Windows・Mac の COBOL を別の OS の COBOL へ直す機能は `pl2pl cobol`(IBM→GnuCOBOL)にあります。
### English
In simple words: it puts old programs (COBOL, C, C++, Fortran) onto today's web. You pick the server (Axum, Poem, FastAPI, RPoem or RPoRea), the screen (HTML5, CSS3, TypeScript, Bootstrap) and the database (MySQL, MariaDB, PostgreSQL or SQLite) in any allowed mix. COBOL is rewritten. C, C++ and Fortran keep running as they are, and the web calls them (they are not rewritten into Rust yet). Turning COBOL from one OS into COBOL for another OS is `pl2pl cobol` (IBM to GnuCOBOL).
### 简体中文
简单地说:把旧程序(COBOL、C、C++、Fortran)放到今天的网页上。服务器可选 Axum、Poem、FastAPI、RPoem、RPoRea;界面可选 HTML5、CSS3、TypeScript、Bootstrap;数据库可选 MySQL、MariaDB、PostgreSQL、SQLite,自由组合。COBOL 会被改写;C、C++、Fortran 保持原样运行,由网页调用(暂不改写成 Rust)。把 COBOL 从一个系统转到另一个系统用 `pl2pl cobol`。
### 繁體中文
簡單地說:把舊程式(COBOL、C、C++、Fortran)放到現在的網頁上。伺服器可選 Axum、Poem、FastAPI、RPoem、RPoRea;畫面可選 HTML5、CSS3、TypeScript、Bootstrap;資料庫可選 MySQL、MariaDB、PostgreSQL、SQLite,自由搭配。COBOL 會被改寫;C、C++、Fortran 維持原樣執行,由網頁呼叫(暫不改寫成 Rust)。把 COBOL 從一個系統轉到另一個系統用 `pl2pl cobol`。
### 한국어
쉽게 말하면, 오래된 프로그램(COBOL, C, C++, Fortran)을 지금의 웹에 올려 주는 기능입니다. 서버는 Axum, Poem, FastAPI, RPoem, RPoRea 중에서, 화면은 HTML5, CSS3, TypeScript, Bootstrap 중에서, 데이터베이스는 MySQL, MariaDB, PostgreSQL, SQLite 중에서 마음대로 조합할 수 있습니다. COBOL은 다시 써서 변환하고, C·C++·Fortran은 그대로 실행하면서 웹에서 부릅니다(아직 Rust로 다시 쓰지는 않습니다). COBOL을 다른 OS의 COBOL로 바꾸는 일은 `pl2pl cobol`이 합니다.
### Deutsch
Einfach gesagt: Alte Programme (COBOL, C, C++, Fortran) werden ins heutige Web gebracht. Man wählt Server (Axum, Poem, FastAPI, RPoem oder RPoRea), Oberfläche (HTML5, CSS3, TypeScript, Bootstrap) und Datenbank (MySQL, MariaDB, PostgreSQL oder SQLite) frei im erlaubten Rahmen. COBOL wird umgeschrieben; C, C++ und Fortran laufen unverändert und werden vom Web aus aufgerufen (noch nicht nach Rust umgeschrieben). COBOL für ein anderes Betriebssystem erzeugt `pl2pl cobol`.
### Français
En termes simples : on met de vieux programmes (COBOL, C, C++, Fortran) sur le web d'aujourd'hui. On choisit le serveur (Axum, Poem, FastAPI, RPoem ou RPoRea), l'écran (HTML5, CSS3, TypeScript, Bootstrap) et la base de données (MySQL, MariaDB, PostgreSQL ou SQLite) dans les combinaisons permises. Le COBOL est réécrit ; C, C++ et Fortran tournent tels quels et sont appelés par le web (pas encore réécrits en Rust). Pour du COBOL d'un autre système, voir `pl2pl cobol`.
### Español
En palabras sencillas: lleva programas antiguos (COBOL, C, C++, Fortran) a la web de hoy. Eliges el servidor (Axum, Poem, FastAPI, RPoem o RPoRea), la pantalla (HTML5, CSS3, TypeScript, Bootstrap) y la base de datos (MySQL, MariaDB, PostgreSQL o SQLite) con las combinaciones permitidas. El COBOL se reescribe; C, C++ y Fortran siguen funcionando tal cual y la web los llama (todavía no se reescriben en Rust). Para COBOL de otro sistema operativo, usa `pl2pl cobol`.
### Português
Em palavras simples: leva programas antigos (COBOL, C, C++, Fortran) para a web de hoje. Você escolhe o servidor (Axum, Poem, FastAPI, RPoem ou RPoRea), a tela (HTML5, CSS3, TypeScript, Bootstrap) e o banco de dados (MySQL, MariaDB, PostgreSQL ou SQLite) nas combinações permitidas. O COBOL é reescrito; C, C++ e Fortran continuam rodando como estão e a web os chama (ainda não são reescritos em Rust). Para COBOL de outro sistema, use `pl2pl cobol`.
### Русский
Простыми словами: старые программы (COBOL, C, C++, Fortran) переносятся в сегодняшний веб. Сервер выбирается из Axum, Poem, FastAPI, RPoem, RPoRea; интерфейс — из HTML5, CSS3, TypeScript, Bootstrap; база данных — из MySQL, MariaDB, PostgreSQL, SQLite, в допустимых сочетаниях. COBOL переписывается; C, C++ и Fortran работают как есть, а веб их вызывает (в Rust пока не переписываются). COBOL для другой ОС делает `pl2pl cobol`.

**確認したこと / Verified**: Axum(COBOL 7本・C・C++)を起動して `POST /cobol_to.Program/Run` が正しい出力を返す。全組み合わせの検査は単体試験。**まだ / Not yet**: gfortran の実機ビルド(この PC の WSL に無い)、C/C++/Fortran→Rust の書き換え、COBOL→別 OS の COBOL の拡張、Axum 以外の起動確認。**進め方 / How we work**: 未了は確認せず自動で進める(元のリポジトリの削除だけは最後に確認)。 / Unfinished items go on automatically without asking (only deleting the original repos is confirmed first).

## 現状と完成予定 (2026-11-20) / Status and plan
### 日本語
**現状(確認済み)**: Java(Spring/JAX-RS/Micronaut/Servlet/Spark)のルート抽出と、FastAPI/MFastAPI の本体翻訳(if/for/while/try、switch 文と switch 式〈矢印形〉、コレクション)、Poem/RPoem の骨組み。単体試験 17 件。移せないものは TODO(Java_to) として明示。
**完成予定(未了・この順で確認なしに進める)**: ①ブロック本体のラムダ・メソッド参照・Stream API ②匿名クラス ③yield を含む switch 式・フォールスルー ④Rust 側の本体翻訳(現在は単純な return のみ) ⑤Spring PetClinic 等の実プロジェクトでの変換と実行確認 ⑥Struts/JSF の画面系・Kotlin/Scala ⑦pl2pl との共通 IR 化。
**約束**: 実機で確かめてから「できた」と書く/未対応は黙って落とさず明示する/秘密は書かない/新しい REST は作らない(Connect のみ)。
### English
**Current state (verified)**: Extracts routes from Java (Spring, JAX-RS, Micronaut, Servlet, Spark) and translates bodies for FastAPI/MFastAPI (if/for/while/try, switch statements and arrow-form switch expressions, collections), with Poem/RPoem scaffolds. 17 unit tests. Anything not translatable is flagged as TODO(Java_to).
**Planned (open, continued automatically in this order)**: (1) block-bodied lambdas, method references and the Stream API, (2) anonymous classes, (3) switch expressions with yield and fall-through, (4) body translation on the Rust side (only simple return today), (5) converting and running real projects such as Spring PetClinic, (6) Struts/JSF screens, Kotlin/Scala, (7) a shared IR with pl2pl.
**Rules**: say "done" only after checking on a real system; never drop unsupported items silently, always flag them; keep secrets out; no new REST (Connect only).


### 追記 (2026-11-20) 紹介ページ / Intro pages
**日本語**: 紹介ページ https://easy-web.tokyo/Java_to/ とデモ https://easy-web.tokyo/Java_to/demo/(実際に変換・実行した入出力の記録)を公開した。VPS の `/root/easy-web-intros/Java_to/`(静的、ポート 8141)と `domains.toml` の `path_prefix` で配信。生成は runo の `scripts/gen-converters-intro.py`。日英のみで、他言語は準備中。
**English**: The intro page https://easy-web.tokyo/Java_to/ and the demo https://easy-web.tokyo/Java_to/demo/ (a record of real conversion/run input and output) are live. They are served statically from `/root/easy-web-intros/Java_to/` on the VPS (port 8141) through a `path_prefix` entry in `domains.toml`; generated by `scripts/gen-converters-intro.py` in runo. Japanese and English only; other languages are in preparation.

## 多言語: 現状と完成予定 / Status and plan in 10 languages (ja, en above)

### 简体中文 — 现状与计划 (2026-11-20)
*以下为 AI 翻译,尚未经母语者校对。*

**现状(已验证)**: 自动识别 Java 框架(Spring、JAX-RS、Micronaut、Servlet、Spark),把路由和方法体转换为 FastAPI / MFastAPI 的 Python,并生成 Poem / RPoem 的 Rust 骨架。支持 if / for / while / try、switch 语句与箭头式 switch 表达式、集合。无法转换的部分标为 TODO(Java_to)并保留原 Java 注释。单元测试 17 项通过。

**计划(未完成,按此顺序自动推进)**: 带代码块的 lambda、方法引用、Stream API、匿名类、Rust 侧的方法体翻译(目前仅简单 return),以及在 Spring PetClinic 等真实项目上的完整验证。

原则:先在真实环境确认再说“完成”;不支持的内容绝不悄悄丢弃,而是明确标出;不写入机密;不新增 REST(仅 Connect)。

### 繁體中文(台灣) — 現況與計畫 (2026-11-20)
*以下為 AI 翻譯,尚未經母語者校對。*

**現況(已驗證)**: 自動辨識 Java 框架(Spring、JAX-RS、Micronaut、Servlet、Spark),將路由與方法本體轉換為 FastAPI / MFastAPI 的 Python,並產生 Poem / RPoem 的 Rust 骨架。支援 if / for / while / try、switch 陳述式與箭頭式 switch 運算式、集合。無法轉換的部分標為 TODO(Java_to)並保留原 Java 註解。單元測試 17 項通過。

**計畫(未完成,依此順序自動推進)**: 含區塊的 lambda、方法參照、Stream API、匿名類別、Rust 端的方法本體翻譯(目前僅簡單 return),以及在 Spring PetClinic 等真實專案上的完整驗證。

原則:先在真實環境確認才說「完成」;不支援的內容絕不悄悄丟棄,而是明確標出;不寫入機密;不新增 REST(僅 Connect)。

### 한국어 — 현황과 계획 (2026-11-20)
*아래는 AI 번역이며 원어민 검수 전입니다.*

**현황(검증 완료)**: Java 프레임워크(Spring, JAX-RS, Micronaut, Servlet, Spark)를 자동 판별하여 라우트와 본문을 FastAPI / MFastAPI의 Python으로, 뼈대를 Poem / RPoem의 Rust로 변환합니다. if / for / while / try, switch 문과 화살표형 switch 식, 컬렉션을 지원합니다. 변환할 수 없는 부분은 TODO(Java_to)로 표시하고 원래 Java를 주석으로 남깁니다. 단위 테스트 17건 통과.

**계획(미완료, 이 순서대로 자동 진행)**: 블록 본문 람다, 메서드 참조, Stream API, 익명 클래스, Rust 쪽 본문 변환(현재는 단순 return만), 그리고 Spring PetClinic 같은 실제 프로젝트에서의 전체 검증.

원칙: 실제 환경에서 확인한 뒤에만 "완료"라고 씁니다. 지원하지 않는 항목은 조용히 버리지 않고 명확히 표시합니다. 비밀 정보는 쓰지 않으며 새 REST는 만들지 않습니다(Connect만).

### Deutsch — Stand und Plan (2026-11-20)
*Die folgenden Texte sind KI-Übersetzungen und noch nicht von Muttersprachlern geprüft.*

**Aktueller Stand (geprüft)**: Erkennt das Java-Framework (Spring, JAX-RS, Micronaut, Servlet, Spark) automatisch und wandelt Routen und Rümpfe in FastAPI-/MFastAPI-Python um; für Poem / RPoem entsteht ein Rust-Gerüst. Unterstützt if / for / while / try, switch-Anweisungen und switch-Ausdrücke in Pfeilform, Collections. Nicht Übersetzbares wird als TODO(Java_to) markiert, das Java-Original bleibt als Kommentar. 17 Unit-Tests bestehen.

**Geplant (offen, wird in dieser Reihenfolge automatisch fortgesetzt)**: Lambdas mit Block, Methodenreferenzen, Stream-API, anonyme Klassen, Rümpfe auf der Rust-Seite (bisher nur einfaches return) sowie ein vollständiger Durchlauf mit echten Projekten wie Spring PetClinic.

Regeln: „Fertig“ erst nach Prüfung auf einem echten System; Nicht-Unterstütztes nie stillschweigend weglassen, sondern klar kennzeichnen; keine Geheimnisse ablegen; keine neue REST-API (nur Connect).

### Français — État et plan (2026-11-20)
*Les textes suivants sont des traductions automatiques, pas encore relues par des locuteurs natifs.*

**État actuel (vérifié)**: Détecte automatiquement le framework Java (Spring, JAX-RS, Micronaut, Servlet, Spark) et convertit routes et corps vers du Python FastAPI / MFastAPI, avec un squelette Rust pour Poem / RPoem. Prend en charge if / for / while / try, instructions switch et expressions switch en flèche, collections. Ce qui ne peut pas être converti est signalé TODO(Java_to), le Java d'origine restant en commentaire. 17 tests unitaires réussis.

**Prévu (ouvert, poursuivi automatiquement dans cet ordre)**: Lambdas à bloc, références de méthode, API Stream, classes anonymes, corps côté Rust (seulement return simple aujourd'hui) et un essai complet sur de vrais projets comme Spring PetClinic.

Règles : n'écrire « terminé » qu'après vérification sur un système réel ; ne jamais abandonner en silence ce qui n'est pas pris en charge, mais le signaler clairement ; aucun secret ; pas de nouvelle API REST (Connect uniquement).

### Español — Estado y plan (2026-11-20)
*Los textos siguientes son traducciones de IA, aún sin revisión de hablantes nativos.*

**Estado actual (verificado)**: Detecta automáticamente el framework Java (Spring, JAX-RS, Micronaut, Servlet, Spark) y convierte rutas y cuerpos a Python de FastAPI / MFastAPI, con un esqueleto Rust para Poem / RPoem. Admite if / for / while / try, sentencias switch y expresiones switch en flecha, colecciones. Lo que no se puede convertir se marca TODO(Java_to) y el Java original queda como comentario. 17 pruebas unitarias correctas.

**Previsto (pendiente, se continúa automáticamente en este orden)**: Lambdas con bloque, referencias a métodos, API Stream, clases anónimas, cuerpos en Rust (hoy solo return simple) y una prueba completa con proyectos reales como Spring PetClinic.

Reglas: decir «terminado» solo tras comprobarlo en un sistema real; nunca descartar en silencio lo no admitido, sino señalarlo claramente; sin secretos; sin nuevas API REST (solo Connect).

### Português (BR) — Estado e plano (2026-11-20)
*Os textos a seguir são traduções de IA, ainda não revisadas por falantes nativos.*

**Estado atual (verificado)**: Detecta automaticamente o framework Java (Spring, JAX-RS, Micronaut, Servlet, Spark) e converte rotas e corpos para Python de FastAPI / MFastAPI, com um esqueleto Rust para Poem / RPoem. Suporta if / for / while / try, instruções switch e expressões switch em seta, coleções. O que não pode ser convertido é marcado TODO(Java_to), com o Java original mantido como comentário. 17 testes unitários passam.

**Planejado (em aberto, seguido automaticamente nesta ordem)**: Lambdas com bloco, referências a métodos, API Stream, classes anônimas, corpos no lado Rust (hoje só return simples) e um teste completo com projetos reais como o Spring PetClinic.

Regras: só dizer «concluído» depois de verificar em um sistema real; nunca descartar em silêncio o que não é suportado, mas sinalizar claramente; sem segredos; sem novas APIs REST (somente Connect).

### Русский — Состояние и план (2026-11-20)
*Следующие тексты переведены ИИ и ещё не проверены носителями языка.*

**Текущее состояние (проверено)**: Автоматически определяет Java-фреймворк (Spring, JAX-RS, Micronaut, Servlet, Spark) и преобразует маршруты и тела методов в Python для FastAPI / MFastAPI, а также создаёт каркас на Rust для Poem / RPoem. Поддерживает if / for / while / try, операторы switch и switch-выражения со стрелкой, коллекции. Непереводимое помечается TODO(Java_to), исходный Java остаётся в комментарии. 17 модульных тестов проходят.

**Планируется (не завершено, продолжается автоматически в таком порядке)**: Лямбды с блоком, ссылки на методы, Stream API, анонимные классы, тела методов на стороне Rust (пока только простой return) и полная проверка на реальных проектах вроде Spring PetClinic.

Правила: писать «готово» только после проверки на реальной системе; неподдерживаемое никогда не отбрасывать молча, а явно помечать; не хранить секреты; не создавать новый REST (только Connect).
