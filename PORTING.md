# Java_to 引き継ぎ / Hand-off
## 到達点 (2026-11-18)
初版: 判定58件、ルート抽出(Spring/JAX-RS/Micronaut/Servlet/Spark)、Python 本体翻訳、Rust 骨組み、4OSインストーラーのCI定義。FastAPI e2e 7件・poem/rpoem cargo check 成功。
## 未了 / Open
1. 実プロジェクト(Spring PetClinic 等)での変換・実行確認。
2. CI(release.yml)は未実行。タグ `v0.1.0` を打って各OSの試験を確認する。
3. Rust 側の本体翻訳、Struts/JSF 等の画面系、Kotlin/Scala。
4. pl2pl(統合構想)での共通IR化。
## 再開用メッセージ
「F:\Java_to を再開。PORTING.md の未了1から。まず Spring PetClinic を clone して `java_to convert` → FastAPI 実行確認。」

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

## 2026-11-20 追記: switch 文に対応 / switch statements supported
**日本語**: Java の `switch`(古い形 `case 1: case 2: ... break;` と矢印形 `case 1, 2 -> ...`)を Python の `if/elif/else` に変換する。フォールスルー・途中の `break`・`default` の合流は変換せず、これまでどおり `TODO(Java_to)` として残す。単体試験成功。洗い出した残り(優先順): ①switch 式(`x = switch ...`)、②ブロック本体のラムダ・メソッド参照・Stream API、③匿名クラス、④Rust 側の本体翻訳(現在は単純な `return` のみ)、⑤Spring PetClinic での実プロジェクト通し(未了1)。
**English**: Java `switch` (classic `case 1: case 2: ... break;` and arrow form `case 1, 2 -> ...`) now converts to Python `if/elif/else`. Fall-through, a `break` in the middle, and `default` merged with other labels are not converted and still produce `TODO(Java_to)`. Unit tests pass. Remaining gaps found (in priority order): (1) switch expressions (`x = switch ...`), (2) block-bodied lambdas, method references and the Stream API, (3) anonymous classes, (4) body translation on the Rust side (only simple `return` today), (5) an end-to-end run on Spring PetClinic (open item 1).

## 2026-11-20 追記2: switch 式 / switch expressions
**日本語**: `x = switch (n) { case 1, 2 -> "a"; default -> "b"; };`、`String s = switch ...`、`return switch ...` を `if/elif/else` の代入・`return` に変換する(矢印形のみ。`yield` を含むブロック本体は従来どおり `TODO(Java_to)`)。単体試験 17 件成功。
**English**: `x = switch (n) { case 1, 2 -> "a"; default -> "b"; };`, `String s = switch ...` and `return switch ...` convert to `if/elif/else` assignments or `return` (arrow form only; block bodies with `yield` still produce `TODO(Java_to)`). 17 unit tests pass.

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
*以下为 AI 翻译,并已由 AI 按母语水平审校;尚未经人类母语者校对。*

**现状(已验证)**: 自动识别 Java 框架(Spring、JAX-RS、Micronaut、Servlet、Spark),把路由和方法体转换为 FastAPI / MFastAPI 的 Python,并生成 Poem / RPoem 的 Rust 骨架。支持 if / for / while / try、switch 语句与箭头式 switch 表达式、集合。无法转换的部分标为 TODO(Java_to)并保留原 Java 注释。单元测试 17 项通过。

**计划(未完成,按此顺序自动推进)**: 带代码块的 lambda、方法引用、Stream API、匿名类、Rust 侧的方法体翻译(目前仅简单 return),以及在 Spring PetClinic 等真实项目上的完整验证。

原则:先在真实环境确认再说“完成”;不支持的内容绝不悄悄丢弃,而是明确标出;不写入机密;不新增 REST(仅 Connect)。

### 繁體中文(台灣) — 現況與計畫 (2026-11-20)
*以下為 AI 翻譯,並已由 AI 以母語水準審校;尚未經人類母語者校對。*

**現況(已驗證)**: 自動辨識 Java 框架(Spring、JAX-RS、Micronaut、Servlet、Spark),將路由與方法本體轉換為 FastAPI / MFastAPI 的 Python,並產生 Poem / RPoem 的 Rust 骨架。支援 if / for / while / try、switch 陳述式與箭頭式 switch 運算式、集合。無法轉換的部分標為 TODO(Java_to)並保留原 Java 註解。單元測試 17 項通過。

**計畫(未完成,依此順序自動推進)**: 含區塊的 lambda、方法參照、Stream API、匿名類別、Rust 端的方法本體翻譯(目前僅簡單 return),以及在 Spring PetClinic 等真實專案上的完整驗證。

