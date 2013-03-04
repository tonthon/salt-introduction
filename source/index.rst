.. Gérer son infra avec Salt documentation master file, created by
   sphinx-quickstart on Tue Feb 26 10:31:01 2013.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Salt
----

.. image:: _static/salt.png
    :align: center


Gérer son infra avec Salt
-------------------------

* Introduction
* Bootstrapper un projet
* Cibler à l'aide de grains
* Ordonner des états
* Servir des fichiers

Que faire avec Salt ?
---------------------

* Gérer la configuration de l'ensemble de son infrastructure de manière unifiée
* Éxécuter des commandes à distance sur des ensembles de serveurs

Le projet
---------

* Projet initié en 2011 par Thomas S Hatch
* En 2012 Salt est le 8ème projet avec le plus de contributeur unique sur github
* Une ML très active
* Actuellement en version 0.14

Composants
----------

* ZeroMQ
* msgpack : minimalisation du trafic
* Authentification par clés RSA
* Cryptage par AES
* Templating (jinja par défaut)

Les forces de salt
------------------

* La simplicité architecturale
* La simplicité de sa syntaxe
* La granularité
* L'imbrication d'états
* Les performances
* C'est du Python :)

Une architecture simple
-----------------------

De base, 1 seul étage
    * Le service salt-master donne des ordres
    * Les services salt-minion les éxécutent

.. image:: _static/1floor.svg

Une architecture évolutive
--------------------------

Salt-syndic permet de rajouter des étages

.. image:: _static/saltsyndic.svg

Mise en place du master
-----------------------

.. code-block:: bash

    wget https://github.com/saltstack/salt-bootstrap/develop/bootstrap-salt.sh
    chmod +x bootstrap-salt.sh
    ./bootstrap-salt.sh -M

Mise en place du minion
-----------------------

.. code-block:: bash

    wget https://github.com/saltstack/salt-bootstrap/develop/bootstrap-salt.sh
    chmod +x bootstrap-salt.sh
    ./bootstrap-salt.sh
    echo "<ip du master> salt" > /etc/hosts

* Enregistrement du minion sur le master

.. code-block:: bash

    salt-key -A # Ajoute toutes les clés en attente

On peut désormais jouer
-----------------------

Les states.modules fournissent un panel de commandes éxécutables

.. code-block:: bash

    salt '*' test.ping
    salt '*' pkg.list_upgrades
    mon-server-minion:
            ----------
            base-files:
                6.0squeeze7
            dbus:
                1.2.24-4+squeeze2
            ...

Bien viser, le rôle des grains
------------------------------

* Les minions fournissent par défaut des variables d'environnement les 'grains'

.. code-block:: python

    salt '*' grains.items
    biosreleasedate: 01/01/2007
    biosversion: Bochs
    cpu_flags: fpu de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pse36
    clflush mmx fxsr sse sse2 syscall nx lm up rep_good pni cx16 popcnt
    hypervisor lahf_lm
    cpu_model: QEMU Virtual CPU version 1.1.2
    cpuarch: x86_64
    defaultencoding: None
    ...

Rajouter des grains
-------------------

* Des grains personnalisés peuvent être:

  * Ajoutés depuis le fichier de config du minion
  * Envoyés depuis le master vers les minions par le biais de fichiers
  * Définis en ligne de commande depuis le master `salt 'target' grains.setval
    key val`

* Voir aussi : Pillar (des grains dynamiques et confidentiels)

Les états
---------

* Les fichiers d'état sont des templates jinja ayant pour destination le format
  yaml
* Ils sont stockés par défaut dans /srv/salt
* Un état correspond à une exigence : "Installer le paquet nginx"
* Il peut dépendre d'autre état : "Nginx doit être lancé" requiert "Installation
  du paquet Nginx"

Exemple d'état avec utilisation de grain
----------------------------------------

.. code-block:: yaml

    {% if grains['hosts'] == 'monserveurweb.org' %}
    nginx:
        pkg.installed:
            - nginx
        service.runing:
            - name: nginx
            - require:
              - pkg: nginx
    {% endif %}

Le fileserver
-------------

* Un serveur de fichier en ZeroMQ intégré au service salt-master
* Permet de fournir des fichiers au gestionnaire d'états
* Les fichiers sont des templates
* Par défaut, les fichiers sont placés dans la même arborescence que les états

.. code-block:: yaml

    mysql_conf:
        file.managed:
            - name: /etc/mysql/my.cnf
            - source: salt://etc/mysql/my.cnf
            - require:
              - pkg: mysql

Ce qu'on voudra voir par la suite
---------------------------------

* Écrire ses propres modules
* Écrire ses propres états
* Pillar : grains confidentiels et dynamiques
* Salt-cloud : s'interfacer avec des environnements Cloud (Amazon AWS, RackSpace
  ...)

Merci
-----

Des questions ?
