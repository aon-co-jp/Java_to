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

## 2026-11-22 追記: メソッド参照と Comparator (JA/EN)
**日本語**: メソッド参照(`String::length`・`Integer::parseInt`・`Integer::sum`・`this::m`・`型::getXxx` など)をラムダ/関数へ変換し、`.sorted(Comparator)`(`Comparator.comparing/comparingInt/naturalOrder/reverseOrder` と `.reversed()`、ラムダの比較関数)に対応。生成した Python を実際に実行して結果が一致することを単体試験で確認(19件成功)。**未了**: `thenComparing`、`Collectors.groupingBy`、匿名クラス、Rust 側のメソッド本体、Spring PetClinic での通し試験。
**English**: Method references (`String::length`, `Integer::parseInt`, `Integer::sum`, `this::m`, `Type::getXxx`, ...) now become lambdas/functions, and `.sorted(Comparator)` works (`Comparator.comparing/comparingInt/naturalOrder/reverseOrder`, `.reversed()`, lambda comparators). The unit test runs the generated Python and checks the result (19 tests pass). **Open**: `thenComparing`, `Collectors.groupingBy`, anonymous classes, Rust-side method bodies, an end-to-end run on Spring PetClinic.

## 2026-11-22 追記: groupingBy と thenComparing (JA/EN)
**日本語**: `Collectors.groupingBy(f)`(値は一覧)と `groupingBy(f, Collectors.counting())`(件数)、`Comparator.thenComparing/thenComparingInt` を Python へ変換。生成した Python を単体試験で実行し結果の一致を確認(20件成功)。**未了**: `groupingBy` の他の下流コレクター(`mapping`・`summingInt` 等)、`toMap`、匿名クラス、Rust 側のメソッド本体、Spring PetClinic での通し試験。
**English**: `Collectors.groupingBy(f)` (lists), `groupingBy(f, Collectors.counting())` (counts) and `Comparator.thenComparing/thenComparingInt` now convert to Python; the unit test runs the generated code and checks the result (20 tests pass). **Open**: other downstream collectors (`mapping`, `summingInt`, ...), `toMap`, anonymous classes, Rust-side method bodies, an end-to-end run on Spring PetClinic.

## 2026-11-22 追記: toMap と groupingBy の下流コレクター (JA/EN)
**日本語**: `Collectors.toMap(k, v)`、`groupingBy` の下流 `counting`/`summingInt`(Long・Double)/`mapping(f, toList()|toSet())` を Python へ変換。生成した Python を単体試験で実行して結果の一致を確認(21件成功)。括弧の数え間違い(ラムダ内の `)` を削っていた)も直した。**未了**: `toMap` の重複キー結合、`averagingInt`、`partitioningBy`、匿名クラス、Rust 側のメソッド本体、Spring PetClinic での通し試験。
**English**: `Collectors.toMap(k, v)` and the `groupingBy` downstream collectors `counting`, `summingInt` (Long/Double) and `mapping(f, toList()|toSet())` now convert to Python; the unit test runs the generated code and checks the result (21 tests pass). Also fixed a paren-stripping bug that ate the `)` of a lambda. **Open**: `toMap` merge function, `averagingInt`, `partitioningBy`, anonymous classes, Rust-side method bodies, an end-to-end run on Spring PetClinic.


## 2026-11-22 現状と完成予定と課題 / Status, plan and issues

### 日本語
**現状**: Java(Spring/JAX-RS/Servlet/Spark)を Python(FastAPI/MFastAPI)と Rust(Poem/RPoem)へ変換。ラムダ(式・ブロック)、メソッド参照、Stream API(map/filter/sorted(Comparator)/reduce/collect)、Comparator(comparing/thenComparing/reversed)、Collectors(toList/toSet/joining/toMap/groupingBy と counting・summingInt・mapping)に対応。生成した Python を単体試験で実行して結果を確認(21件成功)。
**完成予定**: toMap の重複キー結合、averagingInt、partitioningBy、匿名クラス、Rust 側のメソッド本体(今は単純な return のみ)、Spring PetClinic など実プロジェクトでの通し試験。
**課題**: 中間結果を list で即時評価するため、無限ストリームや遅延評価に依存するコードは対象外。Rust 側は骨組み中心。実プロジェクトでの検証が未了。

