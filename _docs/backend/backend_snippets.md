---
title: Backend Snippets
authors: [skerbis]
prio:
---

# Backend Snippets

Snippets und Tweaks zur Modifikation des Backends

1. [AddOn ausblenden](#addonhide)
2. [Seite eines AddOns durch eigene austauschen/ersetzen](#replacepage)
3. [Sortierung des Medienpools ändern](#mediasort)


<a name="addonhide"></a>
## AddOn ausblenden

Es ist möglich, einzelne AddOns auch für Administratoren auszublenden. In diesem Beispiel werden der Installer und die Systemverwaltung ausgeblendet.

**Folgenden Code in die boot.php des Project-AddOns platzieren:**

```php
if (rex::isBackend() and rex_backend_login::hasSession()) {
  $page = rex_be_controller::getPageObject('system');
  $page->setHidden(true);
  $page->setPath('...');

  $page = rex_be_controller::getPageObject('packages');
  $page->setHidden(true);
  $page->setPath('...');
}
```

<a name="replacepage"></a>
## Seite eines AddOns durch eigene austauschen/ersetzen

Will man eine alternative Seite in einem AddOn darstellen und die vorhandene ersetzen, kann dies mit dem nachfolgenden Code erfogen. In diesem Beispiel wird die `index.php` des Struktur-AddOns ausgetauscht. 

Verwendeter Extension point: [PAGES_PREPARED](https://github.com/redaxo/redaxo/blob/591146a1dc60e8aacefd58dc9b7e9c307c0983b9/redaxo/src/core/backend.php#L132)

**Folgenden Code in die boot.php des Project-AddOns platzieren:**

```php
rex_extension::register('PAGES_PREPARED',function($ep) {
  if (rex_be_controller::getCurrentPage() == 'structure') {
    $Page = rex_be_controller::getCurrentPageObject();
    $Page->setPath($this->getPath('pages/index.php'));
  }
});
```

<a name="mediasort"></a>
## Sortierung des Medienpools ändern

Die Standard-Sortierung im Medienpool ist immer chronologisch. Die folgende Lösung sorgt für eine alphabetische Sortierung. 

Verwendeter Extension point: [MEDIA_LIST_QUERY](https://github.com/redaxo/redaxo/blob/0b624db20ce0baab171ff054d975645e22eceed8/redaxo/src/addons/mediapool/pages/media.php#L637-L642)

**Folgenden Code in die boot.php des Project-AddOns platzieren:**

```php
// Sortierung Medienpool aufsteigend
if (rex::isBackend() && rex::getUser()) {
  rex_extension::register('MEDIA_LIST_QUERY', function (rex_extension_point $ep) {
    $subject = $ep->getSubject();
    $subject = str_replace("f.updatedate", "f.filename, f.updatedate", $subject);
    $subject = str_replace("desc", "asc", $subject);
    return $subject;
  });
}
```
