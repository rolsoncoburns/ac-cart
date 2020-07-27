<template>
  <div>
    <ul class="navbar-nav nav-color-white">
      <li class="nav-item">
        <a href="#!" onclick="LC_API.open_chat_window();return false" class="nav-link">
          <svg
            class="icon icon-chat"
            viewBox="0 0 32 32"
            version="1.1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
          >
            <path
              fill="currentColor"
              d="M16,0 C24.836556,0 32,6.39593215 32,14.2857143 C32,22.1754964 24.836556,28.5714286 16,28.5714286 C14.7305159,28.5714286 13.4955631,28.439423 12.3114208,28.1899467 L6.38768161,32 L6.09379802,25.5047862 C2.3822378,22.8886179 0,18.8355596 0,14.2857143 C0,6.39593215 7.163444,0 16,0 Z"
            />
          </svg> Live Chat
        </a>
      </li>
      <li class="nav-item">
        <a href="https://coburns.com/store/myaccount.aspx" class="nav-link">
          <svg
            class="icon icon-person"
            viewBox="0 0 32 32"
            version="1.1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
          >
            <path
              fill="currentColor"
              d="M16,16c4,0,7.3-3.3,7.3-7.3S20,1.5,16,1.5S8.7,4.7,8.7,8.7S12,16,16,16z M16,18.9c-5.3,0-16,2.6-16,7.8v3.9h32v-3.9 C32,21.5,21.3,18.9,16,18.9z"
            />
          </svg>
          <template v-if="!customer">
            Login
          </template>
          <template v-else>
            Hi, {{ customer }}
          </template>
        </a>
      </li>
      <li class="nav-item">
        <a href="/store/shopcart.aspx" class="nav-link" data-toggle="modal" data-target="#MiniCart">
          <svg
            class="icon icon-cart"
            viewBox="0 0 32 32"
            version="1.1"
            xmlns="http://www.w3.org/2000/svg"
            xmlns:xlink="http://www.w3.org/1999/xlink"
          >
            <path
              fill="currentColor"
              d="M9.13426423,25.1428571 C11.0247046,25.1428571 12.5714286,26.6857143 12.5714286,28.5714286 C12.5714286,30.4571429 11.0247046,32 9.13426423,32 C7.24382385,32 5.71428571,30.4571429 5.71428571,28.5714286 C5.71428571,26.6857143 7.24382385,25.1428571 9.13426423,25.1428571 Z M25.1342642,25.1428571 C27.0247046,25.1428571 28.5714286,26.6857143 28.5714286,28.5714286 C28.5714286,30.4571429 27.0247046,32 25.1342642,32 C23.2438238,32 21.7142857,30.4571429 21.7142857,28.5714286 C21.7142857,26.6857143 23.2438238,25.1428571 25.1342642,25.1428571 Z M5.232,0 L6.736,3.2 L30.4,3.2 C31.28,3.2 32,3.92 32,4.8 C32,5.0176 31.95904,5.2352 31.87712,5.428224 L31.808,5.568 L26.08,15.952 C25.568,16.8856471 24.6024637,17.5216609 23.490195,17.5932554 L23.28,17.6 L11.36,17.6 L9.92,20.208 L9.872,20.4 C9.872,20.596 10.00675,20.75525 10.1905,20.792 L10.272,20.8 L28.8,20.8 L28.8,24 L9.6,24 C7.84,24 6.4,22.56 6.4,20.8 C6.4,20.32 6.50579592,19.8635102 6.69723615,19.460758 L6.8,19.264 L8.96,15.344 L3.2,3.2 L0,3.2 L0,0 L5.232,0 Z"
            />
          </svg>
          Cart
          <span id="item_count" v-if="loaded">({{ cart.totalItemCount }})</span>
        </a>
      </li>
    </ul>
    <div
      class="modal fade"
      id="MiniCart"
      tabindex="-1"
      role="dialog"
      aria-labelledby="MiniCart"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <button
              type="button"
              class="close btn btn-default"
              data-dismiss="modal"
              aria-label="Close"
            >
              <span aria-hidden="true">×</span>
            </button>
          </div>
          <div class="modal-body">
            <div class="mini-cart">
              <div class="h5 subhead mini-cart-header">
                <Spinner v-if="loading" />
                <span v-else>{{cart.totalItemCount}}</span> 
                <span v-if="cart.totalItemCount == 1">Item</span><span v-else>Items</span> in Your Cart
              </div>

              <div class="mini-cart-contents">
                <template v-for="item in cart.items">
                  <CartItem
                    @decItem="onDecItem"
                    @incItem="onIncItem"
                    @change="onChange"
                    :item="item"
                    :key="item.itemId"
                  />
                </template>
              </div>

              <div class="mini-cart-footer">
                <div class="h4 price">
                  Subtotal:
                  <Spinner v-if="loading" />
                  <span v-else>${{cart.subtotal.toFixed(2)}}</span>
                </div>
                <div class="mini-cart-footer__actions">
                  <a
                    href="https://www.coburns.com/store/checkout.aspx"
                    class="btn btn-beige has-white-color"
                  >Proceed to Checkout</a>
                  <br />
                  <a
                    href="https://www.coburns.com/store/shopcart.aspx"
                    class="btn btn-link cart-link"
                  >
                    View Full Cart
                    <svg
                      class="icon icon-arrow"
                      width="7px"
                      height="10px"
                      viewBox="0 0 7 10"
                      version="1.1"
                      xmlns="http://www.w3.org/2000/svg"
                      xmlns:xlink="http://www.w3.org/1999/xlink"
                    >
                      <polyline
                        stroke="currentColor"
                        stroke-width="2"
                        fill="none"
                        fill-rule="evenodd"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        points="1 9 6 5 1 1"
                      />
                    </svg>
                  </a>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "../ac-client-api.js";
