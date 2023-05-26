<template>
  <div class="card card-flush" :class="className">
    <!--begin::Body-->
    <div
      class="card-body d-flex flex-column justify-content-between mt-9 bgi-no-repeat bgi-size-cover bgi-position-x-center pb-0"
      :style="`background-position: 100% 50%;
                        background-image: url('${getAssetPath(
        '/media/stock/900x600/42.png'
      )}');`">
      <!--begin::Wrapper-->
      <div class="mb-10" id="app">
        <!--begin::Title-->
        <div class="fs-2hx fw-bold text-gray-800 text-center mb-13">
          <span class="me-2">
            <h1 style="font-size: larger;">
              Hi, {{ shUsername }}
              <hr>
            </h1>
              <h1>
                Connection is successfully!
              <hr>
              </h1>
            <h4 style="font-size: large;">
              IP address is 192.168.xx.xx
              <hr>
              You're connect this network for {{ time }}
              <hr>
              Status refresh : None
              <hr>
            </h4>
    
          </span>
        </div>
        <!--end::Title-->

        <!--begin::Action-->

      </div>
      <!--begin::Wrapper-->

    </div>
    <!--end::Body-->
  </div>
</template>
<script lang="ts" type = "module">
import { getAssetPath } from "@/core/helpers/assets";
import { defineComponent } from "vue";
import { username } from "@/views/crafted/authentication/basic-flow/SignIn.vue";
import { useAuthStore } from "@/stores/auth";
import { useRouter } from "vue-router";
export default defineComponent({
  name: "default-dashboard-widget-5",
  components: {},
  data() {
    return {
      shUsername: username,
      time: "55m ",
    };
  },
  props: {
    className: { type: String, required: false },
  },
  setup() {
    const router = useRouter();
    const store = useAuthStore();
    const signOut = () => {
      store.logout();
      router.push({ name: "sign-in" });
    };
    return {
      getAssetPath,
      signOut,
    };
  },
});
</script>
