<template>
    <div class="content-background">
        <main class="card products-table">
            <header class="card-header blue-gradient text-left d-flex justify-content-between align-items-center py-2">
                <h3 class="font-weight-bold text-uppercase my-3">Our Products</h3>
                <span class="float-right">
                        <button type="button" class="btn blue lighten-5 btn-rounded text-dark button-add-product"
                                @click="newProduct" aria-label="New product">
                            <mdb-icon icon="plus" class="icon-margin"></mdb-icon>
                        </button>
                    </span>
            </header>

            <section class="card-body p-0">
                <div id="table" class="table-editable">
                    <table class="table text-center mb-0">
                        <thead>
                        <tr>
                            <th class="text-center font-weight-bold products-table-content">Code</th>
                            <th class="text-center font-weight-bold products-table-content">Name</th>
                            <th class="text-center font-weight-bold products-table-content">Before Tax</th>
                            <th class="text-center font-weight-bold products-table-content">After Tax</th>
                            <th class="text-center font-weight-bold products-table-content">Actions</th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr v-for="(product, index) in products" v-bind:key="index">
                            <td class="pt-3-half products-table-content">{{ product.code }}</td>
                            <td class="pt-3-half products-table-content">{{ product.name }}</td>
                            <td class="pt-3-half products-table-content">${{ product.pre_tax }}</td>
                            <td class="pt-3-half products-table-content">${{ getPriceWithTax(product.pre_tax) }}</td>
                            <td class="pt-3-half products-table-content">
                            <span class="table-remove">
                                <button type="button"
                                        class="btn blue lighten-3 btn-rounded btn-sm button-add-to-cart"
                                        @click="addToCart(product)" aria-label="Add to cart">
                                    <mdb-icon icon="cart-plus" class="icon-margin"></mdb-icon>
                                </button>
                                <button type="button" class="btn blue lighten-5 btn-rounded btn-sm button-edit"
                                        @click="editProduct(product)" aria-label="edit">
                                <mdb-icon icon="pen" class="icon-margin"></mdb-icon>
                                </button>
                            </span>
                            </td>
                        </tr>

                        <tr>
                            <td class="pt-3-half font-weight-bold " colspan="2">Subtotal:</td>
                            <td class="pt-3-half font-weight-bold" colspan="2">${{ getCartTotal }}</td>
                            <td>
                            <span class="table-remove">
                                <a to="/checkout">
                                    <button type="button"
                                            class="btn blue darken-4 btn-rounded btn-sm my-0 text-light button-checkout"
                                            @click="modal_cart = true" aria-label="Checkout">
                                        <mdb-icon icon="shopping-cart" class="icon-margin"></mdb-icon>
                                    </button>
                                </a>
                            </span>
                            </td>
                        </tr>
                        </tbody>
                    </table>
                </div>
            </section>
        </main>

        <mdb-container>
            <mdb-modal :show="modal" @close="modal = false">
                <mdb-modal-header class="text-center">
                    <mdb-modal-title tag="h4" bold class="w-100">Add new product</mdb-modal-title>
                </mdb-modal-header>
                <mdb-modal-body class="mx-3 grey-text">
                    <mdb-input label="Product code" class="mb-5" v-model="entry.code"/>
                    <mdb-input label="Product name" class="mb-5" v-model="entry.name"/>
                    <mdb-input label="Base price" class="mb-5" v-model="entry.pre_tax"/>
                </mdb-modal-body>
                <mdb-modal-footer center>
                    <button type="button" class="btn blue darken-4 text-light" @click="saveNewProduct" aria-label="Save">Save
                        <mdb-icon icon="save" class="ml-1"/>
                    </button>
                </mdb-modal-footer>
            </mdb-modal>
        </mdb-container>

        <mdb-container>
            <mdb-modal :show="modal_edit" @close="modal_edit = false">
                <mdb-modal-header class="text-center">
                    <mdb-modal-title tag="h4" bold class="w-100">Edit product</mdb-modal-title>
                </mdb-modal-header>
                <mdb-modal-body class="mx-3 grey-text">
                    <mdb-input label="Product code" class="mb-5" v-model="entry.code"/>
                    <mdb-input label="Product name" class="mb-5" v-model="entry.name"/>
                    <mdb-input label="Base price" class="mb-5" v-model="entry.pre_tax"/>
                </mdb-modal-body>
                <mdb-modal-footer center>
                    <button type="button" class="btn blue darken-4 text-light" @click="saveProduct" aria-label="Save">Save
                        <mdb-icon icon="save" class="ml-1"/>
                    </button>
                </mdb-modal-footer>
            </mdb-modal>
        </mdb-container>

        <mdb-container>
            <mdb-modal :show="modal_cart" @close="modal_cart = false">
                <mdb-modal-header class="text-center">
                    <mdb-modal-title tag="h4" bold class="w-100">Cart</mdb-modal-title>
                </mdb-modal-header>
                <mdb-modal-body class="mx-3 grey-text">
                    <mdb-tbl>
                        <mdb-tbl-head>
                            <tr>
                                <th class="text-center">Code</th>
                                <th class="text-center">Name</th>
                                <th class="text-center">Price</th>
                                <th class="text-center">Quantity</th>
                            </tr>
                        </mdb-tbl-head>
                        <mdb-tbl-body>
                            <tr v-for="(item, index) in cart" v-bind:key="index">
                                <th class="text-center">{{ item.product.code }}</th>
                                <td class="text-center">{{ item.product.name }}</td>
                                <td class="text-center">{{ getPriceWithTax(item.product.pre_tax) }}</td>
                                <td class="text-center">{{ item.quantity }}</td>
                            </tr>
                            <tr>
                                <td class="text-center pt-3-half font-weight-bold " colspan="2">Subtotal:</td>
                                <td class="text-center pt-3-half font-weight-bold" colspan="2">${{ getCartTotal }}</td>
                            </tr>
                        </mdb-tbl-body>
                    </mdb-tbl>
                </mdb-modal-body>
                <mdb-modal-footer center>
                    <button type="button" class="btn blue lighten-5 text-dark" @click="modal_cart = false" aria-label="Close">Close
                        <mdb-icon icon="times" class="ml-1"/>
                    </button>
                    <button type="button" class="btn blue darken-4 text-light" aria-label="Buy">Buy
                        <mdb-icon icon="money-bill-alt" class="ml-1"/>
                    </button>
                </mdb-modal-footer>
            </mdb-modal>
        </mdb-container>
    </div>
