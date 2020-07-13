<template>
  <div class="mini-cart">
      <div class="h5 subhead mini-cart-header">
      <Spinner v-if="loading" />
      <span v-else>{{cart.totalItemCount}}</span> Item(s) in Your Cart
      </div>

    <div class="mini-cart-contents">
      <template v-for="item in cart.items">
        <CartItem @decItem="onDecItem" @incItem="onIncItem" @change="onChange"  :item="item" :key="item.itemId" />
      </template>
    </div>
    
    <div class="mini-cart-footer">
        <div class="h4 price">Subtotal: 
          <Spinner v-if="loading" />
          <span v-else>${{cart.subtotal.toFixed(2)}}</span>
        </div> 
        <div class="mini-cart-footer__actions">
          <a href="https://www.coburns.com/store/checkout.aspx" class="btn btn-beige has-white-color">Proceed to Checkout</a><br/>
          <a href="https://www.coburns.com/store/shopcart.aspx" class="btn btn-link cart-link">View Full Cart <svg class="icon icon-arrow" width="7px"
                    height="10px" viewbox="0 0 7 10" version="1.1" xmlns="http://www.w3.org/2000/svg"
                    xmlns:xlink="http://www.w3.org/1999/xlink">
                    <polyline stroke="currentColor" stroke-width="2" fill="none" fill-rule="evenodd"
                        stroke-linecap="round" stroke-linejoin="round" points="1 9 6 5 1 1"></polyline>
                </svg>
          </a>
        </div>
      </div>
  </div>
</template>

<script>
import '../ac-client-api.js';
import CartItem from './CartItem.vue';
import Spinner from './Spinner.vue';

/*eslint-disable */
//Vue.config.devtools = true;

export default {
  name: 'AcCart',
  components: {CartItem, Spinner},
  data() {
    return { 
      cart : {
        cartId : null,
        items : [],
        subtotal : 99999,
        taxTotal : null,
        totalItemCount : 0,
        discountTotal : null,
        grandTotal : 99999
      },
      loaded: false,
      loading:false,
      updating:false,
      debounce: null
    };
  },
  beforeMount() {
    var self = this;
    
    /*eslint-disable */
    AC.init({ storeDomain : "www.coburns.com"});
    AC.cart.get(x => {
      console.log(x.data);
      self.cart.cartId = x.data.cartId;
      self.cart.items = x.data.items;
      self.cart.subtotal = x.data.subtotal;
      self.cart.taxTotal = x.data.taxTotal;
      self.cart.totalItemCount = x.data.totalItemCount;
      self.cart.discountTotal = x.data.discountTotal;
      self.cart.grandTotal = x.data.grandTotal;

      self.loaded = true;

      console.log(self);
    });
  },
  methods: {
    onDecItem(item){
      if (this.updating)
        return;
      item.quantity--;
      this.update(item);
    },
    onIncItem(item){
      if (this.updating)
        return;
      item.quantity++;
      this.update(item);
    },
    onChange(item){
      this.update(item)
    },
    update(item){
      var self = this;

      if(self.debounce)
        clearTimeout(self.debounce)

      self.loading = true;
      
      /*eslint-disable */
      self.debounce = setTimeout(function() {
        self.updating = true;
        AC.cart.update({
          cartRowId: item.cartRowId,
          quantity : item.quantity
        }, function(res){
          self.cart = res.data;
          self.loading = false;
          self.updating = false;
        })
      }, 400);
    }
  }
}
</script>

