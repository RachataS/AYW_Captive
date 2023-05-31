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
              IP address is {{ip}}
              <hr>
              You're connect this network for {{ uptime }}
              <hr>
              Bytein : {{ bytein }} / Byteout : {{ byteout }}
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
import UserAccountMenu from "@/layouts/main-layout/menus/UserAccountMenu.vue";

export default defineComponent({
  name: "default-dashboard-widget-5",
  components: {},
  data() {
    return {
      username: '',
      ip: '',
      bytein: '',
      byteout: '',
      uptime: '',
    };
  },
  props: {
    className: { type: String, required: false },
  },
  mounted() {
    this.getData();
  },
  methods: {
    async getData() {
      const protocol = window.location.protocol ?? "http:";
      const host = window.location.hostname ?? "localhost";
      const port = window.location.port ?? "5173";

      const html = await ApiService.get(`${protocol}//${host}:${port}`, "status");
      const $ = cheerio.load(html.data.toString());
      const APusername = $(`input[name = "username"]`).val() as string;
      const APip = $(`input[name = "ip"]`).val() as string;
      const APbytein = $(`input[name = "bytes-in-nice"]`).val() as string;
      const APbyteout = $(`input[name = "bytes-out-nice"]`).val() as string;
      const APuptime = $(`input[name = "uptime"]`).val() as string;
      this.username = APusername;
      this.ip = APip;
      this.bytein = APbytein;
      this.byteout = APbyteout;
      this.uptime = APuptime;
    }
  },
  setup() {
    const router = useRouter();
    const store = useAuthStore();
    const username = ref('');
    const ip = ref('');
    const bytein = ref('');
    const uptime = ref('');
    const byteout = ref('');
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
