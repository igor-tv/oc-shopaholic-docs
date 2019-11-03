# Examples: Brand

[Back to modules](modules/home.md)
/ [Home](modules/brand/home.md)
/ [Model](modules/brand/model/model.md)
/ [Item](modules/brand/item/item.md)
/ [Collection](modules/brand/collection/collection.md)
/ [Components](modules/brand/component/component.md)
/ [Events](modules/brand/event/event.md)
/ Examples
/ [Extending](modules/brand/extending/extending.md)

!> **Attention!** We recommend that you read [Architecture](home.md#architecture), [ElementItem class](item-class/item-class.md),
[ElementCollection class](collection-class/collection-class.md) sections for complete understanding of  project architecture.

* [Example 1: Brand page](#example-1-brand-page)
* [Example 2: Brand card](#example-2-brand-card)
* [Example 3: Random brand list](#example-3-random-brand-list)
* [Example 4: Brand list with pagination](#example-4-brand-list-with-pagination)
* [Example 5: Filter panel with brands](example-5-filter-panel-with-brands)

## Example 1: Brand page

### 1.1 Task

Create simple brand page and render brand name.

### 1.2 How can i do it?

> Example uses [BrandPage](modules/brand/component/component.md#brandpage) component.
Component method returns [BrandItem](modules/brand/item/item.md#branditem) class object.
All available fields and methods of **BrandItem** class you can find in [section](modules/brand/item/item.md#branditem)

```plantuml
@startuml
:Create page file;
note left
    For example: **pages/brand.htm**
end note
:Attach **BrandPage** component;
:Get BrandItem object
from BrandPage component;
:Render brand name;
@enduml
```

### 1.3 Source code

File: **pages/brand.htm**
```twig
title = "Brand page"
url = "/brands/:slug"
layout = "main"
is_hidden = 0

[BrandPage]
slug = "{{ :slug }}"
slug_required = 1
smart_url_check = 1
==

{# Get brand item #}
{% set obBrand = BrandPage.get() %}

<div data-id="{{ obBrand.id }}" itemscope itemtype="http://schema.org/Brand">
    <h1 itemprop="name">{{ obBrand.name }}</h1>
</div>
```

## Example 2: Brand card

### 2.1 Task

Create simple brand card and render brand name, preview_image, preview_text fields.
Render link on brand page.

> **"obBrand"** is object of [BrandItem](modules/brand/item/item.md#branditem) class.

### 2.2 Source code

Simple example of brand card.

File: **partials/brand/brand-card/brand-card.htm**
```twig
<a href="{{ obBrand.getPageUrl('brand') }}">
    <div itemscope itemtype="http://schema.org/Brand">
        {% if obBrand.preview_image is not empty %}
            <img src="{{ obBrand.preview_image.path }}" itemprop="image" alt="{{ obBrand.preview_image.description }}" title="{{ obBrand.preview_image.title }}">
        {% endif %}
        <h3 itemprop="name">{{ obBrand.name }}</h3>
        {% if obBrand.preview_text is not empty %}
            <div itemprop="description">
                {{ obBrand.preview_text }}
            </div>
        {% endif %}
    </div>
</a>
```

## Example 3: Random brand list

### 3.1 Task

Create simple block with random 5 brand list on index page.

### 3.2 How can i do it?

> Example uses [BrandList](modules/brand/component/component.md#brandlist) component.
Component method returns [BrandCollection](modules/brand/collection/collection.md#brandcollection) class object.
All available methods of **BrandCollection** class you can find in [section](modules/brand/collection/collection.md#brandcollection)

```plantuml
@startuml
:Create page file;
note left
    For example: **pages/index.htm**
end note
:Attach BrandList component to page;
:Create partial "random-brand-list";
note left
    For example:
    **partials/brand/brand-list**
    **/random-brand-list.htm**
end note
:Get BrandCollection object from
BrandList component;
:Apply filter by "active" field
to BrandCollection object;
:Get array with 5 random
BrandItem objects;
:Render brand list;
@enduml
```

### 3.3 Source code

File: **pages/index.htm**
```twig
title = "Index"
url = "/"
layout = "main"
is_hidden = 0

[BrandList]
==
<div class="brand-wrapper">
    {% partial 'brand/brand-list/random-brand-list' %}
</div>
```

File: **partials/brand/brand-list/random-brand-list.htm**
```twig
{# Get brand collection #}
{% set obBrandList = BrandList.make().active() %}
{# Get array with random brands #}
{% set arBrandList = obBrandList.random(5) %}

{% if arBrandList is not empty %}
    {# Render brand list #}
    <ul>
        {% for obBrand in arBrandList %}
            <li>{% partial 'brand/brand-card/brand-card' obBrand=obBrand %}</li>
        {% endfor %}
    </ul>
{% endif %}
```

## Example 4: Brand list with pagination

### 4.1 Task

Create simple page with brand list.
Brand list must have pagination block.

### 4.2 How can i do it?

> Example uses [BrandList](modules/brand/component/component.md#brandlist) component.
Component method returns [BrandCollection](modules/brand/collection/collection.md#brandcollection) class object.
All available methods of **BrandCollection** class you can find in [section](modules/brand/collection/collection.md#brandcollection)

> You can find more information about **Pagination** component [here](modules/pagination/home.md)

```plantuml
@startuml
:Create page file;
note left
    For example: **pages/brand-list.htm**
end note
:Attach BrandList component to page;
:Create wrapper for block with list of brands;
:Create partial "brand-list";
note left
    For example:
    **partials/brand/brand-list/brand-list.htm**
end note
:Get BrandCollection object from
BrandList component;
:Apply filter by "active" field
to BrandCollection object;
:Apply sorting to BrandCollection object;
:Get current page number from request;
:Get list of pagination buttons;
:Get array of BrandItem objects
for current page;
if (Brand list is empty?) then (yes)
    :Render block "Brands not found";
else (no)
    :Render brand cards;
    :Render pagination buttons;
endif
:Call partial "brand-list" inside wrapper block;
:Add ajax handler on pagination buttons;
:Send ajax request, after user clicks pagination buttons;
:AJAX request must request "brand-kist" partial
and update HTML code inside wrapper block;
@enduml
```

### 4.3 Source code

File: **pages/brand-list.htm**
```twig
title = "Brand list"
url = "/brands"
layout = "main"
is_hidden = 0

[BrandList]

[Pagination]
count_per_page = 15
pagination_limit = 5
active_class = ""
button_list = "first,first-more,main,last-more,last"
first_button_name = "First"
first_button_limit = 3
first_button_number = 1
first-more_button_name = "..."
first-more_button_limit = 4
prev_button_name = "Prev"
prev_button_limit = 1
prev-more_button_name = "..."
prev-more_button_limit = 1
next-more_button_name = "..."
next-more_button_limit = 1
next_button_name = "Next"
next_button_limit = 1
last-more_button_name = "..."
last-more_button_limit = 4
last_button_name = "Last"
last_button_limit = 3
last_button_number = 1
==
<div class="brand-wrapper">
    {% partial 'brand/brand-list/brand-list' %}
</div>
```

File: **partials/brand/brand-list/brand-list.htm**
```twig
{# Get brand collection #}
{% set obBrandList = BrandList.make().sort().active() %}

{# Get array with pagination buttons #}
{% set iPage = Pagination.getPageFromRequest() %}
{% set arPaginationList = Pagination.get(iPage, obBrandList.count()) %}

{# Apply pagination to brand collection and get array with brand items #}
{% set arBrandList = obBrandList.page(iPage, Pagination.getCountPerPage()) %}

{% if arBrandList is not empty %}
    {# Render brand list #}
    <ul>
        {% for obBrand in arBrandList %}
            <li>{% partial 'brand/brand-card/brand-card' obBrand=obBrand %}</li>
        {% endfor %}
    </ul>
    
    {# Render pagination buttons #}
    {% if arPaginationList is not empty %}
        {% for arPagination in arPaginationList %}
            <a href="?page={{ arPagination.value }}" class="{{ arPagination.class }}" data-page="{{ arPagination.value }}">{{ arPagination.name }}</a>
        {% endfor %}
    {% endif %}
{% else %}
    <div>
        Brands not found
    </div>
{% endif %}
```

## Example 5: Filter panel with brands

### 5.1 Task

Create simple catalog page with filter panel by brands.
Apply filter by category ID to brand list.  

### 5.2 How can i do it?

> Example uses [BrandList](modules/brand/component/component.md#brandlist) component.
Component method returns [BrandCollection](modules/brand/collection/collection.md#brandcollection) class object.
All available methods of **BrandCollection** class you can find in [section](modules/brand/collection/collection.md#brandcollection)

```plantuml
@startuml
:Create catalog page file;
note left
    For example: **pages/catalog.htm**
end note
:Attach BrandList component to page;
:Attach CategoryPage component to page;
:Create partial "filter-brand-list";
note left
    For example:
    **partials/brand/brand-list**
    **/filter-brand-list.htm**
end note
:Get CategoryItem object from
CategoryPage component;
:Get BrandCollection object from
BrandList component;
:Apply filter by "active" field
to BrandCollection object;
:Apply filter by "category" field
to BrandCollection object;
:Get array with BrandItem objects;
:Render brand list;
@enduml
```

### 5.3 Source code

File: **pages/catalog.htm**
```twig
title = "Catalog"
url = "/catalog/:category"
layout = "main"
is_hidden = 0

[CategoryPage]
slug = "{{ :category }}"
slug_required = 1
smart_url_check = 1

[ProductList]
sorting = "popularity|desc"

[BrandList]
==
<div class="filter-brand-wrapper">
    {% partial 'brand/brand-list/filter-brand-list' %}
</div>
```

File: **partials/brand/brand-list/filter-brand-list.htm**
```twig
{# Get CategoryItem object #}
{% set obCategory = CategoryPage.get() %}

{# Get brand collection #}
{% set obBrandList = BrandList.make().active().sort().category(obCategory.id) %}

{% if obBrandList.isNotEmpty() %}
    {# Render brand list #}
    <ul>
        {% for obBrand in obBrandList %}
            <li>
                <input type="checkbox" name="filter-brand" value="{{ obBrand.slug }}" id="brand-{{ obBrand.id }}">
                <label for="brand-{{ obBrand.id }}">{{ obBrand.name }}</label>
            </li>
        {% endfor %}
    </ul>
{% endif %}
```

[Back to modules](modules/home.md)
/ [Home](modules/brand/home.md)
/ [Model](modules/brand/model/model.md)
/ [Item](modules/brand/item/item.md)
/ [Collection](modules/brand/collection/collection.md)
/ [Components](modules/brand/component/component.md)
/ [Events](modules/brand/event/event.md)
/ Examples
/ [Extending](modules/brand/extending/extending.md)