原則:先在真實環境確認才說「完成」;不支援的內容絕不悄悄丟棄,而是明確標出;不寫入機密;不新增 REST(僅 Connect)。

### 한국어 — 현황과 계획 (2026-11-20)
*아래는 AI가 번역하고 AI가 원어민 수준으로 검수한 것이며, 사람 원어민의 검수는 아직 받지 않았습니다.*

**현황(검증 완료)**: Java 프레임워크(Spring, JAX-RS, Micronaut, Servlet, Spark)를 자동 판별하여 라우트와 메서드 본문을 FastAPI / MFastAPI의 Python으로, 뼈대를 Poem / RPoem의 Rust로 변환합니다. if / for / while / try, switch 문과 화살표형 switch 식, 컬렉션을 지원합니다. 변환할 수 없는 부분은 TODO(Java_to)로 표시하고 원래 Java를 주석으로 남깁니다. 단위 테스트 17건 통과.

**계획(미완료, 이 순서대로 자동 진행)**: 블록 본문 람다, 메서드 참조, Stream API, 익명 클래스, Rust 쪽 메서드 본문 변환(현재는 단순 return만), 그리고 Spring PetClinic 같은 실제 프로젝트에서의 전체 검증.

원칙: 실제 환경에서 확인한 뒤에만 "완료"라고 씁니다. 지원하지 않는 항목은 조용히 버리지 않고 명확히 표시합니다. 비밀 정보는 쓰지 않으며 새 REST는 만들지 않습니다(Connect만).

### Deutsch — Stand und Plan (2026-11-20)
*Die folgenden Texte wurden von einer KI übersetzt und von einer KI auf muttersprachliche Natürlichkeit geprüft; eine Prüfung durch menschliche Muttersprachler steht noch aus.*

**Aktueller Stand (geprüft)**: Erkennt das Java-Framework (Spring, JAX-RS, Micronaut, Servlet, Spark) automatisch und wandelt Routen und Methodenrümpfe in FastAPI-/MFastAPI-Python um; für Poem / RPoem entsteht ein Rust-Gerüst. Unterstützt if / for / while / try, switch-Anweisungen und switch-Ausdrücke in Pfeilform, Collections. Nicht Übersetzbares wird als TODO(Java_to) markiert, das Java-Original bleibt als Kommentar. 17 Unit-Tests bestehen.

**Geplant (offen, wird in dieser Reihenfolge automatisch fortgesetzt)**: Lambdas mit Block, Methodenreferenzen, Stream-API, anonyme Klassen, Methodenrümpfe auf der Rust-Seite (bisher nur einfaches return) sowie ein vollständiger Durchlauf mit echten Projekten wie Spring PetClinic.

Regeln: „Fertig“ erst nach Prüfung auf einem echten System; Nicht-Unterstütztes nie stillschweigend weglassen, sondern klar kennzeichnen; keine Geheimnisse ablegen; keine neue REST-API (nur Connect).

### Français — État et plan (2026-11-20)
*Les textes suivants ont été traduits par une IA et relus par une IA pour leur naturel ; ils n'ont pas encore été relus par des locuteurs natifs humains.*

**État actuel (vérifié)**: Détecte automatiquement le framework Java (Spring, JAX-RS, Micronaut, Servlet, Spark) et convertit routes et corps de méthodes vers du Python FastAPI / MFastAPI, avec un squelette Rust pour Poem / RPoem. Prend en charge if / for / while / try, instructions switch et expressions switch en flèche, collections. Ce qui ne peut pas être converti est signalé par TODO(Java_to), le Java d'origine restant en commentaire. 17 tests unitaires réussis.