</template>

<script>
    import {
        mdbContainer,
        mdbIcon,
        mdbModal,
        mdbModalHeader,
        mdbModalTitle,
        mdbModalBody,
        mdbInput,
        mdbModalFooter,
        mdbTbl,
        mdbTblHead,
        mdbTblBody

    } from 'mdbvue';

    export default {
        name: 'ProductsPage',
        components: {
            mdbContainer,
            mdbModal,
            mdbModalHeader,
            mdbModalBody,
            mdbInput,
            mdbModalFooter,
            mdbIcon,
            mdbModalTitle,
            mdbTbl,
            mdbTblHead,
            mdbTblBody
        },
        data() {
            return {
                entry: {
                    code: '',
                    name: '',
                    pre_tax: 0,
                },
                taxRate: 0.21,
                modal: false,
                modal_edit: false,
                modal_cart: false,
                product_copy: {},
                cart: [],
                products: [
                    {
                        code: '12345',
                        name: 'T-shirt',
                        pre_tax: 27,
                    },
                    {
                        code: '23456',
                        name: 'Jeans',
                        pre_tax: 55,
                    },
                    {
                        code: '34567',
                        name: 'Hoodie',
                        pre_tax: 32,
                    },
                    {
                        code: '45678',
                        name: 'Socks',
                        pre_tax: 4,
                    },
                ]
            }
        },
        methods: {
            getPriceWithTax(n) {
                let post_tax = n / (1 - this.taxRate);
                return post_tax.toFixed(2)
            },
            saveNewProduct() {
                this.products.push({
                    code: this.entry.code,
                    name: this.entry.name,
                    pre_tax: parseInt(this.entry.pre_tax) || 0
                });
                this.modal = false;
                this.resetEntry();
            },
            saveProduct() {
                this.product_copy.code = this.entry.code;
                this.product_copy.name = this.entry.name;
                this.product_copy.pre_tax = parseInt(this.entry.pre_tax) || 0;
                this.modal_edit = false;
                this.resetEntry();
            },
            newProduct() {
                this.modal = true;
                this.resetEntry();
            },
            editProduct(product) {
                this.product_copy = product;
                this.entry = ({code: product.code, name: product.name, pre_tax: product.pre_tax});
                this.modal_edit = true;
            },
            resetEntry() {
                this.entry.code = '';
                this.entry.name = '';
                this.entry.pre_tax = '';
            },
            addToCart(product) {
                for (let i = 0; i < this.cart.length; i++) {
                    if (this.cart[i].product.code == product.code) {
                        this.cart[i].quantity++;
                        return;
                    }
                }
                this.cart.push({product: product, quantity: 1});
            },
        },
        computed: {
            getCartTotal: function () {
                let cartTotal = 0;
                for (let i = 0; i < this.cart.length; i++) {
                    cartTotal += this.cart[i].product.pre_tax * this.cart[i].quantity;
                }
                return this.getPriceWithTax(cartTotal);
            }
        }
    }
</script>

<style scoped>
    .content-background {
        background: #e4e5e9;
        min-height: 100vh;
        height: 100%;
        justify-content: center;
        align-content: center;
        display: grid;
    }

    .products-table {
        max-width: 90vw;
        min-width: 90vw;
    }

    .products-table-content {
        vertical-align: middle;
    }

    @media screen and (min-width: 670px) {
        .button-add-product:before {
            content: 'New Product';
        }

        .button-add-to-cart:before {
            content: 'Add to cart';
        }

        .button-edit:before {
            content: 'Edit';
        }

        .button-checkout:before {
            content: 'Checkout'
        }

        .icon-margin {
            margin-left: 10px;
        }
    }

    @media screen and (max-width: 360px) {
        table, td {
            padding: 0;
        }
    }
</style>

