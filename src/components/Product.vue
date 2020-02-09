<template>
    <div class="product">
        <a href="#" class="product__link">
            <img class="product__image" :src="primaryImageUrl" alt="фото продукта">
        </a>
        <a class="product__status product__status_big-screen" :title='has'>Наличие</a>
        <div class="product__description">
            <div class="product__retailer-info">
                <p class="product__code">Код: {{code}}</p>
                <p class="product__status product__status_default" :title='has'>Наличие</p>
            </div>
            <a href="#" class="product__link">
                <p class="product__specification">{{title}}</p>
            </a>
            <div class="product__tags">
                <p class="product__advice">Могут понадобиться: <span v-html="space"></span></p>
                <span
                class="product__tag"
                v-for="tag in currentAssocProducts"
                :key='tag.id'
                >
                    <a class="product__link" href="#">{{tag.name}}</a>
                    <p v-html="space"></p>
                </span>
            </div>
        </div>
        <div class="buy">
            <div class="price price__gold">
                <p class="price__gold-text">По карте клуба</p>
                <span class="price__value price__gold-value">{{unitData.picked ? priceGoldAlt : priceGold | round}} &#8381;</span>
            </div>
            <div class="price price__default">
                <span class="price__value price__default-value">{{unitData.picked ? priceRetailAlt : priceRetail | round}} &#8381;</span>
            </div>
            <p class="price__points">Можно купить за {{unitData.picked ? priceRetailAlt / 1.7 : priceRetail / 1.7 | round}} балла</p>
            <div class="units">
                <p 
                :class="[unitData.picked ? unitData.selected : '', unitData.baseClasses]"
                @click="select"
                >За м. кв.</p>
                <p 
                :class="[!unitData.picked ? unitData.selected : '', unitData.baseClasses]"
                @click="select"   
                >За упаковку</p>
            </div>
            <div v-if="hasAlternateUnits" class="tip">
                <img class="tip__logo" src="../assets/tip__logo.png" alt="">
                <div class="tip__container">
                    <p>Продается упаковками:</p>
                    <p>1 упаковка = {{unitsPerPackage}} м.кв</p>
                </div>
            </div>
            <form class="form">
                <div class="form__stepper">
                    <input class="form__input" v-model="formValue">
                    <div class="stepper__controls">
                        <button type="button" @click="increaceFormValue" class="up-control"><p class="up">&lt;</p></button>
                        <button type="button" @click="decreaceFormValue" class="down-control"><p class="down">&lt;</p></button>
                        
                    </div>
                </div>
                <button class="basket" :data-product-id='productId'>
                    <svg class="basket__img">
                        <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#cart">
                            <svg id="cart">
                                <path d="m14.571 16.381c.571 0 .952.381.952.952 0 .571-.381.952-.952.952-.571 0-.952-.381-.952-.952 0-.571.476-.952.952-.952m0-.952c-1.048 0-1.905.857-1.905 1.905 0 1.048.857 1.905 1.905 1.905 1.048 0 1.905-.857 1.905-1.905 0-1.048-.857-1.905-1.905-1.905"></path>
                                <path d="m7.905 16.381c.571 0 .952.381.952.952 0 .571-.381.952-.952.952-.571 0-.952-.381-.952-.952 0-.571.476-.952.952-.952m0-.952c-1.048 0-1.905.857-1.905 1.905 0 1.048.857 1.905 1.905 1.905 1.048 0 1.905-.857 1.905-1.905 0-1.048-.857-1.905-1.905-1.905"></path>
                                <path d="m16.476 14.476h-10.857l-.095-.381c0-.095-1.429-9.714-1.905-11.524-.381-1.524-3.333-1.429-3.333-1.429v-.952c.095 0 3.714-.286 4.286 2.19.381 1.714 1.619 9.333 1.81 11.143h10.1v.952"></path>
                                <path d="m4.095 3.048h15.238v.952h-15.238z"></path>
                                <path d="m5.05 10.667h12.381v.952h-12.381z"></path>
                                <path d="m16.476 11.619h.952l1.905-8.571h-.952l-1.905 8.571"></path>
                            </svg>
                        </use>
                    </svg>
                    <p class="basket__text">в корзину</p>
                </button>
            </form>
        </div>
    </div>
</template>