**Prévu (ouvert, poursuivi automatiquement dans cet ordre)**: Lambdas à corps de bloc, références de méthode, API Stream, classes anonymes, corps côté Rust (seulement return simple aujourd'hui) et un essai complet sur de vrais projets comme Spring PetClinic.

Règles : n'écrire « terminé » qu'après vérification sur un système réel ; ne jamais abandonner en silence ce qui n'est pas pris en charge, mais le signaler clairement ; aucun secret ; pas de nouvelle API REST (Connect uniquement).

### Español — Estado y plan (2026-11-20)
*Los textos siguientes fueron traducidos por una IA y revisados por una IA en cuanto a naturalidad; aún no los ha revisado un hablante nativo humano.*

**Estado actual (verificado)**: Detecta automáticamente el framework Java (Spring, JAX-RS, Micronaut, Servlet, Spark) y convierte rutas y cuerpos de métodos a Python de FastAPI / MFastAPI, con un esqueleto Rust para Poem / RPoem. Admite if / for / while / try, sentencias switch y expresiones switch en flecha, colecciones. Lo que no se puede convertir se marca con TODO(Java_to) y el Java original queda como comentario. 17 pruebas unitarias correctas.

**Previsto (pendiente, se continúa automáticamente en este orden)**: Lambdas con cuerpo de bloque, referencias a métodos, API Stream, clases anónimas, cuerpos en Rust (hoy solo return simple) y una prueba completa con proyectos reales como Spring PetClinic.

Reglas: decir «terminado» solo tras comprobarlo en un sistema real; nunca descartar en silencio lo no admitido, sino señalarlo claramente; sin secretos; sin nuevas API REST (solo Connect).

### Português (BR) — Estado e plano (2026-11-20)
*Os textos a seguir foram traduzidos por uma IA e revisados por uma IA quanto à naturalidade; ainda não foram revisados por falantes nativos humanos.*

**Estado atual (verificado)**: Detecta automaticamente o framework Java (Spring, JAX-RS, Micronaut, Servlet, Spark) e converte rotas e corpos de métodos para Python de FastAPI / MFastAPI, com um esqueleto Rust para Poem / RPoem. Suporta if / for / while / try, instruções switch e expressões switch em seta, coleções. O que não pode ser convertido é marcado TODO(Java_to), com o Java original mantido como comentário. 17 testes unitários passam.

**Planejado (em aberto, seguido automaticamente nesta ordem)**: Lambdas com corpo em bloco, referências a métodos, API Stream, classes anônimas, corpos no lado Rust (hoje só return simples) e um teste completo com projetos reais como o Spring PetClinic.

Regras: só dizer «concluído» depois de verificar em um sistema real; nunca descartar em silêncio o que não é suportado, mas sinalizar claramente; sem segredos; sem novas APIs REST (somente Connect).

### Русский — Состояние и план (2026-11-20)
*Следующие тексты переведены ИИ и проверены ИИ на естественность звучания; носители языка — люди — их пока не проверяли.*

**Текущее состояние (проверено)**: Автоматически определяет Java-фреймворк (Spring, JAX-RS, Micronaut, Servlet, Spark) и преобразует маршруты и тела методов в Python для FastAPI / MFastAPI, а также создаёт каркас на Rust для Poem / RPoem. Поддерживает if / for / while / try, операторы switch и switch-выражения со стрелкой, коллекции. Непереводимое помечается TODO(Java_to), исходный Java остаётся в комментарии. 17 модульных тестов проходят.

**Планируется (не завершено, продолжается автоматически в таком порядке)**: Лямбда-выражения с блочным телом, ссылки на методы, Stream API, анонимные классы, тела методов на стороне Rust (пока только простой return) и полная проверка на реальных проектах вроде Spring PetClinic.

Правила: писать «готово» только после проверки на реальной системе; неподдерживаемое никогда не отбрасывать молча, а явно помечать; не хранить секреты; не создавать новый REST (только Connect).

## アラビア語・ペルシャ語(イラン) / Arabic and Persian (Iran) — AI翻訳+AIネイティブ確認、人間のネイティブ確認はまだ

### العربية — الحالة والخطة (2026-11-20)

<div dir="rtl" lang="ar">

*النص التالي ترجمه الذكاء الاصطناعي وراجعه الذكاء الاصطناعي من حيث سلامة اللغة، ولم يراجعه بعدُ متحدث أصلي من البشر.*

**الحالة الحالية (تم التحقق منها)**: يكتشف إطار عمل Java (Spring وJAX-RS وMicronaut وServlet وSpark) تلقائيًا، ويحوّل المسارات وأجسام الدوال إلى Python لـ FastAPI / MFastAPI، ويولّد هيكل Rust لـ Poem / RPoem. يدعم if / for / while / try وعبارات switch وتعبيرات switch ذات السهم والمجموعات. يُوسَم ما تتعذّر ترجمته بـ TODO(Java_to) مع إبقاء Java الأصلي تعليقًا. نجح 17 اختبارًا من اختبارات الوحدة.

**المخطَّط له (غير مكتمل، يُتابَع تلقائيًا بهذا الترتيب)**: تعابير lambda التي لها جسم من كتلة شيفرة، والإشارات إلى الدوال، وواجهة Stream، والأصناف المجهولة، وأجسام الدوال في جانب Rust (حاليًا return البسيط فقط)، وتجربة كاملة على مشاريع حقيقية مثل Spring PetClinic.

المبادئ: لا نكتب «تم» إلا بعد التحقق على نظام حقيقي؛ لا نُسقِط ما لا يُدعَم بصمت بل نضع عليه علامة واضحة؛ لا نكتب أسرارًا؛ لا نضيف واجهات REST جديدة (Connect فقط).

</div>

### فارسی (ایران) — وضعیت و برنامه (2026-11-20)

<div dir="rtl" lang="fa">

*متن زیر را هوش مصنوعی ترجمه و از نظر روانی زبان بازبینی کرده است؛ هنوز هیچ فارسی‌زبانِ بومیِ انسانی آن را بررسی نکرده است.*

**وضعیت کنونی (بررسی‌شده)**: چارچوب جاوا (Spring، JAX-RS، Micronaut، Servlet، Spark) را خودکار تشخیص می‌دهد، مسیرها و بدنهٔ متدها را به Python برای FastAPI / MFastAPI تبدیل می‌کند و اسکلت Rust برای Poem / RPoem می‌سازد. از if / for / while / try، دستور switch و عبارت switch با فلش و مجموعه‌ها (collections) پشتیبانی می‌کند. بخش‌های غیرقابل‌ترجمه با TODO(Java_to) علامت می‌خورند و جاوای اصلی به‌صورت توضیح (کامنت) باقی می‌ماند. ۱۷ آزمون واحد موفق است.

**برنامه‌ریزی‌شده (ناتمام؛ به‌صورت خودکار و با همین ترتیب ادامه می‌یابد)**: لامبداهای دارای بلوک، ارجاع به متد، Stream API، کلاس‌های ناشناس، بدنهٔ متدها در سمت Rust (فعلاً فقط return ساده) و آزمایش کامل روی پروژه‌های واقعی مانند Spring PetClinic.

اصول: فقط پس از بررسی روی یک سامانهٔ واقعی می‌نویسیم «انجام شد»؛ آنچه پشتیبانی نمی‌شود هرگز بی‌صدا حذف نمی‌شود و آشکارا علامت می‌خورد؛ رازی (مانند گذرواژه و کلید) نمی‌نویسیم؛ REST جدید نمی‌سازیم (فقط Connect).

</div>


## 2026-11-21 更新: ブロック本体のラムダと Stream API に対応 / Update: block-bodied lambdas and the Stream API

**日本語**: 複数の文を持つブロック本体のラムダ(`x -> { int d = x * 2; ...; return d; }`)を、その文の直前に `def _lam1(x):` として出して使う形で変換できます。Stream API は `.stream()`・`.map`・`.filter`・`.mapToInt`・`.forEach`・`.anyMatch/allMatch/noneMatch`・`.distinct`・`.limit/.skip`・`.sorted()`・`.sum`・`.count`・`.findFirst`・`.reduce(初期値, 関数)`・`.collect(Collectors.toList()/toSet()/joining(区切り))` を Python に変換します(途中結果は list で即時評価)。変換した Python を実際に動かし、`[1..6]` に `filter(x > 1)` とブロックラムダ(`if` で上限 10)をかけて `[4, 6, 8, 10, 10]`、`mapToInt(...).sum()` が 21 になることを確認しました。単体試験は 18 件成功です。**まだ**: 比較関数つきの `.sorted(...)`、`Collectors.groupingBy` など他の collector、匿名クラス、メソッド参照、Rust 側の本体翻訳(現在は単純な `return` のみ)、Spring PetClinic など実プロジェクトでの通し。

**English**: Block-bodied lambdas with several statements (`x -> { int d = x * 2; ...; return d; }`) are now converted: the body is emitted as `def _lam1(x):` just before the enclosing statement and used by name. The Stream API (`.stream()`, `.map`, `.filter`, `.mapToInt`, `.forEach`, `.anyMatch/allMatch/noneMatch`, `.distinct`, `.limit/.skip`, `.sorted()`, `.sum`, `.count`, `.findFirst`, `.reduce(identity, fn)`, `.collect(Collectors.toList()/toSet()/joining(sep))`) is translated to Python, with intermediate results evaluated eagerly as lists. We ran the generated Python: `filter(x > 1)` and a block lambda (cap 10 via `if`) over `[1..6]` gives `[4, 6, 8, 10, 10]`, and `mapToInt(...).sum()` gives 21. 18 unit tests pass. **Not yet**: `.sorted(...)` with a comparator, other collectors such as `Collectors.groupingBy`, anonymous classes, method references, body translation on the Rust side (simple `return` only), and real projects such as Spring PetClinic.
## 世界の言語での 2026-11-21 更新(ブロック本体のラムダ・Stream API) / 2026-11-21 update in 10 more languages (AI翻訳+AI確認、人間のネイティブ確認はまだ)

### 简体中文 — 更新 (2026-11-21)

*以下为 AI 翻译,并已由 AI 按母语水平审校;尚未经人类母语者校对。*

现已支持带代码块的 lambda 和常用的 Stream API。多条语句的 lambda(如 `x -> { int d = x * 2; ...; return d; }`)会在所在语句之前生成 `def _lam1(x):`,并在原处使用该名称;`.stream()`、`.map(...)`、`.filter(...)`、`.mapToInt(...)`、`.forEach(...)`、`.anyMatch(...)`、`.allMatch(...)`、`.noneMatch(...)`、`.distinct()`、`.limit(n)`、`.skip(n)`、`.sorted()`、`.sum()`、`.count()`、`.findFirst()`、`.reduce(初始值, 函数)`,以及 `.collect(Collectors.toList())`、`Collectors.toSet()`、`Collectors.joining(分隔符)` 会转换为 Python(中间结果以 list 即时求值)。我们实际运行了生成的 Python:对 `[1..6]` 执行 `filter(x > 1)`,再对每个元素执行含 `if` 的块 lambda(上限 10),得到 `[4, 6, 8, 10, 10]`,`mapToInt(...).sum()` 得到 21;单元测试 18 项通过。**尚未支持**:带比较器的 `.sorted(...)`、`Collectors.groupingBy` 等其他收集器、匿名类、方法引用、Rust 一侧的方法体翻译(目前仅简单 `return`),以及在 Spring PetClinic 等真实项目上的完整验证。上面各语言章节中的“计划”清单已经过时,将由每日翻译更新来修正。

### 繁體中文(台灣) — 更新 (2026-11-21)

*以下為 AI 翻譯,並已由 AI 以母語水準審校;尚未經人類母語者校對。*

現已支援含程式區塊的 lambda 與常用的 Stream API。含多條陳述式的 lambda(如 `x -> { int d = x * 2; ...; return d; }`)會在所在陳述式之前產生 `def _lam1(x):`,並在原處使用該名稱;`.stream()`、`.map(...)`、`.filter(...)`、`.mapToInt(...)`、`.forEach(...)`、`.anyMatch(...)`、`.allMatch(...)`、`.noneMatch(...)`、`.distinct()`、`.limit(n)`、`.skip(n)`、`.sorted()`、`.sum()`、`.count()`、`.findFirst()`、`.reduce(初始值, 函式)`,以及 `.collect(Collectors.toList())`、`Collectors.toSet()`、`Collectors.joining(分隔符號)` 會轉換為 Python(中間結果以 list 立即求值)。我們實際執行了產生的 Python:對 `[1..6]` 執行 `filter(x > 1)`,再對每個元素執行含 `if` 的區塊 lambda(上限 10),得到 `[4, 6, 8, 10, 10]`,`mapToInt(...).sum()` 得到 21;單元測試 18 項通過。**尚未支援**:帶比較器的 `.sorted(...)`、`Collectors.groupingBy` 等其他收集器、匿名類別、方法參照、Rust 端的方法本體翻譯(目前僅簡單 `return`),以及在 Spring PetClinic 等真實專案上的完整驗證。上方各語言章節中的「計畫」清單已經過時,將由每日翻譯更新來修正。

### 한국어 — 업데이트 (2026-11-21)

*아래는 AI가 번역하고 AI가 원어민 수준으로 검수한 것이며, 사람 원어민의 검수는 아직 받지 않았습니다.*

이제 블록 본문을 가진 람다와 자주 쓰는 Stream API를 변환합니다. 문장이 여러 개인 람다(예: `x -> { int d = x * 2; ...; return d; }`)는 해당 문장 바로 앞에 `def _lam1(x):`로 내보내고 원래 자리에서는 그 이름을 씁니다. `.stream()`, `.map(...)`, `.filter(...)`, `.mapToInt(...)`, `.forEach(...)`, `.anyMatch(...)`, `.allMatch(...)`, `.noneMatch(...)`, `.distinct()`, `.limit(n)`, `.skip(n)`, `.sorted()`, `.sum()`, `.count()`, `.findFirst()`, `.reduce(초깃값, 함수)`, 그리고 `.collect(Collectors.toList())`, `Collectors.toSet()`, `Collectors.joining(구분자)`는 Python으로 바뀌며, 중간 결과는 list로 즉시 계산합니다. 생성된 Python을 실제로 실행해 확인했습니다. `[1..6]`에 `filter(x > 1)`을 적용하고 `if`가 있는 블록 람다(상한 10)로 바꾸면 `[4, 6, 8, 10, 10]`이 되고, `mapToInt(...).sum()`은 21이었습니다. 단위 테스트 18건이 통과했습니다. **아직 지원하지 않는 것**: 비교자를 받는 `.sorted(...)`, `Collectors.groupingBy` 같은 다른 수집기, 익명 클래스, 메서드 참조, Rust 쪽 메서드 본문 변환(현재는 단순한 `return`만), 그리고 Spring PetClinic 같은 실제 프로젝트 전체 검증. 위의 다른 언어 절에 있는 "계획" 목록은 지금은 오래된 내용이며, 매일의 번역 갱신에서 고칩니다.

### Deutsch — Aktualisierung (2026-11-21)

*Die folgenden Texte wurden von einer KI übersetzt und von einer KI auf muttersprachliche Natürlichkeit geprüft; eine Prüfung durch menschliche Muttersprachler steht noch aus.*

Jetzt werden Lambdas mit Blockrumpf und die gängigen Stream-API-Aufrufe umgewandelt. Ein Lambda mit mehreren Anweisungen (zum Beispiel `x -> { int d = x * 2; ...; return d; }`) wird als `def _lam1(x):` unmittelbar vor der umgebenden Anweisung ausgegeben und an der ursprünglichen Stelle mit diesem Namen verwendet. `.stream()`, `.map(...)`, `.filter(...)`, `.mapToInt(...)`, `.forEach(...)`, `.anyMatch(...)`, `.allMatch(...)`, `.noneMatch(...)`, `.distinct()`, `.limit(n)`, `.skip(n)`, `.sorted()`, `.sum()`, `.count()`, `.findFirst()`, `.reduce(Startwert, Funktion)` sowie `.collect(Collectors.toList())`, `Collectors.toSet()` und `Collectors.joining(Trenner)` werden nach Python übersetzt; Zwischenergebnisse werden sofort als list ausgewertet. Wir haben das erzeugte Python tatsächlich ausgeführt: `filter(x > 1)` auf `[1..6]`, danach ein Block-Lambda mit `if` (Obergrenze 10), ergibt `[4, 6, 8, 10, 10]`, und `mapToInt(...).sum()` ergibt 21. 18 Unit-Tests bestehen. **Noch nicht unterstützt**: `.sorted(...)` mit Komparator, weitere Collector wie `Collectors.groupingBy`, anonyme Klassen, Methodenreferenzen, die Übersetzung der Methodenrümpfe auf der Rust-Seite (derzeit nur einfaches `return`) und ein vollständiger Durchlauf mit echten Projekten wie Spring PetClinic. Die Listen „Geplant“ in den übrigen Sprachabschnitten oben sind damit veraltet und werden durch die tägliche Übersetzungsaktualisierung korrigiert.

### Français — Mise à jour (2026-11-21)

*Les textes suivants ont été traduits par une IA et relus par une IA pour leur naturel ; ils n'ont pas encore été relus par des locuteurs natifs humains.*

Les lambdas à corps de bloc et les appels courants de l'API Stream sont désormais convertis. Un lambda à plusieurs instructions (par exemple `x -> { int d = x * 2; ...; return d; }`) est émis sous la forme `def _lam1(x):` juste avant l'instruction qui le contient, puis utilisé sous ce nom à l'endroit d'origine. `.stream()`, `.map(...)`, `.filter(...)`, `.mapToInt(...)`, `.forEach(...)`, `.anyMatch(...)`, `.allMatch(...)`, `.noneMatch(...)`, `.distinct()`, `.limit(n)`, `.skip(n)`, `.sorted()`, `.sum()`, `.count()`, `.findFirst()`, `.reduce(valeur initiale, fonction)`, ainsi que `.collect(Collectors.toList())`, `Collectors.toSet()` et `Collectors.joining(séparateur)` sont traduits en Python ; les résultats intermédiaires sont évalués tout de suite sous forme de list. Nous avons réellement exécuté le Python généré : `filter(x > 1)` sur `[1..6]`, puis un lambda à bloc contenant un `if` (plafond 10), donne `[4, 6, 8, 10, 10]`, et `mapToInt(...).sum()` donne 21. 18 tests unitaires réussis. **Pas encore pris en charge** : `.sorted(...)` avec comparateur, d'autres collecteurs comme `Collectors.groupingBy`, les classes anonymes, les références de méthode, la traduction des corps de méthodes côté Rust (seulement un simple `return` pour l'instant) et un essai complet sur de vrais projets comme Spring PetClinic. Les listes « Prévu » des autres sections de langue ci-dessus sont donc périmées ; la mise à jour quotidienne des traductions les corrigera.

