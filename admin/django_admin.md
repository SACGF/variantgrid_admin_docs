# Django Admin (admin only page)

Django comes with an [admin interface](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/) that allows users (with is_staff=True) to modify database records.

Programmers need to register the models, so let them know if something doesn't appear.

The interface is automatically generated, but the look and behavior can be customised (eg to use an autocomplete field instead of a select)

## Admin Actions

Generally, you use Django admin to edit a single record at a time, but you can also create [actions](https://docs.djangoproject.com/en/3.2/ref/contrib/admin/actions/) and apply them to multiple records (by selecting them) or all records. 

VariantGrid actions are documented in individual sections, eg the "reattempt_variant_matching" action is documented in [Classification Admin Pages](../classification/classification_admin_pages.md). 