<script>
export default {
    
    name: 'Product',
    props: [
        "productId",
        "code",
        "title",
        "description",
        "primaryImageUrl",
        "assocProducts",
        "weight",
        "unit",
        "unitFull",
        "unitRatio",
        "unitAlt",
        "unitRatioAlt",
        "unitFullAlt",
        "priceRetail",
        "priceRetailAlt",
        "priceGold",
        "priceGoldAlt",
        "bonusAmount",
        "hasAlternateUnit",
        "isActive",
        "modified"
    ],
    data() {
        return {
            space: '&nbsp;',
            unitData: {
                picked: true,
                selected: 'unit_selected',
                baseClasses: 'unit'
            },
            formValue: '1',
            has: this.isActive ? this.has = "В наличии" : this.has = 'Товар отсутствует на складе',
            unitReg: /\d+(\.|,)\d+/g
        }
    },
    methods: {
        select() {
            this.unitData.picked = !this.unitData.picked
        },
        increaceFormValue() {
            this.formValue++
        },
        decreaceFormValue() {
            this.formValue > 0 ? this.formValue-- : ''
        }
    },
    computed: {
        currentAssocProducts() {
            const arr = this.assocProducts.split(';');
            arr.pop()
            const newArr = arr.map((item, index, arr) => {
                return index + 1 != arr.length ?
                    item = {
                        name: item + ',',
                        id: index
                    }
                    :
                    item = {
                        name: item + '.',
                        id: index
                    }
            })
            return newArr
        },
        unitsPerPackage() {
            return this.title.match(this.unitReg).join('')
        },
        hasAlternateUnits() {
            return this.unitReg.test(this.title)
        }
    },
    filters: {
        round(value) {
            return Math.round(value)
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
p {
    margin: 0;
}
.product {
    position: relative;
    display: flex;
    justify-content: space-between;
    max-width: 990px;
    padding: 10px;
    border: 1px solid #e0e0e0;
    font-family: Arial,Helvetica,sans-serif;
    color: #222;
    font-size: 14px;
    line-height: 1.4;
}
.product__image {
    width: 220px;
    height: 220px;
}
.product__tags {
    display: flex;
    align-items: baseline;
    flex-wrap: wrap;
    font-size: 12px;
    line-height: 18px;
    color: #666;
}
.product__advice {
    font-size: 13px;
    line-height: 16px;
    font-weight: bold;
}
.product__tag {
    display: flex;
    align-items: baseline;
}
.product__link {
    text-decoration: none;
    color: #000;
    cursor: pointer;
}
.product__link:hover {
    text-decoration: underline
}
.product__retailer-info {
    display: flex;
    align-items: baseline;   
}
.product__code {
    font-size: 12px;
    color: #666;
}
.product__status {
    color: #093;
    border-bottom: 1px dotted #093;
    text-decoration: none;
    cursor: pointer;
}
.product__status:hover {
    border-bottom-color: transparent;
}
.product__status_default {
    margin-left: 10%;
}
.product__status_big-screen {
    position: absolute;
    right: 10px;
    display: none;
}
.product__description {
    max-width: 500px;
    margin: 0 2%;
}
.product__specification {
    margin: 5% 0;
    font-weight: bold;
}
.buy {
    display: flex;
    flex-direction: column;
    align-items: flex-end;
    max-width: 220px;
}
.price {
    display: flex;
    align-items: baseline
}
.price__gold-text {
    margin-right: 10px;
}
.price__gold-value {
    font-weight: bold;
}
.price__default {
    color: #a7a7a7;
}
.price__value {
    
    font-size: 25px;
}
.price__points {
    font-size: 12px;
    line-height: 17px;
    color: #999;
}
.units {
    display: flex;
    align-items: baseline;
    margin: 5px 0;
}
.unit {
    border-bottom: 1px dotted #707070;
    margin-right: 10px;
    cursor: pointer;
    color: #707070;
}
.unit:last-of-type {
    margin-right: 0;
}
.unit_selected {
    background-color: #000;
    color: #fff;
    border-bottom: none;
    padding: 2px 4px;
}
.tip {
    border: 1px solid #ccc;
    margin: 5px 0;
    padding: 2.5% 5%;
    width: calc(90% - 2px);
    display: flex;
    align-items: center;
    font-size: 12px;
    line-height: 15px;
}
.tip__logo {
    width: 15px;
    height: 15px;
    margin-right: 5%;
}
.form {
    display: flex;
    justify-content: space-between;
    width: 100%;
}
.form__stepper {
    display: flex;
    min-height: 38px;
    width: 65px;
    border: 1px solid #ccc;
    align-items: center
}
.form__input {
    border: none;
    padding: 0;
    width: 65%;
    text-align: center;
}
.form__input:active, .form__input:focus {
    outline: none;
}
.stepper__controls {
    height: 100%;
    width: 35%;
}
.up-control, .down-control {
    background-color: transparent;
    border: none;
    border-left: 1px solid #ccc;
    padding: 0;
    height: 50%;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center
}
.up-control:focus, .down-control:focus {
    outline: none
}
.up-control:hover, .down-control:hover, .basket:hover {
    background-color: #666;
    color: #fff;
    cursor: pointer;
}
.up-control {
    border-bottom: 1px solid #ccc
}
.up {
    transform: rotate(90deg);
    margin: 0;
    width: 10px;
}
.down {
    transform: rotate(-90deg);
    margin: 0;
    width: 10px;
}
.basket {
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    background: #f90;
    border: none;
    font: 14px/40px Arial,Helvetica,sans-serif;
    padding: 10px;
    margin-left: 2%;
}
.basket__img {
    width: 20px;
    height: 20px;
    fill: #fff;
}
.basket__text {
    min-width: 90px;
    margin: 0 10px;
    color: #fff;
    text-transform: uppercase;
    line-height: 100%
}
@media screen and (max-width: 720px) {
    .product {
        flex-wrap: wrap;
        max-width: 365px;
        min-width: 240px;
        justify-content: center
    }
    .product__description {
        position: static;
    }
    .product__image {
        padding-top: 40px;
        
    }
    .product__retailer-info {
        position: absolute;
        top: 10px;
        left: 10px;
        width: 92.3%;
        justify-content: space-between;
    }
    .product__tags {
        display: none;
    }
    .price__points {
        display: none;
    }
    .buy {
        max-width: 100%;
        width: 100%;
    }
    .form {
        width: 100%;
    }
}
@media screen and (min-width: 1250px) {
    .product__status_big-screen {
        display: block;
    }
    .product__status_default {
        display: none;
    }
    .buy {
        padding-top: 40px;
    }
    .product {
        font-size: 16px;
        line-height: 24px;
    }
}
</style>