### Español — Actualización (2026-11-21)

*Los textos siguientes fueron traducidos por una IA y revisados por una IA en cuanto a naturalidad; aún no los ha revisado un hablante nativo humano.*

Ahora se convierten las lambdas con cuerpo de bloque y las llamadas habituales de la API Stream. Una lambda con varias sentencias (por ejemplo `x -> { int d = x * 2; ...; return d; }`) se emite como `def _lam1(x):` justo antes de la sentencia que la contiene y se usa con ese nombre en el lugar original. `.stream()`, `.map(...)`, `.filter(...)`, `.mapToInt(...)`, `.forEach(...)`, `.anyMatch(...)`, `.allMatch(...)`, `.noneMatch(...)`, `.distinct()`, `.limit(n)`, `.skip(n)`, `.sorted()`, `.sum()`, `.count()`, `.findFirst()`, `.reduce(valor inicial, función)`, además de `.collect(Collectors.toList())`, `Collectors.toSet()` y `Collectors.joining(separador)`, se traducen a Python; los resultados intermedios se evalúan al instante como list. Ejecutamos realmente el Python generado: `filter(x > 1)` sobre `[1..6]` y luego una lambda de bloque con un `if` (tope 10) da `[4, 6, 8, 10, 10]`, y `mapToInt(...).sum()` da 21. Pasan 18 pruebas unitarias. **Aún no admitido**: `.sorted(...)` con comparador, otros colectores como `Collectors.groupingBy`, las clases anónimas, las referencias a métodos, la traducción de los cuerpos de método del lado de Rust (por ahora solo un `return` sencillo) y una prueba completa con proyectos reales como Spring PetClinic. Las listas «Previsto» de las demás secciones de idioma de arriba han quedado desactualizadas; la actualización diaria de traducciones las corregirá.

