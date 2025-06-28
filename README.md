<div align="center">
  <h3>Django Scaffold</h3>
  <p>Template na rýchlu výrobu Django aplikácií. ⚡</p>
</div>

## Vytvorenie nového projektu

```shell
uvx copier copy https://github.com/trojsten/django-scaffold.git .
```

A následne si treba vygenerovať lock súbory a nainštalovať dependencies:

```shell
uv sync
pnpm install
```

## Obsah

V novovytvorenom projekte sú predpripravené nasledovné veci:

### Backend

- [Django](https://www.djangoproject.com/)
- [environs](https://pypi.org/project/environs/) pre načítavanie env premenných
- [psycopg 3 (+binary)](https://pypi.org/project/psycopg/) pre pripojenie
  k postgres databáze
- [django-debug-toolbar](https://pypi.org/project/django-debug-toolbar/),
  sidebar s rôznymi debug nástrojmi
- [django-probes](https://pypi.org/project/django-probes/) na skontrolovanie
pripojenia k DB pred štartom
- [django-cleanup](https://pypi.org/project/django-cleanup/) na automatické
  vymazávanie related súborov pri vymazaní objektu
- [django-widget-tweaks](https://pypi.org/project/django-widget-tweaks/) na
  renderovanie formulárov
- [django-tables2](https://pypi.org/project/django-tables2/) na renderovanie tabuliek
- [sentry-sdk](https://pypi.org/project/sentry-sdk/) na posielanie chýb do
  Sentry / Glitchtip serveru
- nakonfigurované logovanie

### Frontend

- [TailwindCSS](https://tailwindcss.com/) a [daisyUI](https://daisyui.com/) na
  frontend štýl
- [htmx](https://htmx.org/)
  (+[django-htmx](https://django-htmx.readthedocs.io/en/latest/))
  pre "AJAX-style" interaktívne elementy
- [Stimulus](https://stimulus.hotwired.dev/) pre funkcionalitu vyžadujúcu JS
- [Iconify Tailwind](https://iconify.design/docs/usage/css/tailwind/tailwind4/)
  na UI ikony

### Deployment a development

- [django-types](https://pypi.org/project/django-types/) pre lepšiu autocomplete
  podporu
- [gunicorn](https://gunicorn.org/) ako produkčný WSGI server
- Docker setup založený na [trojsten/django-docker](https://github.com/trojsten/django-docker)
- [ruff](https://docs.astral.sh/ruff/) na formátovanie Python kódu
- [djade](https://pypi.org/project/djade/) na formátovanie Django template súborov
- [pre-commit](https://pypi.org/project/pre-commit/) pre automatické
  spúšťanie formatterov a iných nástrojoch pri commite
- Github CI workflow na zbehnutie pre-commit hookov v pull requestoch

### Trojsten extras

Tiež je možnosť si pri vytváraní projektu zvoliť aj Trojsten súčasti:

- [mozilla-django-oidc](https://pypi.org/project/mozilla-django-oidc/)
  nakonfigurované pre Trojsten ID prihlásenie
- Trojsten daisyUI téma

## Odporúčania

### Štruktúra aplikácií

Veľa našich projektov používa štruktúru Django projektu, ktorá je trochu
odlišná od tej predvolenej, ktorú Django používa defaultne.
Konkrétne, my dávame všetky aplikácie do priečinka projektu, nie vedľa
neho. Potom napríklad importy z aplikácií sú `project.app.models` miesto
`app.models`, ako je zvykom v štandardných Django projektoch.
Zvyšuje to trochu prehľadnosť projektu a jeho koreňového adresára.

Avšak, vyrábať takéto aplikácie vyžaduje trochu mágie, lebo `startapp`
to nevie úplne automaticky robiť:

```shell
mkdir projekt/app
./manage.py startapp app projekt/app
```

Potom treba v `projekt/app/apps.py` treba upraviť:

- `name` zmeniť na `projekt.app`
- _(optionally)_ zmeniť `label` na `projekt_app`, toto je dobrý nápad, lebo
  občas 3rd party aplikácie majú generické názvy (napr. `rules`), ale aj
  Django builtins (`admin`), ktoré môžu kolidovať s labelmi vlastných aplikácií

### Fat Models, Thin Views

Odporúča sa využívať v projektoch prístup Fat Models, Thin Views, ktorý
hovorí zjednodušene toto:

- Thin Views: Views by mali byť čo najmenšie, riešiť primárne logiku
  spracovania vstupov a výstupov.
  Akcie by mali delegovať, mali by obsahovať čo najmenej biznisovej logiky.
  _Napríklad: view zavolá `user.deactivate()` miesto implementácie logiky (`user.is_active=False`,...)._
- Fat Models: Modely by mali zaobalovať doménovú logiku a integritu dát.
  - Metódy na manipuláciu s dátami modelu (už spomenuté `user.deactivate()`)
  - Metódy na získanie komplexnejších hodnôt (napr. `product.in_stock`)
  - Pre komplexnú aplikačnú logiku, ktorá potrebuje volať externé služby,
    pracovať s viacerými modelmi je vhodné vytvoriť jednoduchý service
    layer (jednoduchá Python funkcia, trieda...)
- Nebáť sa využiť
  [custom Manager](https://docs.djangoproject.com/en/5.2/topics/db/managers/#custom-managers)
  a custom QuerySet.
  - Manager: table-level operácie (operácie na viacerých inštanciách modelu)
  - QuerySet: filtrovanie riadkov, anotácie, zoraďovanie

Doplňujúce čítanie:
[Against service layers in Django](https://www.b-list.org/weblog/2020/mar/16/no-service/),
[More on service layers in Django](https://www.b-list.org/weblog/2020/mar/23/still-no-service/).

### Ďalšie užitočné Django projekty

- [rules](https://pypi.org/project/rules/), object-level permissions na základe predikátov
- [procrastinate](https://procrastinate.readthedocs.io/en/stable/), Postgres
  task queue
- [django-rq](https://github.com/rq/django-rq), Redis task queue
- [django-ipware](https://pypi.org/project/django-ipware/) na spoľahlivé
  zisťovanie IP adresy používateľa
- [mjml-python](https://pypi.org/project/mjml-python/) na formátovanie
  responzívnych emailov (má vstavanú MJML binárku)
- [typst](https://pypi.org/project/typst/) na generovanie PDF dokumentov pomocou
  Typstu
- [markdown](https://pypi.org/project/markdown/) na renderovanie markdownu do HTML
- [django-ninja](https://pypi.org/project/django-ninja/) na implementáciu REST API
- [django-qr-code](https://pypi.org/project/django-qr-code/) na renderovanie QR kódov
- [pillow](https://pillow.readthedocs.io/en/stable/) na manipuláciu s obrázkami
- [pygments](https://pypi.org/project/Pygments/) na syntax highlighting kódu
- [django-phonenumber-field](https://pypi.org/project/django-phonenumber-field/)
  na uchovávanie a validáciu tel. čísiel
- [django-silk](https://pypi.org/project/django-silk/) na logovanie requestov a
  SQL dotazov
- [django-simple-history](https://pypi.org/project/django-simple-history/)
  na uchovávanie histórie zmien modelov
- [Meilisearch](https://www.meilisearch.com/) ak treba sofistikované vyhľadávanie
