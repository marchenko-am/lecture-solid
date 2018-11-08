---
description: Принцип единственной ответственности | Single Responsibility Principe | SRP
---

# SRP

![&#x41F;&#x440;&#x438;&#x43D;&#x446;&#x438;&#x43F; &#x435;&#x434;&#x438;&#x43D;&#x441;&#x442;&#x432;&#x435;&#x43D;&#x43D;&#x43E;&#x439; &#x43E;&#x442;&#x432;&#x435;&#x442;&#x441;&#x442;&#x432;&#x435;&#x43D;&#x43D;&#x43E;&#x441;&#x442;&#x438;](.gitbook/assets/image%20%281%29.png)

 SRP звучит так:

> Существует лишь одна причина, приводящая к изменению класса.

Ответственность – это причина изменения кода.

Если при изменении кода, отвечающего за одну ответственность, в приложении появляются исправления кода, отвечающего за другую ответственность – есть нарушения SRP.

Антипаттерн – божественный объект.

```javascript
// Модель Продукт
function Product(id, description) {
    // ...
}

// Модель Корзина
function Cart() {
    // ...
}

(function() {
    var products = [
            new Product(1, 'MacBook Air'),
            new Product(2, 'iPhone 5s'),
            new Product(3, 'iPad mini')
        ],
        cart = new Cart();

    /**
     * Функция добавления товара в корзину
     */
    function addToCart() {
        // FIXME: первая причина изменения (добавление в модель через cart.addItem)
        var productId = $(this).data('product-id');
        var product = products.find(item => item.id === productId);
        cart.addItem(product);

        // FIXME: вторая причина изменения (обновление представления)
        var newItem = $('<li></li>')
            .html(product.getDescription())
            .attr('id-cart', product.getId())
            .appendTo('#cart');
    }

    products.forEach(function(product) {
        $(`[data-product-id=${product.getId()}]`).on('click', addToCart)
    });
})();


// РАЗДЕЛЯЕМ ОТВЕТСТВЕННОСТИ

function Event(name) {
    this._handlers = [];
    this.name = name;
}

// Функция добавления обработчика события
Event.prototype.addHandler = function(handler) {
    this._handlers.push(handler);
};

// Функция удаления обработчика события
Event.prototype.removeHandler = function(handler) {
    for (var i = 0; i < handlers.length; i++) {
        if (this._handlers[i] == handler) {
            this._handlers.splice(i, 1);
            break;
        }
    }
};

// Срабатывание события (выполнение всех связанных обработчиков)
Event.prototype.fire = function(eventArgs) {
    this._handlers.forEach(function(h) {
        h(eventArgs);
    });
};

var eventAggregator = (function() {
    var events = [];

    function getEvent(eventName) {
        return $.grep(events, function(event) {
            return event.name === eventName;
        })[0];
    }

    return {
        publish: function(eventName, eventArgs) {
            var event = getEvent(eventName);

            if (!event) {
                event = new Event(eventName);
                events.push(event);
            }
            event.fire(eventArgs);
        },

        subscribe: function(eventName, handler) {
            var event = getEvent(eventName);

            if (!event) {
                event = new Event(eventName);
                events.push(event);
            }

            event.addHandler(handler);
        }
    };
})();

/**
 * Модель корзины
 */
function Cart() {
    var items = [];

    this.addItem = function(item) {
        items.push(item);

        /**
         * после добавления элемента в корзину публикуем событие
         * itemAdded
         */
        eventAggregator.publish("itemAdded", item);
    };
}

/**
 * Представление корзины
 */
var cartView = (function() {
    /**
     * представление корзины подписано на событие itemAdded
     * и при наступлении этого события - отображает новый элемент
     */
    eventAggregator.subscribe('itemAdded', function(eventArgs) {
        var newItem = $('<li></li>')
            .html(eventArgs.getDescription())
            .attr('id-cart', eventArgs.getId())
            .appendTo('#cart');
    });
})();

/**
 * Контроллер корзины. Контроллер подписан на событие productSelected
 * и добавляет выбранный продукт в корзину
 */
var cartController = (function(cart) {
    eventAggregator.subscribe('productSelected', function(eventArgs) {
        cart.addItem(eventArgs.product);
    });
})(new Cart());

/**
 * Модель Продукта
 */
function Product(id, description) {
    this.getId = function() {
        return id;
    };
    this.getDescription = function() {
        return description;
    };
}

var products = [
    new Product(1, 'MacBook Air'),
    new Product(2, 'iPhone 5s'),
    new Product(3, 'iPad mini')
];

/**
 * Представление продукта
 */
var productView = (function() {
    function onProductSelected() {
        var productId = $(this).attr('id');
        var product = $.grep(products, function(x) {
            return x.getId() == productId;
        })[0];
        eventAggregator.publish('productSelected', {
            product: product
        });
    }

    products.forEach(function(product) {
        var newItem = $('<li></li>')
            .html(product.getDescription())
            .attr('id', product.getId())
            .dblclick(onProductSelected)
            .appendTo('#products');
    });
})();
```

