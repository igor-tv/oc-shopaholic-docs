# CouponGroupItem {docsify-ignore-all}

!> **Attention!**  We recommend that you read [Architecture](home.md#architecture) and [ElementItem class](item-class/item-class.md) sections for complete understanding of  project architecture.

## Field list

|  Name | Type | Description |
|-------|------|--------|
|id|int|
|date_begin|\October\Rain\Argon\Argon|
|date_end|\October\Rain\Argon\Argon|
|discount_type|string|Available values: fixed, percent|
|discount_value|float|
|name|string|
|product|[ProductCollection](product/collection/collection.md)|
|promo_block|[PromoBlockItem](promo-block/item/item.md)|
|promo_block_id|int|

## Extending

You can add dynamic methods and properties in item class with using [extending constructors](http://octobercms.com/docs/services/behaviors#constructor-extension).
It is default function of OctoberCMS.

You can add custom fields to **$cached** array of **CouponGroup** model.
```php
CouponGroup::extend(function($obModel) {
    $obModel->addCachedField(['field_1', 'field_2']);
});

...

$obCouponGroupItem = CouponGroupItem::make(1);
echo $obCouponGroupItem->field_1;
```

You can add custom fields to cached data array with using your custom method.
You need to add dynamic method in CouponGroupItem class and add your method name in **$arExtendResult** array.
```php
CouponGroupItem::extend(function($obItem) {

     $obItem->arExtendResult[] = 'addMyProperty';

     $obItem->addDynamicMethod('addMyProperty', function() use ($obItem) {

         $obModel = $obItem->getObject();
         $obItem->setAttribute('my_property', $obModel->my_property);
     });
});

...

$obCouponGroupItem = CouponGroupItem::make(1);
echo $obCouponGroupItem->my_property;
```