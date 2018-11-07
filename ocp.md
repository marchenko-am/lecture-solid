---
description: Принцип открытости/закрытости | Open Closed Principle |  OCP
---

# OCP

![&#x41F;&#x440;&#x438;&#x43D;&#x446;&#x438;&#x43F; &#x43E;&#x442;&#x43A;&#x440;&#x44B;&#x442;&#x43E;&#x441;&#x442;&#x438;/&#x437;&#x430;&#x43A;&#x440;&#x44B;&#x442;&#x43E;&#x441;&#x442;&#x438;](.gitbook/assets/image%20%282%29.png)

> Программные сущности \(классы, модули, функции и т.д.\) должны быть открыты для расширения, но закрыты для изменения.

Идея была в том, что однажды разработанная реализация класса в дальнейшем требует только исправления ошибок, а новые или изменённые функции требуют создания нового класса. Этот новый класс может переиспользовать код исходного класса через механизм наследования. Производный подкласс может реализовывать или не реализовывать интерфейс исходного класса.

```javascript
const FieldType = {
    TEXT: 'text',
    CALENDAR: 'calendar',
    ON_OFF: 'on-off',
    SELECT: 'select'
};

class Field {
    constructor(type) {
        this._type = type;
        this._value = null;
    }

    // ... (логика скрыта)

    getValue () {
        switch (this._type) {
            case FieldType.TEXT: return this._value;
            case FieldType.CALENDAR: return {dateStart: this._value.start, dateEnd: this._value.end};
            case FieldType.ON_OFF: return !!this._value;
            case FieldType.SELECT: return this._value[0];
            // FIXME - нарушение принципа. Если добавится новый тип, нужно модифицировать класс...
        }
    }

    isSelected () {
        switch (this._type) {
            case FieldType.TEXT: return this._value !== '';
            case FieldType.CALENDAR: return this._value.start !== '' && this._value.end !== '';
            case FieldType.ON_OFF: return !!this._value;
            case FieldType.SELECT: return this._value.length > 0;
            // FIXME - ...да еще и во многих местах
        }
    }

}

// РЕШЕНИЕ

class Field {
    constructor() {
        this._value = null;
    }

    // ... (логика скрыта)

    getValue () {
        return '';
    }

    isSelected () {
        return this._value !== '';
    }

}

class CalendarField extends Field {

    // ... (логика скрыта)

    getValue () {
        return {dateStart: this._value.start, dateEnd: this._value.end}
    }

    isSelected () {
        return this._value.start !== '' && this._value.end !== '';
    }

}

class SelectField extends Field {

    // ... (логика скрыта)

    getValue () {
        return this._value[0];
    }

    isSelected () {
        return this._value.length > 0;
    }

}
```