### Português (BR) — Atualização (2026-11-21)

*Os textos a seguir foram traduzidos por uma IA e revisados por uma IA quanto à naturalidade; ainda não foram revisados por falantes nativos humanos.*

Agora são convertidas as lambdas com corpo em bloco e as chamadas comuns da API Stream. Uma lambda com várias instruções (por exemplo `x -> { int d = x * 2; ...; return d; }`) é emitida como `def _lam1(x):` logo antes da instrução que a contém e usada com esse nome no lugar original. `.stream()`, `.map(...)`, `.filter(...)`, `.mapToInt(...)`, `.forEach(...)`, `.anyMatch(...)`, `.allMatch(...)`, `.noneMatch(...)`, `.distinct()`, `.limit(n)`, `.skip(n)`, `.sorted()`, `.sum()`, `.count()`, `.findFirst()`, `.reduce(valor inicial, função)`, além de `.collect(Collectors.toList())`, `Collectors.toSet()` e `Collectors.joining(separador)`, são traduzidos para Python; os resultados intermediários são avaliados na hora como list. Executamos de fato o Python gerado: `filter(x > 1)` sobre `[1..6]` e depois uma lambda de bloco com um `if` (teto 10) dá `[4, 6, 8, 10, 10]`, e `mapToInt(...).sum()` dá 21. 18 testes unitários passam. **Ainda não suportado**: `.sorted(...)` com comparador, outros coletores como `Collectors.groupingBy`, classes anônimas, referências a métodos, a tradução dos corpos de método do lado do Rust (por enquanto apenas um `return` simples) e um teste completo com projetos reais como o Spring PetClinic. As listas «Planejado» das demais seções de idioma acima estão desatualizadas; a atualização diária das traduções vai corrigi-las.

