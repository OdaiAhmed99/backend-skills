---
name: translation
description: When and how to translate backend strings - as the last step before the PR, not the first. Use when a feature needs i18n or someone asks to translate.
---
# Translation - last layer

Build in plain English. Get the logic right and the tests green. **Then** translate. Wrapping strings early means re-wrapping every time the copy changes.

## Two different things
**1. Messages and labels** (errors, choice labels, `message` on buttons) - `gettext_lazy`:
```python
from django.utils.translation import gettext_lazy as _
return Response({"message": _("You can't edit this record.")}, status=403)
```
```bash
python manage.py makemessages -l <lang>
# fill msgstr in locale/<lang>/LC_MESSAGES/django.po
python manage.py compilemessages
```
The language comes from `Accept-Language` through `LocaleMiddleware` - no code per view.

**2. Stored values that need a version per language** (a category name) - `django-modeltranslation`, registered in the app's `translation.py`. It adds `<field>_<lang>` columns, so it needs a migration.

## Checklist
1. Works in English, tests green.
2. Wrap user-facing strings; register translatable fields.
3. Migrate if fields were registered.
4. `makemessages` -> translate -> `compilemessages`.
5. Run the tests again - wrapped strings can change asserted bodies.
