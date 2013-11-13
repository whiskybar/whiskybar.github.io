---
layout: page
title: "Salt: extending included SLS"
description: ""
permalink: salt-extending-sls
---
{% include JB/setup %}

We can use a SLS in another SLS using *include*. We can also modify that 
included SLS using *extend*. How do you decide when you want to use *extend*? 
I believe I have finally found that fineline.

##Growing complexity

We have a basic SLS for the machine with plain Apache in `apache/init.sls`:

    apache2:
      pkg:
        - installed
        - pkgs:
          - apache2
          - apache2-mpm-prefork
          ...
      service:
        - running
        - require:
          - pkg: apache2
        - watch:
          - file: /etc/apache2/conf.d/default
          - file: /etc/apache2/conf.d/charset
          ...

    /etc/apache2/conf.d/default:
      file.managed:
        - source: salt://apache/etc/apache2/conf.d/default
    
    /etc/apache2/conf.d/charset:
      file.managed:
        - source: salt://apache/etc/apache2/conf.d/charset

    ...


Then, we need to add PHP for some machines and we also want to keep the 
configuration from `apache/init.sls`, in `apache/php.sls`:

    include:
      - apache
    
    php5:
      pkg:
        - installed
        - pkgs:
          - libapache2-mod-php5
    
    /etc/apache2/conf.d/php:
      file.managed:
        - source: salt://apache/etc/apache2/conf.d/php
        - template: jinja
        - require:
          - pkg: php5
        - watch_in:
          - service: apache2

    /etc/php5/apache2/php.ini:
      file.managed:
        - source: salt://apache/etc/php5/apache2/php.ini
        - template: jinja
        - require:
          - pkg: php5
        - watch_in:
          - service: apache2

This works. However the following stanza always repeats:

    ...
        - require:
          - pkg: php5
        - watch_in:
          - service: apache2

The configuration files in `/etc/apache2` need four lines in `php.sls` but 
they need one line in `init.sls`, while they achieve the same thing 
essentially:

    # service:
    # ...
    #   - watch:
        - file: /etc/apache2/conf.d/php

The difference is this dependency from `php.sls` would need to become part of 
`apache2: service` from `init.sls` and that is where *extending* comes to play.

##Simplified

Using the guide
[Extending Included SLS Data](http://docs.saltstack.com/topics/tutorials/starting_states.html#extending-included-sls-data)
we find we can redefine `php.sls`:

    php5:
      pkg:
        - installed
        - pkgs:
          - libapache2-mod-php5
    
    extend:
      apache2:
        service:
          - require:
            - pkg: php5
          - watch:
            - file: /etc/apache2/conf.d/php
    
    /etc/apache2/conf.d/php:
      file.managed:
        - source: salt://apache/etc/apache2/conf.d/php
        - template: jinja

In this definition, the dependency `/etc/apache2/conf.d/php` becomes part of 
the service definition naturally.

**Extending SLS allows adding requirements to an existing state. This avoids 
copying long blocks or reverting the logic with `require_in` or `watch_in`.**

**Many `watch_in` or `require_in` is a sign to consider extending SLS.**