### Русский — Обновление (2026-11-21)

*Следующие тексты переведены ИИ и проверены ИИ на естественность звучания; носители языка — люди — их пока не проверяли.*

Теперь преобразуются лямбды с блочным телом и самые распространённые вызовы Stream API. Лямбда из нескольких операторов (например, `x -> { int d = x * 2; ...; return d; }`) выводится как `def _lam1(x):` непосредственно перед содержащим её оператором, а на прежнем месте используется это имя. `.stream()`, `.map(...)`, `.filter(...)`, `.mapToInt(...)`, `.forEach(...)`, `.anyMatch(...)`, `.allMatch(...)`, `.noneMatch(...)`, `.distinct()`, `.limit(n)`, `.skip(n)`, `.sorted()`, `.sum()`, `.count()`, `.findFirst()`, `.reduce(начальное значение, функция)`, а также `.collect(Collectors.toList())`, `Collectors.toSet()` и `Collectors.joining(разделитель)` переводятся в Python; промежуточные результаты сразу вычисляются как list. Мы действительно запустили сгенерированный Python: `filter(x > 1)` для `[1..6]`, затем блочная лямбда с `if` (предел 10) даёт `[4, 6, 8, 10, 10]`, а `mapToInt(...).sum()` даёт 21. Проходят 18 модульных тестов. **Пока не поддерживается**: `.sorted(...)` с компаратором, другие коллекторы вроде `Collectors.groupingBy`, анонимные классы, ссылки на методы, перевод тел методов на стороне Rust (пока только простой `return`) и полная проверка на реальных проектах, например Spring PetClinic. Списки «Планируется» в остальных языковых разделах выше устарели; их исправит ежедневное обновление переводов.

