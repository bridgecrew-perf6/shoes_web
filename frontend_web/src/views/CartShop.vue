<script>
import HeaderShop from "@/components/HeaderShop.vue";
import CartService from "../services/Cart.service";
import toastsVue from "../components/toasts.vue";
import toastsjs from "../assets/js/toasts.js";
import CartItem from "../components/CartItem.vue";
export default {
  data() {
    return {
      carts: [],
      toasts: {
        title: "",
        msg: "",
        type: "",
        duration: 0,
      },
    };
  },
  components: {
    HeaderShop,
    toastsVue,
    CartItem,
  },
  methods: {
    getiduser() {
      const user = JSON.parse(localStorage.getItem("user"));
      return user._id;
    },
    async getcarts() {
      try {
        this.carts = await CartService.get(this.getiduser());
      } catch (error) {
        console.log(error);
      }
    },
    async delcart(index) {
      (this.toasts.title = "Deleted"),
        (this.toasts.msg = "Đã xóa sản phẩm"),
        (this.toasts.type = "error"),
        (this.toasts.duration = 2000);
      this.toastsjs();
      await CartService.delete(this.carts[index]._id);
      this.refeshlistcart();
    },
    async increaseQuantity(index) {
      (this.toasts.title = "Increased"),
        (this.toasts.msg = "Đã tăng số lượng sản phẩm"),
        (this.toasts.type = "success"),
        (this.toasts.duration = 2000);
      this.toastsjs();
      await CartService.update(this.carts[index].quantity, { quantity: this.carts[index].quantity++ });
      this.refeshlistcart();
    },
    async decreaseQuantity(index) {
      if (this.carts[index].quantity === 1) {
        (this.toasts.title = "Deleted"),
          (this.toasts.msg = "Đã xóa sản phẩm"),
          (this.toasts.type = "error"),
          (this.toasts.duration = 500);
        this.toastsjs();
        await CartService.delete(this.carts[index]._id);
        this.refeshlistcart();
        return;
      }

      (this.toasts.title = "Decreased"),
        (this.toasts.msg = "Đã giảm số lượng sản phẩm"),
        (this.toasts.type = "success"),
        (this.toasts.duration = 500);
      this.toastsjs();
      await CartService.update(this.carts[index].quantity, { quantity: this.carts[index].quantity-- });
      this.refeshlistcart();
    },
    toastsjs,
    refeshlistcart() {
      this.getcarts();
    },
    registerproduct() {
      if (this.carts.length > 0) {
        (this.toasts.title = "Success"),
          (this.toasts.msg = "Đã thành toán"),
          (this.toasts.type = "success"),
          (this.toasts.duration = 2000),
          this.toastsjs();
      } else {
        (this.toasts.title = "Failed"),
          (this.toasts.msg = "Bạn chưa có sản phẩm"),
          (this.toasts.type = "error"),
          (this.toasts.duration = 2000),
          this.toastsjs();
      }
    },
    total() {
      var total = 0;
      for (var i in this.carts) {
        total += this.carts[i].price * this.carts[i].quantity;
      }
      return total;
    },
  },
  created() {
    this.refeshlistcart();
  },
};
</script>
<template>
  <HeaderShop></HeaderShop>
  <toastsVue></toastsVue>
  <section class="h-100 h-custom" style="background-color: rgb(234, 232, 232)">
    <div class="container py-5 h-100">
      <div class="row d-flex justify-content-center align-items-center h-100">
        <div class="col-12">
          <div class="card card-registration card-registration-2" style="border-radius: 15px">
            <div class="card-body p-0">
              <div class="row g-0">
                <div class="col-lg-8">
                  <div class="p-5">
                    <div class="d-flex justify-content-between align-items-center mb-5">
                      <h1 class="fw-bold mb-0 text-black">Shopping Cart</h1>
                      <h6 class="mb-0 text-muted">{{ carts.length }} items</h6>
                    </div>
                    <hr class="my-4" />
                    <CartItem
                      :refeshlistcart="refeshlistcart"
                      :carts="carts"
                      @increased:cartIndex="increaseQuantity"
                      @decreased:cartIndex="decreaseQuantity"
                      @deleted:cartIndex="delcart"
                    ></CartItem>
                    <div class="pt-5">
                      <button class="btn btn-secondary mb-0">
                        <router-link to="/" class="text-body">Back to shop</router-link>
                      </button>
                    </div>
                  </div>
                </div>
                <div class="col-lg-4 bg-grey">
                  <div class="p-5">
                    <h5 class="text-uppercase mb-3">Payment method</h5>

                    <div class="mb-4 pb-2">
                      <select class="select">
                        <option value="1">Cash on delivery (COD)</option>
                        <option value="2">Direct payment</option>
                      </select>
                    </div>

                    <div class="mb-5">
                      <h5 class="text-uppercase mb-3">Your name:</h5>
                      <div class="form-outline">
                        <input type="text" id="form3Examplea2" class="form-control form-control-lg" />
                        <label class="form-label" for="form3Examplea2">Please enter your name</label>
                      </div>
                    </div>
                    <div class="mb-5">
                      <h5 class="text-uppercase mb-3">Phone number:</h5>
                      <div class="form-outline">
                        <input type="text" id="form3Examplea2" class="form-control form-control-lg" />
                        <label class="form-label" for="form3Examplea2">Please enter your phone number </label>
                      </div>
                    </div>
                    <div class="mb-5">
                      <h5 class="text-uppercase mb-3">Address:</h5>
                      <div class="form-outline">
                        <input type="text" id="form3Examplea2" class="form-control form-control-lg" />
                        <label class="form-label" for="form3Examplea2">Please enter your address</label>
                      </div>
                    </div>

                    <hr class="my-4" />

                    <div class="d-flex justify-content-between mb-5">
                      <h5 class="text-uppercase">Total price</h5>
                      <h5>{{ total() }}<span>$</span></h5>
                    </div>

                    <button
                      type="button"
                      class="btn btn-dark btn-block btn-lg"
                      data-mdb-ripple-color="dark"
                      @click="registerproduct()"
                    >
                      Place order
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
<style scoped>
@media (min-width: 1025px) {
  .h-custom {
    height: 100%;
  }
}

.card-registration .select-input.form-control[readonly]:not([disabled]) {
  font-size: 1rem;
  line-height: 2.15;
  padding-left: 0.75em;
  padding-right: 0.75em;
}

.card-registration .select-arrow {
  top: 13px;
}

.bg-grey {
  background-color: #eae8e8;
}

@media (min-width: 992px) {
  .card-registration-2 .bg-grey {
    border-top-right-radius: 16px;
    border-bottom-right-radius: 16px;
  }
}

@media (max-width: 991px) {
  .card-registration-2 .bg-grey {
    border-bottom-left-radius: 16px;
    border-bottom-right-radius: 16px;
  }
}
</style>