### English
**Status**: Converts Java (Spring/JAX-RS/Servlet/Spark) to Python (FastAPI/MFastAPI) and Rust (Poem/RPoem). Supports lambdas (expression and block), method references, the Stream API (map/filter/sorted(Comparator)/reduce/collect), Comparator (comparing/thenComparing/reversed) and Collectors (toList/toSet/joining/toMap/groupingBy with counting, summingInt, mapping). The unit tests run the generated Python and check results (21 tests pass).
**Planned**: toMap merge function, averagingInt, partitioningBy, anonymous classes, Rust-side method bodies (currently a simple return only), end-to-end runs on real projects such as Spring PetClinic.
**Issues**: Intermediate results are evaluated eagerly as lists, so infinite or lazy streams are out of scope. The Rust side is mostly a scaffold. Real-project verification is not done yet.


## 2026-11-22 更新: Collectors 対応の完了と残課題 / Update: Collectors done, what remains

### 日本語
**現状**: Collectors の対応が揃いました: `toList`/`toSet`/`joining`/`toMap`(重複キーの結合関数つき)/`groupingBy`(下流 `counting`・`summingInt`・`mapping`)/`partitioningBy`(`counting` つき)/`averagingInt`。メソッド参照、`Comparator`(`comparing`/`thenComparing`/`reversed`)も対応済み。生成した Python を単体試験で実際に動かして結果を確認(Java_to 23件・pl2pl 55件成功)。
**未了(残る課題)**: 匿名クラス、Rust 側のメソッド本体(今は単純な return のみ)、Spring PetClinic など実プロジェクトでの通し試験、`groupingBy` のその他の下流コレクター(`summingDouble` 以外の `reducing` など)。
**課題**: 中間結果を list で即時評価するため、無限・遅延ストリームは対象外。実プロジェクトでの検証が未了。

### English
**Status**: Collectors coverage is now complete for the common cases: `toList`/`toSet`/`joining`/`toMap` (with a merge function)/`groupingBy` (downstream `counting`, `summingInt`, `mapping`)/`partitioningBy` (with `counting`)/`averagingInt`. Method references and `Comparator` (`comparing`/`thenComparing`/`reversed`) are supported too. Unit tests run the generated Python and check results (Java_to 23, pl2pl 55 pass).
**Still open**: anonymous classes, Rust-side method bodies (currently a simple return only), end-to-end runs on real projects such as Spring PetClinic, and other `groupingBy` downstream collectors (`reducing`, etc.).
**Issues**: intermediate results are evaluated eagerly as lists, so infinite or lazy streams are out of scope; real-project verification is not done.

## 2026-11-22 追記: 匿名クラス (JA/EN)
**日本語**: `new Comparator<T>() { public int compare(...) {...} }` のような匿名クラスを、メソッドが1つなら Python の関数 `_anonN` として直前に出し、その名前に置き換える(関数型インターフェースの代用)。生成した Python を実行して並び替えの結果が一致することを単体試験で確認(24件成功)。メソッドが複数ある場合は最初の1つだけ関数にし、注記を残す。**未了**: 状態(フィールド)を持つ匿名クラス、Rust 側のメソッド本体、Spring PetClinic などでの通し試験。
**English**: Anonymous classes such as `new Comparator<T>() { public int compare(...) {...} }` with a single method are emitted just before as a Python function `_anonN` and replaced by its name (standing in for a functional interface). The unit test runs the generated Python and checks the sort result (24 tests pass). With several methods only the first becomes a function, with a note. **Open**: anonymous classes that hold state (fields), Rust-side method bodies, end-to-end runs on Spring PetClinic and similar.


## 2026-11-22 追記: Rust 側のメソッド本体の変換 / Rust-side method bodies (日本語・English)

### 日本語
**できたこと**: これまで Rust(Poem / RPoem)側は「単純な `return` 1つ」だけ移していましたが、新しい `src/rsblock.rs` で、**ローカル変数・代入(`+=` など)・`if / else`・`for (int i = a; i < b; i++)`・`while`・複数の `return`** も Rust にできるようになりました。文字列の `+` は `format!` になります。対応外の文が1つでもあれば、これまでどおり TODO の応答に戻します(壊れたコードは出しません)。確認: 単体テスト27件、`tests/run-rust.sh`(Poem 4種)と `tests/run-rpoem.sh` で、生成した Rust が `cargo check` を通ること(サンプルに `total` を追加)。pl2pl の `converters/java_to` にも同期しました。
**まだ**: メソッド呼び出しやコレクション操作を含む本体、状態を持つ無名クラス、`groupingBy` の他の下流コレクター(`reducing` など)、Spring PetClinic など実プロジェクトでの通し確認。
**注意**: 同名メソッドがあると本体を最初の1件で探します(サンプルでは `total` と別名にした)。