### العربية — تحديث (2026-11-21)

<div dir="rtl" lang="ar">

*النص التالي ترجمه الذكاء الاصطناعي وراجعه الذكاء الاصطناعي من حيث سلامة اللغة، ولم يراجعه بعدُ متحدث أصلي من البشر.*

أصبحت تعابير lambda ذات الجسم الكتلي واستدعاءات واجهة Stream الشائعة مدعومة الآن. فتعبير lambda الذي يحوي عدة عبارات (مثل `x -> { int d = x * 2; ...; return d; }`) يُولَّد على هيئة `def _lam1(x):` قبل العبارة التي تحويه مباشرةً، ويُستخدَم هذا الاسم في موضعه الأصلي. وتُترجَم إلى Python كلٌّ من `.stream()` و`.map(...)` و`.filter(...)` و`.mapToInt(...)` و`.forEach(...)` و`.anyMatch(...)` و`.allMatch(...)` و`.noneMatch(...)` و`.distinct()` و`.limit(n)` و`.skip(n)` و`.sorted()` و`.sum()` و`.count()` و`.findFirst()` و`.reduce(القيمة الابتدائية, الدالة)`، إضافةً إلى `.collect(Collectors.toList())` و`Collectors.toSet()` و`Collectors.joining(الفاصل)`؛ وتُقيَّم النتائج الوسيطة فورًا في صورة list. شغّلنا فعليًا شيفرة Python المولَّدة: تطبيق `filter(x > 1)` على `[1..6]` ثم lambda كتلي فيه `if` (الحد الأقصى 10) يعطي `[4, 6, 8, 10, 10]`، و`mapToInt(...).sum()` يعطي 21. نجح 18 اختبارًا من اختبارات الوحدة. **غير مدعوم بعدُ**: `.sorted(...)` مع مقارِن، والمجمِّعات الأخرى مثل `Collectors.groupingBy`، والأصناف المجهولة، والإشارات إلى الدوال، وترجمة أجسام الدوال في جانب Rust (حاليًا `return` البسيط فقط)، والتجربة الكاملة على مشاريع حقيقية مثل Spring PetClinic. وقوائم «المخطَّط له» في أقسام اللغات الأخرى أعلاه صارت قديمة، وسيصحّحها التحديث اليومي للترجمات.

