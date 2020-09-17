<template>
    <div>
        <div class="card products-table">
            <div class="card-header blue-gradient text-left d-flex justify-content-between align-items-center py-2">
                <h3 class="font-weight-bold text-uppercase my-3">Products</h3>
                <span class="float-right">
                        <mdb-btn class="blue lighten-5 text-dark" @click.native="newProduct">New Product<mdb-icon
                                icon="plus" class="ml-2"></mdb-icon></mdb-btn>
                    </span>
            </div>

            <div class="card-body p-0">
                <div id="table" class="table-editable">

                    <table class="table table-bordered table-responsive-md table-striped text-center mb-0">
                        <thead>
                        <tr>
                            <th class="text-center font-weight-bold">Code</th>
                            <th class="text-center font-weight-bold">Name</th>
                            <th class="text-center font-weight-bold">Before Tax</th>
                            <th class="text-center font-weight-bold">After Tax</th>
                            <th class="text-center font-weight-bold">Actions</th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr v-for="(product, index) in products" v-bind:key="index">
                            <td class="pt-3-half">{{ product.code }}</td>
                            <td class="pt-3-half">{{ product.name }}</td>
                            <td class="pt-3-half">{{ product.pre_tax }}</td>
                            <td class="pt-3-half">{{ getPriceWithTax(product.pre_tax) }}</td>
                            <td>
                            <span class="table-remove">
                                <button type="button"
                                        class="btn blue lighten-3 btn-rounded btn-sm my-0" @click="addToCart(product)">Add to cart</button>
                                <button type="button" class="btn blue lighten-5 btn-rounded btn-sm my-0"
                                        @click="editProduct(product)">Edit</button>
                            </span>
                            </td>
                        </tr>

                        <tr>
                            <td class="pt-3-half font-weight-bold " colspan="2">Subtotal:</td>
                            <td class="pt-3-half font-weight-bold" colspan="2">${{ getCartTotal }}</td>
                            <td>
                            <span class="table-remove">
                                <a to="/checkout">
                                    <button type="button" class="btn blue darken-4 btn-rounded btn-sm my-0 text-light">Checkout
                                        <mdb-icon icon="shopping-cart" class="ml-2"></mdb-icon>
                                    </button>
                                </a>
                            </span>
                            </td>
                        </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
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
                    <mdb-btn @click.native="saveNewProduct" class="blue darken-4">Save
                        <mdb-icon icon="save" class="ml-1"/>
                    </mdb-btn>
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
                    <mdb-btn @click.native="saveProduct" class="blue darken-4">Save
                        <mdb-icon icon="save" class="ml-1"/>
                    </mdb-btn>
                </mdb-modal-footer>
            </mdb-modal>
        </mdb-container>
    </div>
</template>

<script>
    import {
        mdbContainer,
        mdbIcon,
        mdbBtn,
        mdbModal,
        mdbModalHeader,
        mdbModalTitle,
        mdbModalBody,
        mdbInput,
        mdbModalFooter
    } from 'mdbvue';

    export default {
        name: 'ProductsPage',
        components: {
            mdbContainer,
            mdbBtn,
            mdbModal,
            mdbModalHeader,
            mdbModalBody,
            mdbInput,
            mdbModalFooter,
            mdbIcon,
            mdbModalTitle
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
                this.cart.push(product);
            }
        },
        computed: {
            getCartTotal: function () {
                let cartTotal = 0;
                for (let i = 0; i < this.cart.length; i++) {
                    cartTotal += this.cart[i].pre_tax;
                }
                return this.getPriceWithTax(cartTotal);
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    /*h3 {*/
    /*    margin: 40px 0 0;*/
    /*}*/

    /*ul {*/
    /*    list-style-type: none;*/
    /*    padding: 0;*/
    /*}*/

    /*li {*/
    /*    display: inline-block;*/
    /*    margin: 0 10px;*/
    /*}*/

    /*a {*/
    /*    color: #42b983;*/
    /*}*/

    /*.pt-3-half {*/
    /*    padding-top: 1.4rem;*/
    /*}*/

    .products-table {
        max-width: 90vw;
        margin: 0 auto;
    }
</style>

