<template>
  <!--begin::Row-->
  <div class="row g-5 g-xl-10 mb-5 mb-xl-10">
    <div class="col-xxl-3"></div>
    <!--begin::Col-->
    <div class="col-xxl-6">
      <Widget5 className="h-md-100" :username="username" :ip="ip" :bytein="bytein" :byteout="byteout" :uptime="uptime" />
    </div>
    <!--end::Col-->
    <div class="col-xxl-3"></div>
  </div>
</template>

<script lang="ts">
import { getAssetPath } from "@/core/helpers/assets";
import { defineComponent } from "vue";
import Widget1 from "@/components/dashboard-default-widgets/Widget1.vue";
import Widget2 from "@/components/dashboard-default-widgets/Widget2.vue";
import Widget3 from "@/components/dashboard-default-widgets/Widget3.vue";
import Widget4 from "@/components/dashboard-default-widgets/Widget4.vue";
import Widget5 from "@/components/dashboard-default-widgets/Widget5.vue";
import Widget6 from "@/components/dashboard-default-widgets/Widget6.vue";
import Widget7 from "@/components/dashboard-default-widgets/Widget7.vue";
import Widget8 from "@/components/dashboard-default-widgets/Widget8.vue";
import Widget9 from "@/components/dashboard-default-widgets/Widget9.vue";
import Widget10 from "@/components/dashboard-default-widgets/Widget10.vue";
import MixedWidget5 from "@/components/widgets/mixed/Widget5.vue";
import ApiService from "@/core/services/ApiService";
import * as cheerio from "cheerio";
import liff from "@line/liff";
import { userProfile } from "./crafted/authentication/basic-flow/SignIn.vue";

export default defineComponent({
  name: "main-dashboard",
  components: {
    Widget1,
    Widget2,
    Widget3,
    Widget4,
    Widget5,
    Widget6,
    Widget7,
    Widget8,
    Widget9,
    Widget10,
    MixedWidget5,
  },
  data() {
    return {
      username: '',
      ip: '',
      bytein: '',
      byteout: '',
      uptime: '',
    };
  },
  mounted() {
    this.getData();
    this.startAutoRefresh();
    console.log("line info =",userProfile);
  },
  methods: {
    getUptimeMin(uptime) {
      const uptimemin = uptime.replace(/(\d+s)$/, "");
      if (uptimemin === "") return "0m";
      return uptimemin;
    },
    async startAutoRefresh() {
      setInterval(() => {
        this.getData();
      }, 60000);
    },
    async getData() {
      const protocol = window.location.protocol ?? "http:";
      const host = window.location.hostname ?? "localhost";
      const port = window.location.port ?? "5173";

      const html = await ApiService.get(`${protocol}//${host}:${port}`, "apapi/status");
      const $ = cheerio.load(html.data.toString());
      this.username = $(`input[name = "username"]`).val() as string;
      console.log(this.username);
      this.ip = $(`input[name = "ip"]`).val() as string;
      console.log(this.ip);
      this.bytein = $(`input[name = "bytes-in-nice"]`).val() as string;
      this.byteout = $(`input[name = "bytes-out-nice"]`).val() as string;
      this.uptime = $(`input[name = "uptime"]`).val() as string;
    },
  },
  setup() {
    return {
      getAssetPath,
    };
  },
});
</script>