</div>

### فارسی (ایران) — به‌روزرسانی (2026-11-21)

<div dir="rtl" lang="fa">

*متن زیر را هوش مصنوعی ترجمه و از نظر روانی زبان بازبینی کرده است؛ هنوز هیچ فارسی‌زبانِ بومیِ انسانی آن را بررسی نکرده است.*

اکنون لامبداهای دارای بدنهٔ بلوکی و فراخوانی‌های رایج Stream API تبدیل می‌شوند. لامبدایی که چند دستور دارد (مثلاً `x -> { int d = x * 2; ...; return d; }`) به‌صورت `def _lam1(x):` درست پیش از دستورِ دربرگیرنده تولید می‌شود و در جای اصلی با همین نام به کار می‌رود. `.stream()`، `.map(...)`، `.filter(...)`، `.mapToInt(...)`، `.forEach(...)`، `.anyMatch(...)`، `.allMatch(...)`، `.noneMatch(...)`، `.distinct()`، `.limit(n)`، `.skip(n)`، `.sorted()`، `.sum()`، `.count()`، `.findFirst()`، `.reduce(مقدار آغازین, تابع)` و نیز `.collect(Collectors.toList())`، `Collectors.toSet()` و `Collectors.joining(جداکننده)` به Python ترجمه می‌شوند؛ نتیجه‌های میانی بلافاصله به‌صورت list محاسبه می‌شوند. کد Python تولیدشده را واقعاً اجرا کردیم: اعمال `filter(x > 1)` روی `[1..6]` و سپس لامبدای بلوکی دارای `if` (سقف 10) نتیجهٔ `[4, 6, 8, 10, 10]` را می‌دهد و `mapToInt(...).sum()` برابر 21 است. ۱۸ آزمون واحد موفق است. **هنوز پشتیبانی نمی‌شود**: `.sorted(...)` همراه با مقایسه‌گر، گردآورنده‌های دیگر مانند `Collectors.groupingBy`، کلاس‌های ناشناس، ارجاع به متد، ترجمهٔ بدنهٔ متدها در سمت Rust (فعلاً فقط `return` ساده) و آزمایش کامل روی پروژه‌های واقعی مانند Spring PetClinic. فهرست‌های «برنامه‌ریزی‌شده» در بخش‌های زبانی دیگرِ بالا اکنون قدیمی است و به‌روزرسانی روزانهٔ ترجمه‌ها آن را اصلاح خواهد کرد.

</div>
