---
layout: default
---

### Contents

- [Декларуйте doctype](#doctype)
- [Математика box моделі](#box-model-math)
- [Rem одиниці та мобільний Safari](#rems-mobile-safari)
- [Спочатку float-и](#floats-first)
- [Float-и та очищення](#floats-clearing)
- [Float-и і обчислювана висота](#floats-computed-height)
- [Плаваючі елементи зразу є блоками](#floats-block-level)
- [Колапс вертикально суміжних margin-ів](#vertical-margins-collapse)
- [Стилізація рядків таблиці](#styling-table-rows)
- [Firefox inner outline on buttons](#buttons-firefox-outline)
- [Always set a `type` on `<button>`s](#buttons-type)
- [Internet Explorer's selector limit](#ie-selector-limit)
- [Пояснення позиціонуванню](#position-explained)
- [Позиція і ширина](#position-width)
- [Фіксована позиція та перетворення](#position-transforms)


<a name="doctype"></a>
### Декларуйте doctype
Завжди додавайте doctype. Я рекомендую простий HTML5 doctype:

```html
<!DOCTYPE html>
```

[Упускання doctype може спричиняти помилки](http://quirks.spec.whatwg.org) з неправильними таблицями, формами тощо, оскільки сторінка буде виведена в примхливому режимі.


<a name="box-model-math"></a>
### Математика box моделі
Елементи, що мають встановлену ширину `width` стають *ширшими* коли вони мають встановлені `padding` та/чи `border-width`. Аби уникнути цих проблем, використовуйте тепер повсякденне [`box-sizing: border-box;` скидання](http://www.paulirish.com/2012/box-sizing-border-box-ftw/).


<a name="rems-mobile-safari"></a>
### Rem одиниці та мобільний Safari
Хоча мобільний Safari підтримує використання `rem`-ів у всіх властивостях, він, схоже, нервово курить в сторонці, коли використовуються `rem` в просторових мультимедійних запитах і нескінченно миготить текст сторінки в різних розмірах.

Поки що, використовуйте `em`-и замість `rem`-ів.

```css
html {
  font-size: 16px;
}

/* Спричиняє миготіння в мобільному Safari */
@media (min-width: 40rem) {
  html {
    font-size: 20px;
  }
}

/* Прекрасно працює в мобільному Safari */
@media (min-width: 40em) {
  html {
    font-size: 20px;
  }
}
```

**Допоможіть!** *Якщо у вас є посилання на повідомлення про помилку Apple або WebKit, я хотів би його включити. Я не впевнений, куди повідомити про це, оскільки баг стосується лише мобільного, а не десктопного Safari.*


<a name="floats-first"></a>
### Спочатку float-и
Плаваючі елементи повинні завжди бути першими в порядку документа. Плаваючі елементи зобов'язані щось обернути, інакше вони можуть спричинити ефект випадання, а не з'являтиметься нижче вмісту.

```html
<div class="parent">
  <div class="float">Плаваємо</div>
  <div class="content">
    <!-- ... -->
  </div>
</div>
```


<a name="floats-clearing"></a>
### Float-и та очищення
Якщо ви пускаєте щось у плавання, то, *можливо*, варто щось очистити. Будь-який вміст, який йде за елементом з "float", обертається навколо цього елемента до моменту очищення. Щоб очистити float-и, використовуйте однин з наступних методів.

Використовуйте [micro clearfix](http://nicolasgallagher.com/micro-clearfix-hack/), аби очистити ваші float-и за допомогою окремого класу.

```css
.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}
```

З іншого боку, можна вказати `overflow`, з параметрами `auto` чи `hidden`, для батьківського елемента.

```css
.parent {
  overflow: auto; /* clearfix */
}
.other-parent {
  overflow: hidden; /* clearfix */
}
```

Майте на увазі, що `overflow` може спричинити інші небажані побічні ефекти, як правило, навколо розташованих елементів у батьківському елементі.

**Професійна порада!** *Зберігайте свої нерви на майбутнє та ваших колег задоволенням, включивши коментар на кшталт `/* clearfix */` під час очищення float-ів, оскільки властивість може бути використана з інших причин.*


<a name="floats-computed-height"></a>
### Float-и і обчислювана висота
Батьківський елемент, який складається лише з float-ів, матиме висоту `height: 0;`. Додайте clearfix для батьківського елементу, аби змусити браузерів обчислювати висоту.


<a name="floats-block-level"></a>
### Плаваючі елементи зразу є блоками
Елементи з `float` автоматично стануть `display: block;`. Не встановлюйте обидва, оскільки в цьому немає жодної необхідності, і `float` все одно перевизначить ваш `display`.

```css
.element {
  float: left;
  display: block; /* Не має необхідності */
}
```

**Цікавий факт:** *Роки тому, ми мали встановлювати `display: inline;` для більшості float-ів для правильної роботи в IE6 аби уникнути [баг подвоєного відступу](http://www.positioniseverything.net/explorer/doubled-margin.html). Проте, ці часи давно минули.*


<a name="vertical-margins-collapse"></a>
### Колапс вертикально суміжних margin-ів
Верхні і нижні відступи (margins) на сусідніх елементах (один за іншим) можуть і будуть руйнуватися в багатьох ситуаціях, але ніколи для плаваючих або абсолютно розташованих елементів. [Прочитайте цю MDN статтю](https://developer.mozilla.org/uk/docs/Web/CSS/margin_collapsing) або документацію CSS2 [collapsing margin section](http://www.w3.org/TR/CSS2/box.html#collapsing-margins) щоб дізнатись більше.

Горизонтально суміжні margin-и **ніколи не руйнуються**.


<a name="styling-table-rows"></a>
### Стилізація рядків таблиці
Рядки таблиці, `<tr>`-и, не зважають на `border`-и допоки ви не встановите `border-collapse: collapse;` для батьківського елементу `<table>`. До того ж, якщо `<tr>` і дочірні `<td>`-и чи `<th>`-и мають *однаковий* `border-width`, рядки не бачитимуть власні `border` правила. [Перегляньте це JS Bin посилання, як приклад.](http://jsbin.com/yabek/2/)


<a name="buttons-firefox-outline"></a>
### Firefox inner outline on buttons

Firefox [adds an inner outline](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#Notes) to buttons (`<input>`s and `<button>`s) on `:focus`. Apparently it's for accessibility, but its placement seems rather odd. Use this CSS to override it:

```css
input::-moz-focus-inner,
button::-moz-focus-inner {
  padding: 0;
  border: 0;
}
```

You can see this fix in action in the same [JS Bin example](http://jsbin.com/yabek/4/) mentioned in the previous section.

**Pro-Tip!** *Be sure to include some focus state on buttons, links, and inputs. Providing an affordance for accessibility is paramount, both for pro users who tab through content and those with vision impairments.*


<a name="buttons-type"></a>
### Завжди задавайте атрибут `type` для тегу `<button>`s
Значення за замовчуванням `submit`, означає, що будь-яка в формі може відправити форму. Задавайте `type="button"` для всіх тегів `<button>`, що не відправляють форму і `type="submit"` для тих, які відправляють.

```html
<button type="submit">Save changes</button>
<button type="button">Cancel</button>
```

Для дій, які вимагають використання тегу `<button>` і є не всередині форми використовуйте `type="button"`.

```html
<button class="dismiss" type="button">x</button>
```

**Цікавий факт:** *Очевидно, що браузер IE7 не належним чином підтримує атрибут `value` для тегу `<button>`s. Замість того, щоб читати вміст атрибуту, він витягує з innerHTML (вміст між відкриваючим і закриваючим тегом `<button>`). Однак, я не розглядаю це, як величезне занепокоєння з двох причин: використання IE7 зменшується, і здається досить незвичним встановити атрибут `value` і innerHTML для тегу `<button>`s.*


<a name="ie-selector-limit"></a>
### Internet Explorer's selector limit
Internet Explorer 9 and below have a max of 4,096 selectors per stylesheet. There is also a limit of 31 combined stylesheets and `<style></style>` includes per page. Anything after this limit is ignored by the browser. Either split your CSS up, or start refactoring. I'd suggest the latter.

As a helpful side note, here's how browsers count selectors:

```css
/* One selector */
.element { }

/* Two more selectors */
.element,
.other-element { }

/* Three more selectors */
input[type="text"],
.form-control,
.form-group > input { }
```


<a name="position-explained"></a>
### Пояснення позиціонуванню
Елементи з `position: fixed;` розташовані відносно вікна браузера. Елементи з `position: absolute;` розташовані відносно найближчого батьківського елементу з позиціонуванням, іншим за `static` (напр., `relative`, `absolute`, чи `fixed`).


<a name="position-width"></a>
### Позиція і ширина
Не встановлюйте `width: 100%;` на елементі, що має `position: [absolute|fixed];`, `left`, та `right`. Використання `width: 100%;` це те саме, що й комбіноване використання `left: 0;` та `right: 0;`. Використовуйте щось одне.


<a name="position-transforms"></a>
### Фіксована позиція та перетворення
Браузери ламають `position: fixed;` коли батьківський елемент має встановлену властивість `transform`. Використання перетворень створює новий блок, який фактично змушує батьківський елемент мати `position: relative;` і фіксований елемент вести себе як `position: absolute;`.

[Дивіться демо](http://jsbin.com/yabek/1/) і читайте [публікацію Еріка Мейера з цього приводу](http://meyerweb.com/eric/thoughts/2011/09/12/un-fixing-fixed-elements-with-css-transforms/).