import CartItem from "./CartItem.vue";
import Spinner from "./Spinner.vue";

/*eslint-disable */
//Vue.config.devtools = true;

export default {
  name: "AcCart",
  components: { CartItem, Spinner },
  data() {
    return {
      cart: {
        cartId: null,
        items: [],
        subtotal: 99999,
        taxTotal: null,
        totalItemCount: 0,
        discountTotal: null,
        grandTotal: 99999
      },
      loaded: false,
      loading: false,
      updating: false,
      debounce: null,
      customer: ""
    };
  },
  mounted() {
    var self = this;

    /*eslint-disable */
    AC.init({ storeDomain: "www.coburns.com" });
    AC.customer.get(function(response) {
      if (response.data) {
        self.customer = response.data.firstName;
      }
    });
    AC.cart.get(x => {
      //console.log(x.data);
      self.cart.cartId = x.data.cartId;
      self.cart.items = x.data.items;
      self.cart.subtotal = x.data.subtotal;
      self.cart.taxTotal = x.data.taxTotal;
      self.cart.totalItemCount = x.data.totalItemCount;
      self.cart.discountTotal = x.data.discountTotal;
      self.cart.grandTotal = x.data.grandTotal;

      self.loaded = true;

      //console.log(self);
    });
  },
  methods: {
    onDecItem(item) {
      if (this.updating) return;
      item.quantity--;
      this.update(item);
    },
    onIncItem(item) {
      if (this.updating) return;
      item.quantity++;
      this.update(item);
    },
    onChange(item) {
      this.update(item);
    },
    update(item) {
      var self = this;

      if (self.debounce) clearTimeout(self.debounce);

      self.loading = true;

      /*eslint-disable */
      self.debounce = setTimeout(function() {
        self.updating = true;
        AC.cart.update(
          {
            cartRowId: item.cartRowId,
            quantity: item.quantity
          },
          function(res) {
            self.cart = res.data;
            self.loading = false;
            self.updating = false;
          }
        );
      }, 400);
    }
  }
};
</script>

