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
              Hi, {{ username }}
              <hr>
            </h1>
            <h1>
              Connection is successfully!
              <hr>
            </h1>
            <h4 style="font-size: large;">
              IP address is {{ ip }}
              <hr>
              You're connect this network for {{ getUptimeMin(uptime) }}
              <hr>
              Byte in : {{ bytein }} / Byte out : {{ byteout }}
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
import { defineComponent, ref } from "vue";
import { useAuthStore } from "@/stores/auth";
import { useRouter } from "vue-router";
import ApiService from "@/core/services/ApiService";
import * as cheerio from "cheerio";

export default defineComponent({
  name: "default-dashboard-widget-5",
  components: {},
  props: {
    className: { type: String, required: false },
    username:{type: String, required: false},
    ip:{type: String, required: false},
    bytein:{type: String, required: false},
    byteout:{type: String, required: false},
    uptime:{type: String, required: false}
  },
  mounted() {
  },
  methods: {
    getUptimeMin(uptime) {
      const uptimemin = uptime.replace(/(\d+s)$/, "");
      if (uptimemin === "") return "0m";
      return uptimemin;
    },
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
}
);

</script>
