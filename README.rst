Introduction à Salt
===================

Install virtualenvwrapper

.. code-block:: bash

    mkvirtualenv saltslides

Installer les packages requis

.. code-block:: bash

    pip install sphinx  # Génération de doc
    pip install hieroglyph # Extension sphinx pour la génération de slide
    pip install rst2pdf # Extension de sphinx pour la génération de pdf
    pip install svg2rlg # Extension permettant à rst2pdf de gérer les svg

Générer les slides

.. code-block::

    make slides


Générer une sortie pdf

.. code-block::

    make pdf
