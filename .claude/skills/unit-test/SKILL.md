---
name: unit-test
description: Write and run Django tests in plain TestCase / APITestCase style - one test per decision the code makes. Use whenever asked to add tests or to cover new code.
---
# Tests

Plain `django.test.TestCase` / `rest_framework.test.APITestCase`, run with `python manage.py test`. No pytest. One `tests/` package per app: `<app>/tests/test_<feature>.py`.

## One test per decision
- **Model**: constraints that matter (`unique`, `choices`) and any `clean()` / `save()` rule.
- **Serializer**: valid input -> right `validated_data`; each rule -> its own 400 case.
- **Service**: each branch of the business rule (status moves, who acts next).
- **View**: happy path (status + response shape - only the fields the screen needs), each 400, 403 for a role that can't act, 404 for another tenant.

## Setup
- Shared data in `setUpTestData` (runs once per class).
- If your user manager creates inactive users, pass `is_active=True` or auth-gated requests fail.
- Authenticate with `self.client.force_authenticate(user)`. Use a real login only when testing login itself.

## Side effects
- **Celery**: call `task.apply()` - runs inline, no broker.
- **Redis**: if a test truly needs it, PING in `setUpClass` and `skipTest` when down. Never an always-false skip.
- **Email / push / websocket**: `mock.patch` the sending function.

## Response shape test
Assert the exact key set for list rows and buttons, so extra fields can't creep in:
```python
self.assertEqual(set(row), {"id", "reference", "status", "assignee"})
```

## Run
```bash
python manage.py test <app>
python manage.py test        # when you touched core/, config/, or a shared signal
```
Not done until it ran green.