### English
**Done**: the Rust (Poem / RPoem) side used to carry only a single simple `return`. The new `src/rsblock.rs` also converts **local variables, assignments (`+=` etc.), `if / else`, `for (int i = a; i < b; i++)`, `while` and multiple `return`s**; string `+` becomes `format!`. If any statement is unsupported, it falls back to the TODO response as before (it never emits broken code). Verified: 27 unit tests, and the generated Rust passes `cargo check` under `tests/run-rust.sh` (four Poem samples) and `tests/run-rpoem.sh` (a `total` method was added to the sample). Synced to pl2pl `converters/java_to`.
**Still open**: bodies with method calls or collection operations, anonymous classes with state, other `groupingBy` downstream collectors (`reducing`, etc.), and end-to-end runs on real projects such as Spring PetClinic.
**Caveat**: methods are looked up by name and the first match wins (so the sample method is called `total`, not `sum`).


## 2026-11-22 追記2: 未了の仕上げ(日本語)
- **`groupingBy` の下流コレクター**: `averagingInt/Long/Double`・`joining`・`toSet`・`reducing`(2引数、3引数)に対応しました。
- **複数引数のラムダ**: `(x, y) -> x + y` のような丸かっこ付きラムダを、これまでの「引数1つだけ」から広げました(`reducing` の確認で見つかった不足)。
- **状態を持つ無名クラス**: フィールド付きの `new I() { int n = 0; ... }` を、`__init__` でフィールドを初期化する Python クラス(`_AnonN`)として出します。最初のメソッドは `__call__` でも呼べます。
- **Rust 側の本体**: `Math.max/min/abs/pow/sqrt`、`String.valueOf`、文字列の `length/size/toUpperCase/toLowerCase/trim/isEmpty/contains/startsWith/endsWith/equals` に対応しました。対応外の呼び出しがあれば、これまでどおり TODO の応答に戻します。
- **確認**: 単体テスト30件(生成した Python を実行して結果を確認)。`tests/run-rust.sh`・`tests/run-rpoem.sh` で生成 Rust のコンパイル確認。pl2pl の `converters/java_to` にも同期しました。
- **まだ**: コレクション操作を含む Rust 本体、Spring PetClinic など実プロジェクトでの通し確認、複数メソッドを持つ無名クラスの全メソッド対応(いまは最初の1つだけ)。

## 2026-11-23 追記: 複数メソッドを持つ無名クラス(日本語)
- 無名クラスにメソッドが複数あるとき、これまでは最初の1つだけを出していましたが、**すべてのメソッドを Python クラスのメソッドとして出す**ようにしました(フィールドは各メソッドから見えます)。最初のメソッドは従来どおり `__call__` でも呼べます。
- 単体テストは31件成功(`anonymous_class_multiple_methods`)。生成物の文字列確認までで、Python 実行の通し確認は未了です。
- 未了: コレクション操作を含む Rust 本体、Spring PetClinic など実プロジェクトでの通し確認。

## 2026-11-23 追記: Rust 本体の List 操作(日本語)
- Rust(Poem / RPoem)側のメソッド本体で、`List<Integer> xs = new ArrayList<>();`(`Vec<i32>` になる。`Integer` などの箱型は基本型に直す)、`xs.add(x);`(`push`)、`xs.get(i)`(添字アクセス)、拡張 for `for (int x : xs)` を変換できるようにしました。`xs.size()` は従来どおり `len()` です。
- 単体テストは32件成功(`list_operations`、生成された Rust の文字列確認)。生成 Rust のコンパイル確認(`tests/run-rust.sh`)はこの追加分では未実施です。
- 未了: Map や Stream を含む Rust 本体、Spring PetClinic など実プロジェクトでの通し確認。

## 2026-11-23 追記: List 操作の生成 Rust をコンパイル確認(日本語)
- 前回の List 操作(宣言・`add`・`get`・拡張 for)を、Spring サンプルに `/squares/{n}` を足して実際に変換し、WSL で `tests/run-rust.sh` を実行しました。生成された Rust(`Vec<i32>`、`push`、`for x in xs.clone()`、`xs.len()`)は、spring・jaxrs・servlet・spark の4つとも `cargo check` に通りました。
- 実行して値を確かめるところまでは未了です。未了: Map や Stream を含む Rust 本体、Spring PetClinic など実プロジェクトでの通し確認。

- 2026-11-23 追記: Rust 側のメソッド本体で三項演算子 `c ? a : b` を `(if c { a } else { b })` へ移すようにしました(試験33件成功)。実プロジェクトでの通し試験・`reducing` などの下流コレクターは未了です。

- 2026-11-23 update: in Rust-side method bodies, the ternary `c ? a : b` is now ported to `(if c { a } else { b })` (33 tests pass). End-to-end runs on real projects and downstream collectors such as `reducing` are still open.

