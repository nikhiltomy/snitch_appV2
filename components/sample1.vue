<template lang="">
  <div>
    <div class="options" v-for="option in options.Size" ::key="option">
      <button @click.prevent="" class="size-btn">{{ option.value }}</button>
    </div>
    <div class="cart">
      <button class="add-to-cart" @click.prevent="addingToCart({ fproduct, quantity: parseInt(qty) })">
        <span
          class="sf-icon color-white size-sm"
          style="display:inline-block;vertical-align:middle"
        >
          <svg
            class="sf-icon-path"
            viewBox="0 0 24 24"
            preserveAspectRatio="none"
          >
            <path
              d="M21.561 6.874a2.026 2.026 0 00-1.579-.779H7.16l-.4-1.558A2.024 2.024 0 004.801 3H2.653A.656.656 0 002 3.653c0 .358.294.653.653.653h2.148c.316 0 .59.21.673.527l2.57 10.234a2.024 2.024 0 001.958 1.537h8.4c.927 0 1.749-.632 1.96-1.537L21.94 8.58a1.931 1.931 0 00-.38-1.707zm-.904 1.41l-1.58 6.487a.695.695 0 01-.673.526h-8.402a.695.695 0 01-.674-.526L7.496 7.422h12.487a.7.7 0 01.548.274.662.662 0 01.126.589zM10.445 17.445c-1.2 0-2.19.99-2.19 2.19s.99 2.189 2.19 2.189a2.2 2.2 0 002.19-2.189c0-1.2-.99-2.19-2.19-2.19zm0 3.053a.854.854 0 01-.864-.864c0-.484.38-.863.864-.863s.863.379.863.863c0 .464-.4.864-.863.864zM17.688 17.445c-1.2 0-2.19.99-2.19 2.19s.99 2.189 2.19 2.189 2.19-.99 2.19-2.19c-.021-1.2-.99-2.19-2.19-2.19zm0 3.053a.854.854 0 01-.864-.864c0-.484.38-.863.864-.863.485 0 .864.379.864.863 0 .464-.4.864-.864.864z"
              fill="var(--icon-color)"
            />
          </svg>
        </span>
        <span style="display:inline-block;vertical-align:middle"
          >Add to cart</span
        >
      </button>
    </div>
  </div>
</template>
<script>
import {
  useProduct,
  useCart,
  productGetters,
  cartGetters,
} from "@vue-storefront/shopify";
import { onSSR } from "@vue-storefront/core";
import { ref, computed, watch, onMounted } from "@vue/composition-api";

export default {
  props: ["product"],
  data() {
    return {
      options: productGetters.getAttributes(this.product),
    };
  },
  methods:{
    async addingToCart(Productdata) {
       console.log(Productdata);
       await this.addItem(Productdata).then(() => {
        this.qty = 1;
      });;
    },
  },
  mounted(){
 console.log(this.options)
  },

  setup(props,context){
     /* const id  = {slug_1: props.product.id}; */
      const qty = ref(1);
      const slug  = props.product.handle;
      const productTitle = props.product.title;
      const variant = {Size: 'M'};
      const attb= 'L';
      const { cart, removeItem, updateItemQty, load: loadCart } = useCart();
      const checkoutURL = computed(() => cartGetters.getcheckoutURL(cart.value));
      const { addItem, loading } = useCart();
      const { loading: productloading, products, search } = useProduct(
      "products"
    );
    const fproduct = computed(
      () =>
        productGetters.getFiltered(products.value)[0]
    );
    const configuration = computed(() => {
      return productGetters.getSelectedVariant(
        products.value,
        variant
      );
    });

    
 
    

 onMounted(() => {
      console.log(slug)
      console.log(products)
      // console.log("Hi mounted");
      // console.log(fproduct);
      
    });


   onSSR(async () => {
      await search({ slug, selectedOptions: {Size: 'M', Color: 'Red'} }).then(() => {
        if (productTitle.value === "Product's name") {
          return context.root.error({
            statusCode: 404,
            message: "This product could not be found",
          });
        }
      });
    });
    return{
      products,
      productloading,
      configuration,
      addItem,
      loading,
      qty,
      checkoutURL,
      fproduct
    }
  }
};
</script>
<style scoped>
.options {
  display: inline-block;
  padding: 20px 0px 0px;
}
.size-btn {
  border-radius: 24px;
  border: 1px solid #cecece;
  padding: 4px 10px;
  margin: 0 2px;
  font-size: 16px;
  background-color: white;
}
.add-to-cart {
  display: block;
  text-align: center;
  border-radius: 5px;
  border: 1px solid #000;
  background: #000;
  color: #fff;
  margin: 10px;
}
.add-to-cart :hover {
  display: block;
  color: white;
}
.cart {
  display: flex;
  justify-content: center;
}
</style>
