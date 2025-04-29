# Django Rules

## Project Structure

- Place all new Django apps inside the project's apps/ directory.
- Follow the standard Django app structure (models.py, views.py, admin.py, urls.py, forms.py, tests.py or tests/).
- Configure settings using environment variables managed by django-environ, accessed via env() in settings files.
- Use relative imports within an app, and absolute imports starting from the project root or apps for inter-app imports.

## Models (Django ORM)

- Define models in apps/<app_name>/models.py.
- Use appropriate Django field types (e.g., CharField, IntegerField, DateTimeField, ForeignKey, ManyToManyField). Define verbose_name for fields.
- Always define a __str__ method for readable object representation.
- Add database indexes (db_index=True) to fields frequently used in filtering or ordering where performance benefits are expected.
- Use models.Manager for custom queryset methods where appropriate.
- Generate migrations using `python manage.py makemigration <app_name>` and apply them with `python manage.py migrate`. Keep migrations small and focused.

## Views & Templates

- Prefer Class-Based Views (CBVs) for standard CRUD operations and complex logic, but use Function-Based Views (FBVs) for simple cases.
- Use Django's generic CBVs (e.g., ListView, DetailView, CreateView, UpdateView, DeleteView) where applicable.
- Keep business logic out of views; place it in service layers or model methods.
- Organize templates in templates/<app_name>/ directories. Use template inheritance with a base template (templates/base.html).
- Use Django template tags and filters; avoid complex logic in templates.
- Ensure all views requiring authentication use appropriate decorators (@login_required) or mixins (LoginRequiredMixin).
- Implement CSRF protection for all POST requests.

## Django REST Framework (DRF)

- Define serializers in serializers.py (usually extending serializers.ModelSerializer).
- Use ViewSets (e.g., ModelViewSet) for standard API CRUD operations.
- Configure permissions (permissions.py) and authentication classes appropriately in views or globally.
- Use DRF's built-in filtering, pagination, and ordering capabilities.
- Ensure API endpoints are documented (e.g., using drf-spectacular if configured).

## Forms

- Use Django Forms (forms.py) for data validation, especially for user input not handled by DRF serializers.
- Use ModelForm when the form directly maps to a model.

## URLs

- Define app-specific URLs in apps/<app_name>/urls.py and include them in the main urls.py using include().
- Use named URL patterns (name='...') and reverse URL resolution (reverse() or {% url %}) consistently.

## Admin

- Register important models in apps/<app_name>/admin.py for access via the Django admin site.
- Customize admin views using admin.ModelAdmin subclasses for better usability.

## Performance

- Use select_related for ForeignKey/OneToOneField lookups and prefetch_related for ManyToManyField/reverse ForeignKey lookups to optimize database queries and avoid N+1 problems.
- Use .only() or .defer() to fetch only necessary model fields for performance-critical queries.
- Use .count() for counting objects instead of len(queryset).
- Use .exists() to check for object existence instead of if queryset.
- Add db_index=True to model fields frequently used in filter(), exclude(), or order_by() clauses.

## Security

- Use Django's built-in security features (CSRF, XSS protection).
- Keep Django and all third-party dependencies updated.
- Handle SECRET_KEY and other sensitive credentials securely using environment variables (via django-environ).
- Use Django's template system for rendering HTML to leverage built-in XSS protection.
- Always include {% csrf_token %} in POST forms and ensure CsrfViewMiddleware is enabled.