## 2026-11-23 追記: OpenAPI 先行のプロジェクトに対応(Spring PetClinic REST で確認・日本語)
- **きっかけ(実プロジェクトでの通し確認)**: GitHub の `spring-petclinic/spring-petclinic-rest`(Java 109ファイル)を変換にかけたところ、ルートが **1本しか見つかりませんでした**。このプロジェクトは `openapi.yml` から生成した `XxxApi` インターフェースを `implements` する方式で、コントローラーに `@GetMapping` などが無いためです。
- **対応**: `openapi.yml`(`openapi.yaml`・`swagger.yml`・`swagger.yaml` も)の `paths:` から (operationId・動詞・経路) を読み、`implements XxxApi` のコントローラーでメソッド名が operationId と一致するものをルートにします。引数は、経路の `{名前}` に当たれば Path、単純な型なら Query、それ以外は Body としました。
- **結果**: ルートが **1本 → 37本**(openapi.yml の operationId 37件と一致)になりました。単体テスト34件成功(`openapi_first_routes`)。`tests/run-pc.sh <プロジェクトのパス>` で実プロジェクトを変換できます。
- **まだ**: 本体(`ResponseEntity<...Dto>` を返す部分や MapStruct のマッパー)は変換していません。サーバー URL の接頭辞(`/api` など)、生成した FastAPI/Rust の実行確認、`/v2` 系のページング型は未対応です。

## 2026-11-23 追記: PetClinic の生成 Python を構文エラー0に(日本語)
- 実プロジェクト(Spring PetClinic REST)の変換結果を `python -m py_compile` にかけたところ、2種類の構文エラーが出たため直しました。
  - **クラスリテラル** `Foo.class`(82か所)が Python に無い `.class` のまま出ていたので、`Foo` に変換します。
  - **`e.getClass().getSimpleName()`** が getter の規則で `e.class.simpleName` になっていたので、`type(e).__name__` にします(`getName` も同様)。
- 結果: 生成された Python 7ファイルすべてが構文エラー0になりました。単体テスト36件成功(`class_literal`、`get_class_name`)。`tests/check-py2.sh` で構文確認、`tests/check-py.sh` で TODO の数も見られます。
- **まだ**: 生成物の実行確認(FastAPI を起動して呼ぶ)は未実施です。`services.py` に TODO が108件残っています(JDBC・MapStruct・`ResponseEntity` などの本体)。

## 2026-11-23 追記: PetClinic の TODO の内訳(日本語)
- `tests/todo-top.sh` で生成 Python の TODO を種類ごとに数えました。残りの TODO の大半は、**インターフェースのメソッドの実装**です。`XxxMapper`(MapStruct が実行時に作るもの)と `XxxRepository`(Spring Data / JPA が実行時に作るもの)で、Java のソースには本体がありません。ほかは導出クエリ(`findByLastName` など)と未対応の式が数件です。
- これは「黙って落とさず、TODO を `NotImplementedError` で残す」という既存の方針どおりの出力です。自動で埋めるには、Spring Data のメソッド名からの SQL 生成(導出クエリ)と、MapStruct のフィールド対応からの変換関数の生成が必要で、設計の判断を伴うため、次の課題にしました。
- 未了: 導出クエリの SQL 生成、MapStruct マッパーの生成、生成した FastAPI の起動確認。

## 2026-11-23 追記: 導出クエリの対応を拡大(日本語)
- **調査(英語・日本語のWeb検索とGitHub)**: Spring Data の導出クエリの規則は公式の「Repository query keywords」が正本です(`find…By` の `By` から条件が始まる、`And`/`Or`、`Like`・`Containing`・`StartingWith`・`EndingWith`、`GreaterThan`・`LessThan`・`Between`・`In`・`IsNull`、`OrderBy…Asc/Desc` など)。メソッド名から条件を作る実装は Spring Data Commons の `PartTree` ですが、Python へ移せる既存ライブラリは見つかりませんでした(日本語の解説も公式の英語文書が中心)。
- **実装**: 生成コードの実行時部品 `java_rt.py` の `InMemoryRepository._derived` を拡張しました。`_or_`、`Containing`/`NotContaining`、`Like`/`NotLike`(`%`・`_`)、`StartingWith`/`EndingWith`、`GreaterThan`/`LessThan`(と `Equal` 付き)、`Between`、`In`/`NotIn`、`IsNull`/`IsNotNull`、`Not`、`True`/`False`、`IgnoreCase`、`OrderBy…Asc/Desc` に対応します。
- **確認**: 単体テスト37件成功(`derived_queries_in_memory_repo`、実際に Python でメモリ上のリポジトリに問い合わせ、並びと件数を検証)。
- **まだ**: メモリ上の足場であり、実 DB(SQL 生成)ではありません。`Top`/`First` の件数制限、`Distinct`、入れ子のプロパティ(`AddressCity`)、MapStruct マッパーの生成は未対応です。

