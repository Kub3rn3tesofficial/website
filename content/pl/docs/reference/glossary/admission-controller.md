---
title: Kontroler wejściowy
id: admission-controller
date: 2019-06-28
full_link: /docs/reference/access-authn-authz/admission-controllers/
short_description: >
  Część kodu który przejmuje przychodzące zapytania do API Kubernetesa
  zanim zostaną one zachowane.

aka:
tags:
- extension
- security
---
Część kodu który przejmuje przychodzące zapytania do API Kubernetesa
zanim zostaną one zachowane.

<!--more-->

Kontrolery wejściowe są konfigurowane w otoczeniu API Kubernetesa i ich celem jest sprawdzanie lub modyfikacja (albo jedno i drugie) obiektów przychodzących w żądaniach.

Kontrolery sprawdzające ("validating") nie zmieniają obiektów.
Kontrolery modyfikujące ("mutating") mogą dodatkowo zmieniać obiekty (np. dodawać dodatkowe adnotacje.)

Dowolny kontroler może także odrzucić obiekt dostarczony w żądaniu, przez co nie zostanie on przekazany dalej, a tym bardziej stworzony.

* [Kontrolery wejściowe w Kubernetes API](/docs/reference/access-authn-authz/admission-controllers/)