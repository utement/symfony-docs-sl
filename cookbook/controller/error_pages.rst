.. index::
   single: Controller; Customize error pages
   single: Error pages

Kako prilagoditi strani z napako
================================

Ko je vržena katerakoli izjema v Symfony2, je izjema ujeta znotraj
razreda ``Kernel`` in eventuelno posredovana posebnemu krmilniku,
``TwigBundle:Exception:show`` za ravnanje. Ta krmilnik, ki domuje
znotraj jedra ``TwigBundle``, ugotovi, katero predlogo napake prikazati in
statusno kodo, ki bi morala biti nastavljena za dano izjemo.

Strani z napako se lahko naredi po meri na dva načina, odvisno od tega
koliko kontrole potrebujete:

1. Naredite po meri predlogo napake za različne strani z napako (razloženo spodaj);

2. Zamenjajte privzeti krmilnik izjem ``twig.controller.exception:showAction``
   z vašim lastnim krmilnikom in z njim ravnajte kakor želite (glejte
   :ref:`exception_controller v Twig referenci <config-twig-exception-controller>`).
   Privzeti krmilnik izjem je registriran kot storitev - dejanski razred
   je ``Symfony\Bundle\TwigBundle\Controller\ExceptionController``.

.. tip::

    Prilagajanje ravnanja izjem je dejansko veliko bolj močnejše, kot
    je opisano tu. Notranji dogodek ``kernel.exception`` je vržen,
    kar dovoljuje celotno kontrolo nad ravnanjem izjeme. Za več informacij
    glejte :ref:`kernel-kernel.exception`.

Vse predloge z napakami domujejo znotraj ``TwigBundle``. Za prepis predlog
se enostavno zanašajte na standardno metodo za prepis predlog, ki domuje
znotraj paketa. Za več informacij, glejte
:ref:`overriding-bundle-templates`.

Na primer, za prepis privzete predloge napak, ki je prikazana končnemu
uporabniku, izdelajte novo predlogo locirano v
``app/Resources/TwigBundle/views/Exception/error.html.twig``:

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <title>An Error Occurred: {{ status_text }}</title>
    </head>
    <body>
        <h1>Oops! An Error Occurred</h1>
        <h2>The server returned a "{{ status_code }} {{ status_text }}".</h2>
    </body>
    </html>

.. caution::

    **Ne smete** uporabiti ``is_granted`` v vaših straneh z napakami (ali
    postavitvi uporabljeni s strani vaši strani z napakami), ker se usmerjevalnik
    zaganja pred požarnim zidom. Če usmerjevalnik vrže izjemo (na primer, ko se smer
    ne ujema), potem bo uporaba ``is_granted`` vrgla naslednjo izjemo. Lahko
    uporabite ``is_granted`` varno z ``{% if app.user and is_granted('...') %}``.

.. tip::

    Če niste seznanjeni s Twig-om, ne skrbite. Twig je enostaven, močan
    in opcijski motor predlog, ki se integrira s Symfony2. Za več informacij
    o Twig-u glejte :doc:`/book/templating`.

Kot dodatek standardnim HTML stranmi z napakami, Symfony ponuja privzeto stran
z napako za mnoge najbolj pogoste oblike odzivov, vključujoč JSON
(``error.json.twig``), XML (``error.xml.twig``) in celo JavaScript
(``error.js.twig``), da jih imenujemo samo nekaj. Za prepis katerekoli od teh predlog, samo
izdelajte novo datoteko z enakim imenom v
``app/Resources/TwigBundle/views/Exception`` direktoriju. To je standardni
način prepisa katerekoli predloge, ki domuje znotraj paketa.

.. _cookbook-error-pages-by-status-code:

Prilagoditev 404 strani in ostalih strani z napako
--------------------------------------------------

Lahko tudi prilagodite določeno predlogo za napake, glede na kodo HTTP statusa.
Na primer izdelajte
``app/Resources/TwigBundle/views/Exception/error404.html.twig`` predlogo za
prikazovanje posebne strani za 404 (stran ni najdena) napake.

Symfony uporablja sledeče algoritme za ugotovitev, katero predlogo naj uporabi:

* Najprej pogleda za predlogo za dani format in statusno kodo (kot je
  ``error404.json.twig``);

* Če ne obstaja, potem pogleda za predlogo za dani format (kot je
  ``error.json.twig``);

* Če ne obstaja, se vrne nazaj k HTML predlogi (kot je
  ``error.html.twig``).

.. tip::

    Da vidite celotni seznam privzetih predlog za napake, glejte
    ``Resources/views/Exception`` direktorij paketa ``TwigBundle``. V
    standardni Symfony2 namestitvi, se ``TwigBundle`` lahko najde v
    ``vendor/symfony/symfony/src/Symfony/Bundle/TwigBundle``. Pogosto je
    najenostavnejši način za prilagoditev strani za napake kopiranje iz
    ``TwigBundle`` v ``app/Resources/TwigBundle/views/Exception`` in nato
    njeno spreminjanje.

.. note::

    Razhroščevanju prijazne strani izjem prikazane razvijalcu so tudi lahko
    prilagojene na enak način z izdelavo predlog kot je
    ``exception.html.twig`` za standardne HTML strani izjem ali
    ``exception.json.twig`` za JSON strani izjem.