## 2026-11-23 追記: PetClinic の生成アプリが起動(import)するようになった(日本語)
- 生成した FastAPI アプリ(`main.py`)を実際に `import` して起動を試したところ、実行時の不具合が2つ見つかったので直しました。
  - `LoggerFactory.getLogger(...)` が未定義だったため、実行時部品 `java_rt.py` に SLF4J 相当の `LoggerFactory`(`info/warn/error/debug/trace`、`{}` の置換つき)を追加。
  - `private static final Logger logger = LoggerFactory.getLogger(X.class)` のように、**自分のクラス名を使う static の初期値**がクラス本体の中で `NameError` になっていたため、クラス定義の後ろで `X.logger = ...` と代入する形にしました。
- 結果: PetClinic の変換結果が `import ok`(ルート41本、うち37本が API)になりました。
- **実際に呼ぶと500のまま**です。例: `GET /pettypes` は `PetTypeRestControllerV1 object has no attribute clinicService`。コンストラクター注入(`ClinicService` やマッパー)の配線が、この形(Lombok や明示コンストラクター)ではまだ生成されていません。リポジトリ・マッパーのインターフェースも TODO のままです。
- 未了: コンストラクター注入の自動配線、MapStruct マッパーの生成、実 DB。

## 2026-11-23 追記: `this.フィールド` の名前を揃えた(PetClinic の呼び出しが先へ進む・日本語)
- 生成した PetClinic アプリを呼ぶと `object has no attribute clinicService` で落ちていた原因は、本体の `this.clinicService` が **camelCase のまま** `self.clinicService` と出る一方、フィールドの宣言は `self.clinic_service`(snake_case)だったことでした。`this.xxx` は snake_case にそろえるよう直しました(単体テスト38件成功、`this_field_is_snake_case`)。
- 結果: `GET /pettypes`・`/owners`・`/vets` の処理が **コントローラー → サービス → リポジトリ** まで進むようになりました。いまはリポジトリのインターフェース(`PetTypeRepository.findAll` など)の `NotImplementedError`(TODO)で止まります。
- **まだ**: リポジトリのインターフェースに実装クラス(Jdbc・JPA・Spring Data JPA の3種類がある)が複数ある場合の選び方と、メモリ上の足場(`InMemoryRepository`)への割り当て、MapStruct マッパーの生成は未対応です。

## 2026-11-25 現況・未了 / Status and open items

### 日本語

現状: Java(2000年頃〜2026年)のフレームワークを自動判定し、FastAPI・MFastAPI・Poem・RPoem のいずれかへ変換する道具。最近は PetClinic 向けに ResponseEntity・HttpHeaders・UriComponentsBuilder・MapStruct の Mapper・親データクラスの継承に対応し、導出クエリをメモリ上のリポジトリで動かせるようにした。残り: Mac・iPhone の実機での確認(ここには実機がありません)。翻訳: AI 翻訳(ja/en 以外)、ネイティブの確認はまだです。

### English

Status: a tool that auto-detects the Java framework (c. 2000 to 2026) and converts to FastAPI, MFastAPI, Poem or RPoem. Recently, for PetClinic, it gained ResponseEntity, HttpHeaders, UriComponentsBuilder, MapStruct mappers and parent data class inheritance, and derived queries now run on an in-memory repository. Open: checks on a real Mac and iPhone (no such devices here). Translations: AI-translated (other than ja/en), not yet checked by native speakers.

## 2026-11-25 追記: 実装が複数あるときの選び方を試験で固定 / Pinned implementation choice with a test

**日本語**: リポジトリのインターフェースに実装が複数あるとき、メモリ上で動くもの(名前に Jdbc・Jpa を含まない)を優先し、無ければ先頭を選ぶ処理は実装済みでした(以前の「未対応」の記述は古い)。単体テスト `multiple_impls_prefer_non_db_one` で固定しました(Java_to 40件成功)。未了: MapStruct の複雑なマッパー、実 DB での起動確認。

**English**: When a repository interface has several implementations, the generator already prefers one without Jdbc/Jpa in its name and otherwise takes the first (the earlier "unsupported" note was stale). It is now pinned by the unit test `multiple_impls_prefer_non_db_one` (Java_to: 40 pass). Open: complex MapStruct mappers and starting against a real DB.
