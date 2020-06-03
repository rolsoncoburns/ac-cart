<template>
<div class="card card-product product-small">
	<figure class="itemside">
		<div class="img-padding">
            <div class="img-wrap img-sm"><img width="50" :src="item.imageUrl + '?w=50'" ></div>
        </div>
		<figcaption class="align-self-center">
			<h4 class="title"><a :href="'https://www.coburns.com/store/productdetails.aspx?itemId=' + item.itemId">{{item.itemName}}</a></h4>
            <div class="row">
                <div class="price h4 col-6">${{item.price}}</div>
                <div class="form-group col-6 quantity-box">
                <button v-on:click.prevent="decrement" class="decrement">-</button>
                <input type="text" v-on:change.enter="change" v-model="item.quantity" class="addition small form-control">
                <button v-on:click.prevent="increment" class="increment">+</button>
                </div>
            </div>
		</figcaption>
	</figure> 
</div>
</template>
<style scoped>
.card-product {
    width:100%;
}
    .quantity-box {
        position:relative;
    }
    .increment, .decrement {
        position:absolute;
        top:0;
        height:100%;
        opacity:0;
    }
    .decrement {
        left:8px;
    }
    .increment {
        right:8px;
    }
</style>
<script>
export default {
    name : "CartItem",
    props: ["item"],
    methods: {
        decrement(event){
            this.$emit("decItem", this.item);
        },
        increment(event){
            this.$emit("incItem", this.item);
        },
        change(event){
            this.$emit("change", this.item);
        }
    }
}
</script>
